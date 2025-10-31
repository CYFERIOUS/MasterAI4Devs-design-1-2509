# Technical Architecture: LTI-Based Assessment Platform

## 1. Introduction

This document outlines the proposed technical architecture and technology stack for building the LTI-based skills assessment platform, as described in the `User_Story.md`. The system is designed to integrate with Applicant Tracking Systems (ATS) to streamline the pre-employment assessment process.

The architecture consists of two primary components:
1.  **The Assessment Tool (LTI Tool Provider):** A web application that hosts, delivers, and grades the skills assessments.
2.  **The ATS (LTI Tool Consumer/Platform):** The system used by recruiters. For this document, we assume we are building an ATS with LTI capabilities, or at least the necessary modules to integrate with one.

The communication between these two components will be secured and standardized using the **LTI 1.3 and LTI Advantage** specifications.

## 2. System Architecture Overview

The workflow is initiated from the ATS.

1.  **Launch:** A recruiter triggers an assessment for a candidate within the ATS. The ATS initiates an LTI 1.3 launch request, opening the Assessment Tool in a new tab or iframe. Contextual information (candidate ID, recruiter ID, assessment ID) is passed securely in a JSON Web Token (JWT).
2.  **Assessment:** The candidate completes the assessment in the Assessment Tool's interface.
3.  **Grade Sync:** Upon completion, the Assessment Tool uses the LTI Advantage Assignment and Grade Services (AGS) to securely pass the score and results back to the ATS.
4.  **Results Display:** The ATS receives the grade and associates it with the candidate's profile, making it visible to the recruiter.

## 3. Technology Stack

This stack is chosen for its robustness, scalability, and strong community support.

### 3.1. Backend (Assessment Tool & ATS)

*   **Programming Language:** **Python 3.11+**
*   **Framework:** **FastAPI**
    *   *Why:* High-performance, asynchronous, automatic data validation and API documentation (with OpenAPI), and a modern developer experience. It is well-suited for building RESTful APIs.
*   **LTI 1.3 Library:** **pylti1.3**
    *   *Why:* A dedicated library to handle the complexities of the LTI 1.3 security framework (OIDC, OAuth2, JWTs), allowing developers to focus on business logic rather than protocol implementation.
*   **Database ORM:** **SQLAlchemy**
    *   *Why:* A powerful and flexible Object-Relational Mapper that provides a robust way to interact with the database using Python objects.
*   **Data Validation:** **Pydantic**
    *   *Why:* Comes built-in with FastAPI and provides runtime type checking and data validation, which is critical for secure and reliable APIs.

### 3.2. Frontend (Assessment Tool & ATS UI)

*   **Framework:** **React 18+** (with TypeScript)
    *   *Why:* A popular, component-based library for building interactive and dynamic user interfaces. TypeScript adds static typing for improved code quality and maintainability.
*   **UI Component Library:** **Material-UI (MUI)**
    *   *Why:* Provides a comprehensive suite of pre-built, accessible, and customizable React components that follow Material Design principles, accelerating UI development.
*   **State Management:** **React Query (TanStack Query)**
    *   *Why:* Simplifies server-state management by handling data fetching, caching, and synchronization, reducing boilerplate and improving performance.
*   **Styling:** **Styled-components** or **Emotion**
    *   *Why:* Allows for CSS-in-JS, enabling scoped styles at the component level, which improves modularity and avoids style conflicts.

### 3.3. Database

*   **Database System:** **PostgreSQL 15+**
    *   *Why:* A powerful, open-source, and highly reliable relational database with strong support for complex queries, JSON data types, and full-text search.

### 3.4. Deployment & Infrastructure

*   **Containerization:** **Docker**
    *   *Why:* To create consistent and reproducible environments for development, testing, and production. The application will be packaged into Docker containers.
*   **Cloud Provider:** **Amazon Web Services (AWS)** (or equivalent like Google Cloud, Azure)
*   **Compute:** **AWS Elastic Container Service (ECS) with Fargate**
    *   *Why:* A serverless container orchestration service that simplifies running and scaling the application without managing underlying EC2 instances.
*   **Database Hosting:** **AWS RDS for PostgreSQL**
    *   *Why:* A managed database service that handles backups, patching, and scaling, reducing operational overhead.
*   **CI/CD:** **GitHub Actions**
    *   *Why:* To automate the build, testing, and deployment pipeline directly from the source code repository.

## 4. Security Considerations

*   **Authentication & Authorization:** The LTI 1.3 protocol will handle the primary authentication flow between the ATS and the Assessment Tool, using OAuth2 and OpenID Connect for secure, trusted communication.
*   **Data in Transit:** All communication will be over HTTPS (TLS 1.2+).
*   **Data at Rest:** Sensitive data in the database will be encrypted.
*   **Web Security:** The application will be built following OWASP Top 10 best practices to prevent common vulnerabilities like SQL injection, Cross-Site Scripting (XSS), etc.
