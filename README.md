# Eira AI Health Assistant - Backend

This is the backend server for the Eira AI Health Assistant application. It provides RESTful APIs for user management, chat functionality, and session handling.

## Features

- User authentication and profile management
- Chat message storage and retrieval
- Session management (create, list, rename, delete)
- PostgreSQL database integration
- CORS enabled for frontend communication

## Prerequisites

- Node.js (v14 or higher)
- PostgreSQL database
- npm or yarn package manager

## Installation

1. Navigate to the backend directory:
\`\`\`bash
cd backend
\`\`\`

2. Install dependencies:
\`\`\`bash
npm install
\`\`\`

3. Create a `.env` file in the backend root directory with the following variables:
\`\`\`env
DATABASE_URL=postgresql://username:password@localhost:5432/eira_db
PORT=5000
\`\`\`

## Database Setup

Create the following tables in your PostgreSQL database:

\`\`\`sql
-- Users table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Chat sessions table
CREATE TABLE chat_sessions (
    id SERIAL PRIMARY KEY,
    user_email VARCHAR(255) REFERENCES users(email),
    title VARCHAR(255) DEFAULT 'Untitled Session',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Chat history table
CREATE TABLE chat_history (
    id SERIAL PRIMARY KEY,
    user_email VARCHAR(255) REFERENCES users(email),
    session_id INTEGER REFERENCES chat_sessions(id) ON DELETE CASCADE,
    message TEXT NOT NULL,
    sender VARCHAR(50) NOT NULL, -- 'user' or 'bot'
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
\`\`\`

## Running the Server

### Development Mode
## Running the Server

node index.js
This will start the server with nodemon for automatic restarts on file changes.

The server will run on `http://localhost:5000` by default (or the PORT specified in your .env file).

## API Endpoints

### User Routes (`/api/users`)

#### Get or Create User
- **POST** `/api/users/get-or-create`
- **Body**: `{ email: string, name?: string }`
- **Description**: Creates a new user or returns existing user

#### Get User by Email
- **GET** `/api/users/get-user?email={email}`
- **Description**: Retrieves user information by email

#### Update User Name
- **POST** `/api/users/update-name`
- **Body**: `{ email: string, name: string }`
- **Description**: Updates user's display name

### Session Routes (`/api/sessions`)

#### Create Session
- **POST** `/api/sessions/create`
- **Body**: `{ email: string, title?: string }`
- **Description**: Creates a new chat session

#### List Sessions
- **GET** `/api/sessions/list?email={email}`
- **Description**: Gets all sessions for a user

#### Rename Session
- **PUT** `/api/sessions/rename`
- **Body**: `{ sessionId: number, newTitle: string }`
- **Description**: Updates session title

#### Delete Session
- **DELETE** `/api/sessions/{id}`
- **Description**: Deletes a session and its chat history

### Chat Routes (`/api/chat`)

#### Add Message
- **POST** `/api/chat/add`
- **Body**: `{ email: string, session_id: number, message: string, sender: 'user' | 'bot' }`
- **Description**: Adds a message to chat history

#### Get Chat History
- **GET** `/api/chat/history?session_id={session_id}`
- **Description**: Retrieves all messages for a session

## Project Structure

\`\`\`
backend/
├── routes/
│   ├── userRoutes.js      # User management endpoints
│   ├── sessionRoutes.js   # Session management endpoints
│   └── chatRoutes.js      # Chat functionality endpoints
├── db.js                  # Database connection configuration
├── index.js               # Express server setup and middleware
├── package.json           # Dependencies and scripts
├── .env                   # Environment variables (create this)
└── README.md             # This file
\`\`\`

## Dependencies

- **express**: Web framework for Node.js
- **cors**: Cross-Origin Resource Sharing middleware
- **pg**: PostgreSQL client for Node.js
- **dotenv**: Environment variable loader

## Development Dependencies

- **nodemon**: Development server with auto-restart

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `DATABASE_URL` | PostgreSQL connection string | Required |
| `PORT` | Server port number | 5000 |

## Error Handling

The API returns appropriate HTTP status codes:
- `200`: Success
- `400`: Bad Request (missing or invalid parameters)
- `404`: Not Found (user or session not found)
- `500`: Internal Server Error (database or server errors)

## CORS Configuration

The server is configured to accept requests from any origin. In production, you should configure CORS to only allow requests from your frontend domain.

## Security Considerations

- Ensure your PostgreSQL database is properly secured
- Use environment variables for sensitive configuration
- Consider implementing rate limiting for production use
- Add input validation and sanitization as needed
- Implement proper authentication middleware if required

## Troubleshooting

### Common Issues

1. **Database Connection Error**
   - Verify your `DATABASE_URL` is correct
   - Ensure PostgreSQL is running
   - Check database credentials and permissions

2. **Port Already in Use**
   - Change the `PORT` in your `.env` file
   - Kill any processes using the current port

3. **CORS Errors**
   - Ensure the frontend is making requests to the correct backend URL
   - Check that CORS is properly configured

## Contributing

1. Make sure to test all endpoints after making changes
2. Follow the existing code style and structure
3. Update this README if you add new features or endpoints
