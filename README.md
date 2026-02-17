# Ghostli Auth

**Ghostli AuthManager** is the advanced encryption and authentication module used in Ghostli. It employs a **Hybrid Storage System** (SQLite + LocalStorage) to ensure secure, persistent, and synchronized data management while protecting user privacy.

---

## Table of Contents

- [Features](#features)
- [Cryptographic Algorithms](#cryptographic-algorithms)
- [Workflow Diagram](#workflow-diagram)
- [User Sessions](#user-sessions)
- [Data Storage and Security](#data-storage-and-security)

---

## Features

- **Hybrid Storage System** – Prioritizes secure SQLite database via IPC, with safe LocalStorage fallback/sync.
- **Advanced Password Validation** – Enforces 12+ chars, complexity rules, and checks against common weak passwords/patterns.
- **Secure Session Management** – Non-persistent session keys that exist only in volatile memory.
- **Memory Protection** – Sensitive buffers (passwords, keys, IVs) are tracked and securely wiped (overwritten 3 times) after use.
- **Timing Attack Protection** – Uses constant-time comparison for password verification with randomized delays.
- **Data Migration** – Automatically encrypts and migrates legacy data from LocalStorage to the secure SQLite database.
- **Security Reporting** – Provides real-time status on crypto support, authentication state, and data encryption.

---

## Cryptographic Algorithms

1. **AES-GCM 256-bit**  
   - **Role**: Symmetric encryption for all user data.  
   - **Specs**: 12-byte IV (Initialization Vector), 128-bit authentication tag.  
   - **Security**: Ensures confidentiality, data integrity, and authenticity.

2. **PBKDF2 with SHA-256**  
   - **Role**: Key Derivation for passwords.  
   - **Specs**: **300,000 iterations** (exceeds standard recommendations).  
   - **Salt**: 32-byte cryptographically secure random salt.  

3. **Secure Random Generation**  
   - **Role**: Generates Salts, IVs, and memory-wipe patterns.  
   - **Source**: `crypto.getRandomValues()` ensuring high entropy.

4. **Constant-Time Comparison**  
   - **Role**: Password Hash Verification.  
   - **Benefit**: Prevents timing attacks by ensuring comparison time is independent of input.

---

## Workflow Diagram

                 [Application Start]
                          |
                          v
                 +---------------------+
                 | Check DB / LocalStorage |
                 | for Auth Data       |
                 +---------------------+
                          |
            +-------------+-------------+
            |                           |
            v                           v
    [No Auth Data]              [Auth Data Found]
            |                           |
            v                           v
     [Setup Password]               [Login]
            |                           |
       (Validate: 12+ chars,        (Derive Key: PBKDF2
        Complexity, Unique)          300k iterations)
            |                           |
            v                           v
    [Generate Salt & Hash]       [Constant-Time Compare]
            |                           |
            +------------+--------------+
                         |
                         v
          +-------------------------------+
          |   Session Key Derived (RAM)    |
          |   (AES-GCM 256-bit)            |
          +-------------------------------+
                         |
                         v
            [Store/Read Encrypted Data]
          (Prioritizes SQLite -> IPC Bridge)
                         |
                         v
            [Decrypt & Parse JSON]
                         |
                         v
              [Secure Wipe Sensitive Buffers]
                         |
                         v
              [Logout / Clear Memory]


---

## User Sessions

- **Ephemeral Keys**: The AES-GCM session key is derived only upon successful login and is **never stored** on disk.
- **Memory Hygiene**: The AuthManager tracks sensitive `Uint8Array` buffers and automatically wipes them (0xFF, 0x00, 0xAA patterns) after use or timeout.
- **State Management**: Authentication state is managed in the main process, ensuring the renderer process (UI) remains secure.

---

## Data Storage and Security

1. **Versioning**: Data packages are versioned (currently `v2.1`) to support backward compatibility and future upgrades.
2. **Encryption Boundary**: All data is encrypted **before** it leaves the AuthManager. The database and LocalStorage only ever see base64-encoded encrypted blobs.
3. **SQLite Integration**: Utilizing Electron IPC, data is preferably stored in a local SQLite database (`ghostli_encrypted_data` key).
4. **Fallback Mechanism**: If the database is inaccessible, the system seamlessly falls back to LocalStorage, ensuring data availability without compromising security.

---


[https://ghostliai.eu](https://ghostliai.eu)  
**Best AI programming assistant**

![Ghostli Logo](https://ghostliai.eu/images/head.png)
