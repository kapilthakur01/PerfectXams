# PerfectXskills

PerfectXskills is a full-stack secure exam-paper management website built for HackIndia. It helps exam authorities upload encrypted question papers, assign them to institution organisers, lock access until the scheduled exam time, and keep a blockchain-backed record of paper metadata.

## Overview

Question paper leaks can break trust in an exam system. PerfectXskills is designed to reduce that risk with encrypted uploads, role-based access, scheduled unlock times, and an immutable smart-contract record for every paper.

The website includes a public landing page, authentication portal, an exam authority dashboard, and an institution organiser dashboard.

## Features

- Modern landing page with hero section, process flow, feature cards, stats, team section, and secure access portal.
- Signup and login with role selection.
- Supported roles: `Exam Authority / Paper Setter` and `Institution Organiser`.
- JWT-based authentication with hashed passwords.
- Exam authority dashboard for uploading papers, setting exam code/name, scheduling unlock date/time, assigning an organiser, and viewing uploaded papers.
- Institution organiser dashboard for viewing assigned papers, seeing a live unlock countdown, and accessing papers only after the unlock time.
- File upload support using Multer.
- AES-256-CBC file encryption before storage.
- MongoDB storage for users and paper metadata.
- Solidity smart contract for storing paper hash, exam code, unlock timestamp, and uploader address.
- Local Hardhat blockchain integration through ethers.js.

## Tech Stack

| Layer | Technologies |
| --- | --- |
| Frontend | React, Vite, React Router, Lucide React |
| Backend | Node.js, Express.js, MongoDB, Mongoose |
| Auth | JWT, bcryptjs |
| File Security | Multer, Node crypto, AES-256-CBC |
| Blockchain | Solidity, Hardhat, ethers.js |

## Project Structure

```text
.
|-- backend
|   |-- contracts
|   |   `-- ExamStorage.sol
|   |-- controllers
|   |-- middleware
|   |-- models
|   |-- routes
|   |-- scripts
|   |-- services
|   |-- uploads
|   |-- hardhat.config.js
|   |-- package.json
|   `-- server.js
|-- frontend
|   |-- public
|   |-- src
|   |   |-- components
|   |   |-- pages
|   |   |-- styles
|   |   |-- App.jsx
|   |   `-- main.jsx
|   |-- package.json
|   `-- vite.config.js
|-- LICENSE
`-- README.md
```

## Getting Started

### Prerequisites

- Node.js and npm
- MongoDB running locally or a MongoDB Atlas connection string
- Git
- A local Hardhat node for blockchain-backed uploads

### Backend Setup

```bash
cd backend
npm install
```

Create the backend environment file:

```bash
cp .env.example .env
```

For PowerShell:

```powershell
Copy-Item .env.example .env
```

Update `backend/.env`:

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/perfectxskills
JWT_SECRET=replace_this_with_a_long_random_string_at_least_32_chars
JWT_EXPIRES_IN=7d
CLIENT_URL=http://localhost:5173
MASTER_KEY=replace_this_with_a_strong_file_encryption_key
```

Start the backend:

```bash
npm run dev
```

If you do not use `nodemon`, run:

```bash
npm start
```

The API runs at:

```text
http://localhost:5000/api
```

### Blockchain Setup

Paper upload calls the blockchain service, so run a local Hardhat node before testing uploads.

In one terminal:

```bash
cd backend
npx hardhat node
```

In another terminal:

```bash
cd backend
npx hardhat run scripts/deploy.js --network localhost
```

Copy the deployed `ExamStorage` contract address into `backend/services/blockchainService.js` if it is different from the current `CONTRACT_ADDRESS`.

The private key currently used in `blockchainService.js` is a default local Hardhat account key. Use it only for local development.

### Frontend Setup

```bash
cd frontend
npm install
```

Create or update `frontend/.env`:

```env
VITE_API_URL=http://localhost:5000/api
```

Start the frontend:

```bash
npm run dev
```

The website runs at:

```text
http://localhost:5173
```

## Main API Routes

| Method | Route | Description |
| --- | --- | --- |
| GET | `/api/health` | Check API status |
| POST | `/api/auth/signup` | Create a user account |
| POST | `/api/auth/login` | Log in and receive a JWT |
| GET | `/api/auth/me` | Get current authenticated user |
| POST | `/api/papers/upload` | Upload and encrypt a paper |
| GET | `/api/papers/my-papers` | Get papers uploaded by the logged-in authority |
| GET | `/api/papers/assigned` | Get papers assigned to the logged-in organiser |
| GET | `/api/papers/access/:id` | Download a paper after unlock time |

## Basic Workflow

1. Start MongoDB, the Hardhat node, the backend, and the frontend.
2. Sign up as an `Institution Organiser`.
3. Sign up as an `Exam Authority / Paper Setter`.
4. Log in as the exam authority and upload a paper.
5. Enter exam name, exam code, unlock date/time, paper file, and the organiser user ID in the upload form.
6. Log in as the organiser to view assigned papers.
7. After the unlock time, use the organiser dashboard to access the decrypted paper.

## Security Notes

- Keep `.env` files private and never commit real secrets.
- Use a strong `JWT_SECRET` and `MASTER_KEY`.
- The local Hardhat private key is for development only.
- Uploaded files are encrypted in `backend/uploads` and decrypted only when an authorised organiser accesses them after the scheduled unlock time.

## License

This project is licensed under the MIT License. See `LICENSE` for details.
