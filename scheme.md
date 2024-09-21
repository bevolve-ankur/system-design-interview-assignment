# Postgres SQL

```

-- Users
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL,
    organization_id INTEGER REFERENCES organizations(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Organizations
CREATE TABLE organizations (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    industry VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Frameworks
CREATE TABLE frameworks (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    version VARCHAR(50) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Questions
CREATE TABLE questions (
    id SERIAL PRIMARY KEY,
    text TEXT NOT NULL,
    type VARCHAR(50) NOT NULL,
    options JSONB,
    framework_id INTEGER REFERENCES frameworks(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Assessments
CREATE TABLE assessments (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    organization_id INTEGER REFERENCES organizations(id),
    framework_id INTEGER REFERENCES frameworks(id),
    status VARCHAR(50) NOT NULL,
    version INTEGER NOT NULL DEFAULT 1, -- Versioning
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Sections
CREATE TABLE sections (
    id SERIAL PRIMARY KEY,
    assessment_id INTEGER REFERENCES assessments(id),
    title VARCHAR(255) NOT NULL,
    order_index INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Assessment Questions
CREATE TABLE assessment_questions (
    id SERIAL PRIMARY KEY,
    question_id INTEGER REFERENCES questions(id),
    section_id INTEGER REFERENCES sections(id),
    order_index INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Responses
CREATE TABLE responses (
    id SERIAL PRIMARY KEY,
    assessment_question_id INTEGER REFERENCES assessment_questions(id),
    user_id INTEGER REFERENCES users(id),
    value TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Assessment Rights
CREATE TABLE assessment_rights (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    assessment_id INTEGER REFERENCES assessments(id),
    can_edit BOOLEAN NOT NULL DEFAULT FALSE,
    can_view BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Section Rights
CREATE TABLE section_rights (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    section_id INTEGER REFERENCES sections(id),
    can_edit BOOLEAN NOT NULL DEFAULT FALSE,
    can_view BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

```

# Dynamo DB

```

// Assessment Response Item
{
  PK: "ASSESSMENT#<assessment_id>",
  SK: "RESPONSE#<section_id>#<question_id>#<user_id>",
  Data: {
    value: string,
    created_at: string,
    updated_at: string,
    moderation_status: "pending" | "approved" | "rejected",
    moderation_feedback: string
  },
  GSI1PK: "USER#<user_id>",
  GSI1SK: "RESPONSE#<assessment_id>#<question_id>"
}

// Moderation Item
{
  PK: "MODERATION#<moderation_id>",
  SK: "ASSESSMENT#<assessment_id>#<section_id>#QUESTION#<question_id>#USER#<user_id>",
  Data: {
    status: "pending" | "approved" | "rejected",
    feedback: string,
    moderator_id: string,
    moderated_at: string
  },
  GSI1PK: "ASSESSMENT#<assessment_id>",
  GSI1SK: "MODERATION#<moderation_id>"
}

// PR-like Discussion Item
{
  PK: "DISCUSSION#<discussion_id>",
  SK: "ASSESSMENT#<assessment_id>#<section_id>#QUESTION#<question_id>",
  Data: {
    status: "open" | "closed",
    title: string,
    description: string,
    created_by: string,
    created_at: string,
    updated_at: string
  },
  GSI1PK: "ASSESSMENT#<assessment_id>",
  GSI1SK: "DISCUSSION#<discussion_id>"
}

// Discussion Comment Item
{
  PK: "DISCUSSION#<discussion_id>",
  SK: "COMMENT#<timestamp>",
  Data: {
    user_id: string,
    content: string,
    created_at: string
  },
  GSI1PK: "USER#<user_id>",
  GSI1SK: "COMMENT#<discussion_id>#<timestamp>"
}

```