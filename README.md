

# ClarityBox - Decentralized Cloud Storage - Smart Contract

A Clarity smart contract for managing decentralized file metadata storage on the Stacks blockchain. This contract allows users to **upload**, **update**, **delete**, **transfer ownership**, and **control access permissions** for files stored on a decentralized storage backend.

---

## 🚀 Features

* ✅ **Upload Files**: Save metadata like name, size, and timestamp.
* ✏️ **Update Files**: Modify file name or size (only by the owner).
* ❌ **Delete Files**: Permanently remove file metadata.
* 🔁 **Transfer Ownership**: Hand over file ownership to another principal.
* 👥 **Permissions System**: Grant/revoke access to others.
* 🔐 **Access Control**: Only owners can modify or manage access.
* 🧾 **Read-only Views**: Fetch file metadata and total count.

---

## 📄 File Metadata Structure

Each file is represented as a record in the `files` map with:

```clojure
{
  owner: principal,
  name: (string-ascii 64),
  size: uint,
  created-at: uint
}
```

---

## 🛠 Contract Interface

### 🔧 Public Functions

#### `(upload-file (name (string-ascii 64)) (size uint)) → (response uint uint)`

Uploads a file. Returns the new file ID.

* **Validations**:

  * Name must be between 1 and 64 characters.
  * Size must be between 1 byte and 1 GB.

#### `(update-file (file-id uint) (new-name (string-ascii 64)) (new-size uint)) → (response bool uint)`

Updates the name or size of a file. Only the file owner can perform this action.

#### `(delete-file (file-id uint)) → (response bool uint)`

Deletes a file entry. Only the file owner can perform this action.

#### `(transfer-file-ownership (file-id uint) (new-owner principal)) → (response bool uint)`

Transfers ownership of a file to a new principal.

#### `(grant-permission (file-id uint) (permission bool) (recipient principal)) → (response bool uint)`

Grants or revokes read access to another user.

* **Validations**:

  * Only owner can grant permission.
  * Cannot grant permission to oneself.

#### `(revoke-permission (file-id uint) (user principal)) → (response bool uint)`

Revokes permission for a user.

---

### 📖 Read-only Functions

#### `(get-total-files) → (response uint none)`

Returns the total number of files uploaded.

#### `(get-file-info (file-id uint)) → (response { owner: principal, name: string-ascii, size: uint, created-at: uint } uint)`

Returns metadata for a given file ID.

---

## 🧪 Private Functions (Internal Use Only)

* **`(file-exists file-id)`**: Checks if a file exists.
* **`(get-owner-file file-id owner)`**: Verifies ownership.
* **`(get-file-size-by-owner file-id)`**: Returns file size or 0.

---

## 🧾 Error Codes

| Code | Description                   |
| ---- | ----------------------------- |
| 100  | Only owner can perform action |
| 101  | File not found                |
| 102  | File already exists           |
| 103  | Invalid file name             |
| 104  | Invalid file size             |
| 105  | Unauthorized access           |
| 106  | Invalid recipient             |

---

## 🧰 Data Maps

### `files`

```clojure
{ file-id: uint } → {
  owner: principal,
  name: (string-ascii 64),
  size: uint,
  created-at: uint
}
```

### `file-permissions`

```clojure
{ file-id: uint, user: principal } → {
  permission: bool
}
```

---

## 🏁 Deployment Notes

* Ensure `contract-owner` is set appropriately during deployment.
* Backend integration is expected to handle actual file storage (e.g., IPFS, Gaia).
* File size limits are enforced on-chain to discourage abuse.

---

## 🔐 Security Model

* Only the **file owner** can update, delete, or manage permissions.
* Permission controls do not enforce read restrictions on-chain but provide access metadata for off-chain enforcement (e.g., in an app using this contract).

---
