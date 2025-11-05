# Ghostli Auth

**Ghostli AuthManager** is the encryption and authentication module used in Ghostli. It provides **secure session management and encrypted data storage**, ensuring user data is protected at all times.

---

## Table of Contents

- [Features](#features)
- [Cryptographic Algorithms](#cryptographic-algorithms)
- [Workflow Diagram](#workflow-diagram)
- [User Sessions](#user-sessions)
- [Data Storage and Security](#data-storage-and-security)
- [Security Status](#security-status)

---

## Features

- **User Authentication** – secure login and password verification.
- **Session Management** – each session has a unique key for encrypting data.
- **Secure Data Storage** – all user data stored in `localStorage` is encrypted.
- **Memory Protection** – sensitive buffers are tracked and securely wiped.
- **Password Validation** – checks length, complexity, and uniqueness.
- **Data Migration** – safely migrate existing data to encrypted format.
- **Security Status** – allows checking the security state of the module.

---

## Cryptographic Algorithms

1. **AES-GCM 256-bit**  
   - Symmetric encryption for user data.  
   - Ensures confidentiality, integrity, and authentication.  
   - Each encryption uses a unique IV (Initialization Vector).  

2. **PBKDF2 with SHA-256**  
   - Derives keys from user passwords.  
   - High iteration count prevents brute-force attacks.  
   - Salt ensures unique hashes for identical passwords.  

3. **Constant-Time Comparison**  
   - Reduces timing attack risks during password verification.  

4. **Secure Random Generation**  
   - Session keys and IVs generated with high entropy.  
   - Sensitive buffers overwritten multiple times for additional security.

---

## Workflow Diagram

                 [Application Start]
                          |
                          v
                 +---------------------+
                 | Check if password   |
                 | exists?             |
                 +---------------------+
                          |
            +-------------+-------------+
            |                           |
            v                           v
    [No Password]               [Password Exists]
            |                           |
            v                           v
       [Set Password]                [Login]
            |                           |
            v                           v
    [Validate Password]          [Verify Password]
            |                           |
            v                           v
    [Generate Salt]              [Generate Salt]
            |                           |
            +------------+--------------+
                         |
                         v
       [Generate Hash Key & Session Key]
                         |
                         v
          +-------------------------------+
          | User Session Created           |
          | (Unique AES-GCM Key)           |
          +-------------------------------+
                         |
                         v
              [Store Data in Encrypted Form]
                         |
                         v
             [Read Data → Decrypt with Key]
                         |
                         v
                  [Access Data & Session]
                         |
                         v
               [Switch Between Sessions]
                         |
                         v
                  [Logout / Clear Memory]


---

## User Sessions

- Each user session has a **unique AES-GCM session key**.  
- The module allows **switching safely between multiple sessions**.  
- Session keys exist only in memory during active sessions and are **securely wiped after logout**.

---

## Data Storage and Security

1. All user data is **encrypted using AES-GCM 256-bit**.  
2. Sensitive memory buffers are **automatically overwritten and wiped**.  
3. Passwords are stored as **PBKDF2 hashes with salt**, preventing recovery of the original password.  
4. Data in `localStorage` is **encrypted and versioned**, allowing safe migration and backward compatibility.

---

## Security Status

- **Passwords**: secured with PBKDF2, high iterations, and salt.  
- **Data**: AES-GCM 256-bit encryption with integrity authentication.  
- **Sessions**: each session has a unique key, never stored in plaintext.  
- **Memory**: sensitive buffers wiped automatically after use.  
- **Migration**: encrypted format supports safe migration and backward compatibility.

[https://ghostli.eu](https://ghostli.eu)  
**Best AI programming assistant**

![Ghostli Logo](https://cdn.discordapp.com/attachments/1117102302956888195/1435768728381034668/head_4.png?ex=690d2b54&is=690bd9d4&hm=8c961efdab5cb7a6ef29172c2b10913e2eb8c0a6e0ee1411441acb7f198a255e&)

