# Archive

An offline photo vault. Photos are encrypted with AES-256-GCM before they touch
storage, the key is derived from your passcode and only ever exists in memory,
and the app has no network permission at all.

## Putting it on your phone

1. Create a GitHub repo. Give it a dull name — `archive`, `inventory`, `notes-app`.
2. Upload all the files in this folder to the root of the repo.
3. Settings → Pages → Deploy from a branch → `main` / `(root)`. Wait a minute.
4. Open the Pages URL in **Safari** on the phone.
5. Share → **Add to Home Screen**.
6. Launch it from the home screen icon, not from Safari. That gives it its own
   isolated, persistent storage.
7. Set your passcode.

Updating later: replace `index.html` in the repo and commit. The URL stays the
same, so your photos survive the update untouched.

## Files

| File | What it is |
|---|---|
| `index.html` | The entire app — UI, crypto, storage |
| `sw.js` | Service worker, caches the app shell so it opens with no signal |
| `manifest.webmanifest` | Makes it installable as a standalone app |
| `icon-*.png` | Home screen icons |

## How the encryption works

- A random 256-bit master key is generated when you create the vault.
- Your passcode goes through PBKDF2-SHA256, 650,000 iterations, with a random
  16-byte salt, producing a wrapping key.
- The master key is stored only in its wrapped (encrypted) form. The passcode
  itself is never stored, anywhere.
- Every photo and every thumbnail is encrypted with the master key and a fresh
  random IV before it is written to IndexedDB. Album names are encrypted too.
- Unlocking = decrypting the wrapped master key. A wrong passcode fails the
  GCM authentication tag, so there is nothing to compare against and nothing
  to bypass.
- Changing the passcode rewraps the master key. Photos are not re-encrypted,
  so it is instant.

