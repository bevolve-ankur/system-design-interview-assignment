# Postgres SQL

```

-- Users
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL,  -- roles can include 'admin', 'reviewer', 'catalyst', etc.
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
    can_review BOOLEAN NOT NULL DEFAULT FALSE, -- New field for review rights
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

-- Catalyst Team Rights
CREATE TABLE catalyst_rights (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) UNIQUE,  -- One user can be part of the Catalyst team
    can_view_all_organizations BOOLEAN NOT NULL DEFAULT TRUE,  -- Ability to view all organization data
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Questionnaire Categories
CREATE TABLE questionnaire_categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Questionnaires
CREATE TABLE questionnaires (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    category_id INTEGER REFERENCES questionnaire_categories(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Questions
CREATE TABLE questionnaire_questions (
    id SERIAL PRIMARY KEY,
    questionnaire_id INTEGER REFERENCES questionnaires(id),
    text TEXT NOT NULL,
    type VARCHAR(50) NOT NULL,  -- e.g., 'multiple-choice', 'text', 'rating'
    options JSONB,  -- Options for multiple-choice questions
    order_index INTEGER NOT NULL,  -- Order of the question in the questionnaire
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Question Templates
CREATE TABLE question_templates (
    id SERIAL PRIMARY KEY,
    text TEXT NOT NULL,
    type VARCHAR(50) NOT NULL,
    options JSONB,  -- Options for multiple-choice templates
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Questionnaire Versions
CREATE TABLE questionnaire_versions (
    id SERIAL PRIMARY KEY,
    questionnaire_id INTEGER REFERENCES questionnaires(id),
    version INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    change_log TEXT  -- Description of changes made in this version
);

-- Questionnaire Rights
CREATE TABLE questionnaire_rights (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    questionnaire_id INTEGER REFERENCES questionnaires(id),
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

// Assessment Response Audit Item
{
  PK: "ASSESSMENT_AUDIT#<assessment_id>",
  SK: "RESPONSE_AUDIT#<section_id>#<question_id>#<user_id>#<timestamp>",
  Data: {
    action: "CREATE" | "UPDATE" | "DELETE",
    previous_value: {
      value: string,
      created_at: string,
      updated_at: string,
      moderation_status: "pending" | "approved" | "rejected",
      moderation_feedback: string
    },
    changed_by: "<user_id>",
    changed_at: "<timestamp>"
  }
}

// Moderation Audit Item
{
  PK: "MODERATION_AUDIT#<moderation_id>",
  SK: "ASSESSMENT_AUDIT#<assessment_id>#<section_id>#QUESTION_AUDIT#<question_id>#USER_AUDIT#<user_id>#<timestamp>",
  Data: {
    action: "CREATE" | "UPDATE" | "DELETE",
    previous_value: {
      status: "pending" | "approved" | "rejected",
      feedback: string,
      moderator_id: string,
      moderated_at: string
    },
    changed_by: "<user_id>",
    changed_at: "<timestamp>"
  }
}

// PR-like Discussion Audit Item
{
  PK: "DISCUSSION_AUDIT#<discussion_id>",
  SK: "ASSESSMENT_AUDIT#<assessment_id>#<section_id>#QUESTION_AUDIT#<question_id>#<timestamp>",
  Data: {
    action: "CREATE" | "UPDATE" | "DELETE",
    previous_value: {
      status: "open" | "closed",
      title: string,
      description: string,
      created_by: string,
      created_at: string,
      updated_at: string
    },
    changed_by: "<user_id>",
    changed_at: "<timestamp>"
  }
}

// Discussion Comment Audit Item
{
  PK: "DISCUSSION_AUDIT#<discussion_id>",
  SK: "COMMENT_AUDIT#<timestamp>",
  Data: {
    action: "CREATE" | "UPDATE" | "DELETE",
    previous_value: {
      user_id: string,
      content: string,
      created_at: string
    },
    changed_by: "<user_id>",
    changed_at: "<timestamp>"
  }
}

```