```markdown
# Local Password Manager

## Description
This project is a lightweight, command-line interface (CLI) password manager written in Python. It allows users to securely store, view, and delete website credentials locally. To guarantee privacy and data protection, credentials are encrypted before being written to disk, ensuring that your raw passwords are never stored in plain text. Access to the system is restricted by a hashed master password created during the first setup.

---

## Tech Stack
* **Language:** Python 3.x
* **Encryption & Security:**
  * `cryptography` (specifically `Fernet` for symmetric encryption)
  * `hashlib` (SHA-256 for password hashing and key derivation)
  * `base64` (for URL-safe encryption key formatting)
* **Storage & Environment:**
  * `json` (for managing local database file serialization)
  * `os` (for file existence handling)

---

## How To Use

### 1. Prerequisites
Ensure you have Python installed, then install the required dependency via your terminal:
```bash
pip install cryptography

```

### 2. Initialization & Setup

Run the main script from your terminal:

```bash
python main.py

```

* **First Run (Setup):** The application will detect that no master key exists and prompt you:
```text
Please create a master password: 

```


Type your desired master password and press `Enter`. The script will generate a secure hash, save it to `master.txt`, and exit.
* **Subsequent Runs (Login):** Launch the script again. You will be prompted to authenticate:
```text
Please provide the master password:

```


Input your password. If it matches, the interactive menu will open. If it is incorrect, you will see `Wrong password` and the application will terminate.

### 3. Navigation & Operations

Once authenticated, use the numerical interactive menu to manage your credentials:

* **Option 1: Add a Credential**
Select `1` to encrypt and store a new entry. Follow the interactive prompts:
```text
Type in your website: example.com
Type in your name: john_doe
Type in your password: super_secret_password

```


The password is automatically encrypted using your master key and appended to your local database file (`passwords.json`).
* **Option 2: View Credentials**
Select `2` to read your records. The application will decrypt your saved passwords on-the-fly and output them in plain text:
```text
example.com john_doe super_secret_password

```


* **Option 3: Delete a Credential**
Select `3` to remove an entry from your records:
```text
Type in the website you'd like to delete: example.com

```


If the website matches an entry in your database, it is removed, and the local file updates automatically. If no match is found, it will print `Website Not Found`.
* **Option 4: Quit**
Select `4` to safely exit the interface.

---

## Features

* **Secure Credential Encryption:** Passwords are never written to disk in plain text. They are protected using Fernet symmetric encryption, ensuring that anyone inspecting your database directly sees only ciphertext.
* **Local Data Persistence:** Keeps your data entirely in your possession. Your data is managed through a lightweight local `passwords.json` file in the project directory.
* **Zero-Knowledge Master Authentication:** The application uses SHA-256 hashing to verify your identity. Your raw master password is never stored; only its cryptographic signature is saved in `master.txt`.
* **Dynamic Record Updates:** Automatically synchronizes additions and removals back to your local JSON storage without locking or damaging the file structure.

---

## A Note About Security

> [!WARNING]
> **Educational/Basic Utility Disclaimer**
> While this application successfully implements fundamental cryptographic libraries, it is built for educational or simple script purposes. Note these security behaviors if you intend to store critical real-world accounts:
> * **Basic Key Derivation:** The encryption key is derived using a simple, direct SHA-256 hash of your master password. Standard password managers utilize specialized, time-and-memory-hard key derivation functions (like *PBKDF2* or *Argon2*) to actively throttle brute-force attacks.
> * **Local Asset Security:** The encrypted payloads (`passwords.json`) and authentication proof (`master.txt`) sit unprotected in your local working directory. If an unauthorized user gains access to your operating system or files, they can copy your database and try to brute-force your master key offline.
> * **Strict Value Matching:** Deletion relies on string matching with the exact `website` tag provided during entry creation. Ensure consistent naming to keep your data organized.
> 
> 

```

```
