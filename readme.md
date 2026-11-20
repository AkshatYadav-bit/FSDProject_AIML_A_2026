# FSDProject_AIML_A_2026

**Project ID:** Project-ID-009\
**Project Title:** Chat Application

**Team Members:** - **Akshat Yadav** --- CSE-AIML A --- 2400321530022 -
**Aditya Mishra** --- CSE-AIML A --- 2400321530019

------------------------------------------------------------------------

**\# Real-Time Chat Application**

A WhatsApp-inspired, browser-based messaging application built with

**\*\*React\*\*** for the frontend and **\*\*Java Spring Boot\*\*** for
the backend. The

system uses **\*\*PostgreSQL\*\*** for persistent storage,
**\*\*JWT\*\*** for

authentication, and **\*\*WebSocket with STOMP\*\*** for real-time
message

delivery.

\> This project is being developed as a learning-focused full-stack

\> application. The first release targets a reliable MVP rather than

\> complete feature parity with WhatsApp.

**\## 1. Project Overview**

Registered users can:

\-   Create an account and sign in securely.

\-   Search for other registered users.

\-   Start or retrieve one-to-one conversations.

\-   Send and receive text messages in real time.

\-   Retrieve previously exchanged messages from persistent chat
history.

Messages are validated by the backend and stored in PostgreSQL before

being broadcast to connected conversation participants. If a recipient

is offline, the message remains stored and can be retrieved when the

user returns.

**\## 2. Technology Stack**

  Layer                     Technology

**  ------------------------- ----------------------------**

  Frontend                  React, React Router

  API communication         Axios / Fetch

  Real-time communication   WebSocket, STOMP

  Backend                   Java, Spring Boot

  Backend security          Spring Security, JWT

  Password hashing          BCrypt

  ORM / data access         Spring Data JPA, Hibernate

  Database                  PostgreSQL

**\## 3. System Architecture**

The application follows a layered architecture:

1\.  **\*\*Presentation layer:\*\*** React pages and components for
login,

    registration, user search, conversations and messages.

2\.  **\*\*API communication layer:\*\*** REST client and WebSocket
client.

3\.  **\*\*Backend controller layer:\*\*** Spring Boot REST controllers
receive

    HTTP requests.

4\.  **\*\*Security layer:\*\*** Spring Security and JWT validate
identity and

    access.

5\.  **\*\*Business logic layer:\*\*** Spring services implement
conversation and

    messaging rules.

6\.  **\*\*Data access layer:\*\*** Spring Data JPA repositories perform
database

    operations.

7\.  **\*\*Database layer:\*\*** PostgreSQL stores users, conversations,

    participants and messages.

**\### Communication Flow**

\-   REST APIs handle authentication, profiles, user discovery,

    conversation management and message history.

\-   WebSocket/STOMP handles live message communication.

\-   The backend validates the sender and conversation membership.

\-   The message is persisted in PostgreSQL before the server publishes

    the message event.

\-   Authorized connected participants receive the event through the

    conversation topic.

**\### Real-Time Message Flow**

