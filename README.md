# BESSANEPROSSERVICE-ECOLE
Plateforme educative pour l'enseignement à l'ecole elementaire au senegal pour la gestion de l'ecole
```sql
-- Script SQL pour créer la base de données de la plateforme éducative
-- Utilise PostgreSQL

-- Création de la base de données
CREATE DATABASE plateforme_educative_senegal;
\c plateforme_educative_senegal;

-- Table school
CREATE TABLE school (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    address TEXT,
    phone VARCHAR(20),
    email VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table level
CREATE TABLE level (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL, -- CP, CE1, etc.
    school_id INTEGER REFERENCES school(id) ON DELETE CASCADE
);

-- Table classroom
CREATE TABLE classroom (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    level_id INTEGER REFERENCES level(id) ON DELETE CASCADE,
    school_id INTEGER REFERENCES school(id) ON DELETE CASCADE,
    teacher_id INTEGER, -- Référence à user.id, ajoutée après
    capacity INTEGER
);

-- Table user
CREATE TABLE "user" (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL CHECK (role IN ('admin', 'directeur', 'enseignant', 'parent', 'eleve')),
    phone VARCHAR(20),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table student
CREATE TABLE student (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    birth_date DATE,
    gender VARCHAR(10) CHECK (gender IN ('M', 'F')),
    admission_date DATE,
    status VARCHAR(20) DEFAULT 'active'
);

-- Table parent
CREATE TABLE parent (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    relationship VARCHAR(50), -- père, mère, tuteur
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE
);

-- Table teacher
CREATE TABLE teacher (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    hire_date DATE,
    subjects TEXT -- Liste des matières, ou référence à subject
);

-- Ajout de la référence teacher_id dans classroom
ALTER TABLE classroom ADD CONSTRAINT fk_classroom_teacher FOREIGN KEY (teacher_id) REFERENCES teacher(id);

-- Table enrollment
CREATE TABLE enrollment (
    id SERIAL PRIMARY KEY,
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE,
    classroom_id INTEGER REFERENCES classroom(id) ON DELETE CASCADE,
    school_year VARCHAR(20), -- e.g., 2024-2025
    start_date DATE,
    end_date DATE,
    status VARCHAR(20) DEFAULT 'active'
);

-- Table attendance
CREATE TABLE attendance (
    id SERIAL PRIMARY KEY,
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE,
    classroom_id INTEGER REFERENCES classroom(id) ON DELETE CASCADE,
    date DATE NOT NULL,
    status VARCHAR(20) CHECK (status IN ('present', 'absent', 'retard')),
    reason TEXT
);

-- Table subject
CREATE TABLE subject (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    level_id INTEGER REFERENCES level(id) ON DELETE CASCADE,
    school_id INTEGER REFERENCES school(id) ON DELETE CASCADE
);

-- Table curriculum
CREATE TABLE curriculum (
    id SERIAL PRIMARY KEY,
    subject_id INTEGER REFERENCES subject(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    week INTEGER, -- Numéro de semaine
    created_by INTEGER REFERENCES "user"(id)
);

-- Table resource
CREATE TABLE resource (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    type VARCHAR(50) CHECK (type IN ('pdf', 'video', 'image', 'link', 'document')),
    url TEXT,
    subject_id INTEGER REFERENCES subject(id) ON DELETE CASCADE,
    classroom_id INTEGER REFERENCES classroom(id) ON DELETE CASCADE,
    uploaded_by INTEGER REFERENCES "user"(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table announcement
CREATE TABLE announcement (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    content TEXT,
    school_id INTEGER REFERENCES school(id) ON DELETE CASCADE,
    created_by INTEGER REFERENCES "user"(id),
    target_role VARCHAR(50) CHECK (target_role IN ('enseignant', 'parents', 'eleves', 'tous')),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table message
CREATE TABLE message (
    id SERIAL PRIMARY KEY,
    sender_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    recipient_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    subject VARCHAR(255),
    body TEXT,
    sent_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    read_at TIMESTAMP
);

-- Table timetable
CREATE TABLE timetable (
    id SERIAL PRIMARY KEY,
    classroom_id INTEGER REFERENCES classroom(id) ON DELETE CASCADE,
    subject_id INTEGER REFERENCES subject(id) ON DELETE CASCADE,
    teacher_id INTEGER REFERENCES teacher(id) ON DELETE CASCADE,
    day_of_week INTEGER CHECK (day_of_week BETWEEN 1 AND 7), -- 1=Lundi, 7=Dimanche
    start_time TIME,
    end_time TIME
);

-- Table competency
CREATE TABLE competency (
    id SERIAL PRIMARY KEY,
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE,
    subject_id INTEGER REFERENCES subject(id) ON DELETE CASCADE,
    description TEXT,
    level VARCHAR(20) CHECK (level IN ('acquis', 'en_cours', 'non_acquis')),
    evaluation_date DATE
);

-- Index pour optimiser les requêtes
CREATE INDEX idx_student_user ON student(user_id);
CREATE INDEX idx_parent_user ON parent(user_id);
CREATE INDEX idx_teacher_user ON teacher(user_id);
CREATE INDEX idx_enrollment_student ON enrollment(student_id);
CREATE INDEX idx_enrollment_classroom ON enrollment(classroom_id);
CREATE INDEX idx_attendance_student ON attendance(student_id);
CREATE INDEX idx_attendance_date ON attendance(date);
CREATE INDEX idx_resource_subject ON resource(subject_id);
CREATE INDEX idx_resource_classroom ON resource(classroom_id);
CREATE INDEX idx_message_sender ON message(sender_id);
CREATE INDEX idx_message_recipient ON message(recipient_id);
CREATE INDEX idx_timetable_classroom ON timetable(classroom_id);
CREATE INDEX idx_competency_student ON competency(student_id);
``````sql
-- Script SQL pour créer la base de données de la plateforme éducative
-- Utilise PostgreSQL

-- Création de la base de données
CREATE DATABASE plateforme_educative_senegal;
\c plateforme_educative_senegal;

-- Table school
CREATE TABLE school (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    address TEXT,
    phone VARCHAR(20),
    email VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table level
CREATE TABLE level (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL, -- CP, CE1, etc.
    school_id INTEGER REFERENCES school(id) ON DELETE CASCADE
);

-- Table classroom
CREATE TABLE classroom (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    level_id INTEGER REFERENCES level(id) ON DELETE CASCADE,
    school_id INTEGER REFERENCES school(id) ON DELETE CASCADE,
    teacher_id INTEGER, -- Référence à user.id, ajoutée après
    capacity INTEGER
);

-- Table user
CREATE TABLE "user" (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL CHECK (role IN ('admin', 'directeur', 'enseignant', 'parent', 'eleve')),
    phone VARCHAR(20),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table student
CREATE TABLE student (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    birth_date DATE,
    gender VARCHAR(10) CHECK (gender IN ('M', 'F')),
    admission_date DATE,
    status VARCHAR(20) DEFAULT 'active'
);

-- Table parent
CREATE TABLE parent (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    relationship VARCHAR(50), -- père, mère, tuteur
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE
);

-- Table teacher
CREATE TABLE teacher (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    hire_date DATE,
    subjects TEXT -- Liste des matières, ou référence à subject
);

-- Ajout de la référence teacher_id dans classroom
ALTER TABLE classroom ADD CONSTRAINT fk_classroom_teacher FOREIGN KEY (teacher_id) REFERENCES teacher(id);

-- Table enrollment
CREATE TABLE enrollment (
    id SERIAL PRIMARY KEY,
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE,
    classroom_id INTEGER REFERENCES classroom(id) ON DELETE CASCADE,
    school_year VARCHAR(20), -- e.g., 2024-2025
    start_date DATE,
    end_date DATE,
    status VARCHAR(20) DEFAULT 'active'
);

-- Table attendance
CREATE TABLE attendance (
    id SERIAL PRIMARY KEY,
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE,
    classroom_id INTEGER REFERENCES classroom(id) ON DELETE CASCADE,
    date DATE NOT NULL,
    status VARCHAR(20) CHECK (status IN ('present', 'absent', 'retard')),
    reason TEXT
);

-- Table subject
CREATE TABLE subject (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    level_id INTEGER REFERENCES level(id) ON DELETE CASCADE,
    school_id INTEGER REFERENCES school(id) ON DELETE CASCADE
);

-- Table curriculum
CREATE TABLE curriculum (
    id SERIAL PRIMARY KEY,
    subject_id INTEGER REFERENCES subject(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    week INTEGER, -- Numéro de semaine
    created_by INTEGER REFERENCES "user"(id)
);

-- Table resource
CREATE TABLE resource (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    type VARCHAR(50) CHECK (type IN ('pdf', 'video', 'image', 'link', 'document')),
    url TEXT,
    subject_id INTEGER REFERENCES subject(id) ON DELETE CASCADE,
    classroom_id INTEGER REFERENCES classroom(id) ON DELETE CASCADE,
    uploaded_by INTEGER REFERENCES "user"(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table announcement
CREATE TABLE announcement (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    content TEXT,
    school_id INTEGER REFERENCES school(id) ON DELETE CASCADE,
    created_by INTEGER REFERENCES "user"(id),
    target_role VARCHAR(50) CHECK (target_role IN ('enseignant', 'parents', 'eleves', 'tous')),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table message
CREATE TABLE message (
    id SERIAL PRIMARY KEY,
    sender_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    recipient_id INTEGER REFERENCES "user"(id) ON DELETE CASCADE,
    subject VARCHAR(255),
    body TEXT,
    sent_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    read_at TIMESTAMP
);

-- Table timetable
CREATE TABLE timetable (
    id SERIAL PRIMARY KEY,
    classroom_id INTEGER REFERENCES classroom(id) ON DELETE CASCADE,
    subject_id INTEGER REFERENCES subject(id) ON DELETE CASCADE,
    teacher_id INTEGER REFERENCES teacher(id) ON DELETE CASCADE,
    day_of_week INTEGER CHECK (day_of_week BETWEEN 1 AND 7), -- 1=Lundi, 7=Dimanche
    start_time TIME,
    end_time TIME
);

-- Table competency
CREATE TABLE competency (
    id SERIAL PRIMARY KEY,
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE,
    subject_id INTEGER REFERENCES subject(id) ON DELETE CASCADE,
    description TEXT,
    level VARCHAR(20) CHECK (level IN ('acquis', 'en_cours', 'non_acquis')),
    evaluation_date DATE
);

-- Index pour optimiser les requêtes
CREATE INDEX idx_student_user ON student(user_id);
CREATE INDEX idx_parent_user ON parent(user_id);
CREATE INDEX idx_teacher_user ON teacher(user_id);
CREATE INDEX idx_enrollment_student ON enrollment(student_id);
CREATE INDEX idx_enrollment_classroom ON enrollment(classroom_id);
CREATE INDEX idx_attendance_student ON attendance(student_id);
CREATE INDEX idx_attendance_date ON attendance(date);
CREATE INDEX idx_resource_subject ON resource(subject_id);
CREATE INDEX idx_resource_classroom ON resource(classroom_id);
CREATE INDEX idx_message_sender ON message(sender_id);
CREATE INDEX idx_message_recipient ON message(recipient_id);
CREATE INDEX idx_timetable_classroom ON timetable(classroom_id);
CREATE INDEX idx_competency_student ON competency(student_id);
```