\`\`\` text

Sender React Client

        \|

        \| STOMP SEND

        v

/app/conversations/{id}/messages

        \|

        v

Spring Boot Message Handler

        \|

        v

Authentication + Membership Check

        \|

        v

Message Validation

        \|

        v

PostgreSQL Persistence

        \|

        v

Publish MESSAGE_CREATED Event

        \|

        v

/topic/conversations/{id}

        \|

        v

Recipient React Client(s)

\`\`\`

**\## 4. Main Modules**

**\### 4.1 User and Role Management**

Roles defined for the system:

** 
-----------------------------------------------------------------------**

  Role                                Permissions

**  -----------------------------------
-----------------------------------**

  Guest                               Register, log in and request

                                      password reset

  Registered User                     Manage own profile, search users,

                                      create conversations, send and
read

                                      messages in conversations they

                                      belong to

  Administrator (future)              Account management and operational

                                      support; not required for the
first

                                      MVP

 
-----------------------------------------------------------------------

Features: - Login and logout. - JWT authentication. - BCrypt password

hashing. - Password reset (planned). - Profile management. - Protected

routes and authorization checks.

**\### 4.2 User Discovery and Profile**

\-   Search users by username or display name.

\-   View public profile details.

\-   View and update the current user's profile.

\-   Do not expose password hashes or private account data.

**\### 4.3 Conversation Management**

\-   Create or retrieve a one-to-one conversation.

\-   List the current user's conversations.

\-   View conversation participants.

\-   Prevent duplicate direct conversations.

\-   Verify participant membership before allowing access.

**\### 4.4 Messaging and Real-Time Communication**

\-   Send text messages.

\-   Persist messages in PostgreSQL.

\-   Retrieve paginated chat history.

\-   Deliver messages through WebSocket/STOMP.

\-   Handle reconnections.

\-   Ensure only conversation participants can send or subscribe.

**\### 4.5 Frontend Interface**

\-   Login and registration pages.

\-   Conversation sidebar.

\-   User search.

\-   Chat window and message composer.

\-   Message timestamps.

\-   Loading, empty and error states.

\-   Responsive layout.

**\## 5. Database Design**

The database uses relational tables. A conversation can contain multiple

participants, and each conversation can contain multiple messages. The

\`conversation_participants\` table implements the many-to-many

relationship between users and conversations.

** 
-----------------------------------------------------------------------**

  Table                               Important fields

**  -----------------------------------
-----------------------------------**

  \`users\`                             \`id\` (PK), \`username\`
(unique),

                                      \`email\` (unique),
\`password_hash\`,

                                      \`display_name\`, \`avatar_url\`,

                                      \`created_at\`

  \`conversations\`                     \`id\` (PK), \`type\`,
\`title\`,

                                      \`created_by\` (FK),
\`created_at\`

  \`conversation_participants\`         \`id\` (PK), \`conversation_id\`
(FK),

                                      \`user_id\` (FK), \`joined_at\`,

                                      \`last_read_at\`; unique pair of

                                      conversation and user

  \`messages\`                          \`id\` (PK), \`conversation_id\`
(FK),

                                      \`sender_id\` (FK), \`content\`,

                                      \`message_type\`, \`created_at\`

  \`message_receipts\` (future)         \`id\` (PK), \`message_id\`
(FK),

                                      \`user_id\` (FK), \`status\`,

                                      \`updated_at\`

 
-----------------------------------------------------------------------

**\### Database Rules**

\-   Passwords are stored only as BCrypt hashes.

\-   \`sender_id\` is derived from the authenticated user, not trusted
from

    client input.

\-   Only conversation participants can access a conversation and its

    messages.

\-   Messages are stored before real-time broadcast.

\-   Foreign keys and unique constraints protect relational integrity.

**\## 6. REST API Design**

Base path: \`/api/v1\`

Protected endpoints require:

\`\`\` http

Authorization: Bearer \<JWT\>

Content-Type: application/json

\`\`\`

All request and response bodies use JSON.

**\### Authentication Endpoints**

** 
-------------------------------------------------------------------------**

  Method                  Endpoint                  Purpose

**  ----------------------- -------------------------
-----------------------**

  POST                    \`/auth/register\`          Register a new
user

  POST                    \`/auth/login\`             Authenticate user
and

                                                    issue JWT

  POST                    \`/auth/logout\`            Logout /
invalidate

                                                    session or refresh

                                                    token, if
implemented

  POST                    \`/auth/refresh\`           Refresh access
token,

                                                    if refresh-token
flow

                                                    is implemented

  POST                    \`/auth/password/forgot\`   Request password
reset

  POST                    \`/auth/password/reset\`    Reset password
using a

                                                    valid reset token

  GET                     \`/auth/me\`                Get currently

                                                    authenticated user

 
-------------------------------------------------------------------------

**\### User Endpoints**

  Method   Endpoint                Purpose

**  -------- ----------------------- -------------------------**

  GET      \`/users?query={text}\`   Search registered users

  GET      \`/users/{userId}\`       Get safe public profile

  GET      \`/users/me\`             Get own profile

  PATCH    \`/users/me\`             Update own profile

**\### Conversation Endpoints**

** 
------------------------------------------------------------------------------------**

  Method                  Endpoint                             Purpose

**  ----------------------- ------------------------------------
-----------------------**

  POST                    \`/conversations/direct\`              Create
or retrieve

                                                             
 one-to-one conversation

  GET                     \`/conversations\`                     List
current user's

                                                             
 conversations

  GET                     \`/conversations/{id}\`                Get
conversation

                                                               details;
participant

                                                               only

  GET                     \`/conversations/{id}/participants\`   Get
conversation

                                                             
 participants

 
------------------------------------------------------------------------------------

**\### Message Endpoints**

** 
--------------------------------------------------------------------------------**

  Method                  Endpoint                         Purpose

**  ----------------------- --------------------------------
-----------------------**

  GET                     \`/conversations/{id}/messages\`   Get
paginated message

                                                           history

  GET                     \`/messages/{messageId}\`          Get a
message;

                                                           participant
only

  PATCH                   \`/messages/{messageId}\`          Edit own
message

                                                           (optional
extension)

  DELETE                  \`/messages/{messageId}\`          Delete own
message

                                                           (optional
extension)

 
--------------------------------------------------------------------------------

**\## 7. WebSocket / STOMP Design**

** 
------------------------------------------------------------------------------------**

  Destination                          Direction               Purpose

**  ------------------------------------ -----------------------
-----------------------**

  \`/ws\`                                CONNECT                
Establish authenticated

                                                               WebSocket
connection

  \`/app/conversations/{id}/messages\`   SEND                    Client
submits a new

                                                               message

  \`/topic/conversations/{id}\`          SUBSCRIBE / server      Receive
new messages

                                       publish                 for a
conversation;

                                                             
 participant

                                                             
 authorization required

  \`/user/queue/errors\`                 Server publish          Deliver
user-specific

                                                               errors

 
------------------------------------------------------------------------------------

**\### Message Processing**

1\.  The client sends message content to

    \`/app/conversations/{id}/messages\`.

2\.  The server obtains the sender identity from the authenticated

    \`Principal\`.

3\.  The server validates conversation membership and the message.

4\.  The message is saved in PostgreSQL.

5\.  The server publishes a \`MESSAGE_CREATED\` event to

    \`/topic/conversations/{id}\`.

**\*\*Security requirement:\*\*** Clients must not be allowed to choose
a

trusted \`senderId\`. The backend must derive sender identity from the

authenticated connection and authorize conversation subscriptions.

**\## 8. Security Considerations**

\-   Use JWT authentication for protected operations.

\-   Hash passwords with BCrypt; never store plaintext passwords.

\-   Validate conversation membership for message reads, sends and

    WebSocket subscriptions.

\-   Do not expose password hashes in API responses.

\-   Validate request payloads and message content.

\-   Use HTTPS/WSS in deployment.

\-   Keep secrets and database credentials out of source control.

**\## 9. Development Phases**

** 
-----------------------------------------------------------------------**

  Phase                   Development             Duration

**  ----------------------- -----------------------
-----------------------**

  1                       Requirements and UI     2 days

                          design                  

  2                       React and Spring Boot   2 days

                          project setup;          

                          PostgreSQL connection   

  3                       Authentication, JWT and 1 week

                          user management         

  4                       Conversation and        3--4 days

                          database implementation 

  5                       Message APIs and chat   2--3 days

                          history                 

  6                       WebSocket/STOMP         3--5 days

                          real-time messaging     

  7                       Frontend integration    4--5 days

                          and testing             

  8                       Deployment and          2--3 days

                          documentation           

 
-----------------------------------------------------------------------

These are planning estimates, not guaranteed completion dates.

**\## 10. MVP Completion Criteria**

The MVP is considered complete when:

\-   Users can register and log in.

\-   Authenticated users can find another user and create or open a

    direct conversation.

\-   Messages are saved in PostgreSQL.

\-   Connected participants receive messages in real time.

\-   Conversation history remains available after a page refresh.

\-   Unauthorized users cannot access conversations or their messages.

**\## 11. Future Enhancements**

The following features are outside the initial MVP:

\-   Group chat.

\-   Image and document sharing.

\-   Typing indicators.

\-   Online status.

\-   Delivered and read receipts.

\-   Push notifications.

\-   Voice and video calling.

\-   End-to-end encryption.

**\## 12. Project Scope Note**

This README describes the planned system design and MVP scope. It does

not claim that all listed endpoints, modules or features have already

been implemented. Update the setup instructions, screenshots, deployment

links and feature status as development progresses...
