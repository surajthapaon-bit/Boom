# Between Us

A private, anonymous-by-default space for short notes, long letters, replies, saving, search, and per-person read status. No example people or real names are created.

## Run locally

1. Install Python 3.10+.
2. From this folder, run `pip install -r requirements.txt`.
3. Choose a unique, strong room passphrase (at least 12 characters). In a terminal:

   macOS / Linux:
   ```bash
   export BOARD_ACCESS_CODE="replace-with-a-long-random-secret"
   streamlit run app.py
   ```

   Windows PowerShell:
   ```powershell
   $env:BOARD_ACCESS_CODE="replace-with-a-long-random-secret"
   streamlit run app.py
   ```

   Alternatively put `BOARD_ACCESS_CODE = "..."` in `.streamlit/secrets.toml`, which must stay private.

4. The first visitor unlocks the room and creates a private seat. Save the one-time *return key* displayed on screen; all subsequent sign-ins require both the shared room passphrase and that private key.
5. Share **only the room passphrase and app address** with trusted people. Every other person creates their own seat; never share a personal return key.

## How the privacy model works

- Users have system-issued identifiers (`Member 01`, `Member 02`, ...), not names or profiles.
- A message addressed to **Everyone in the room** can be viewed by present and future room participants.
- A message addressed to a member is visible only to its author and designated recipient.
- Replies inherit the original conversation's audience. A reply to a group post is visible to the whole group.
- Read markers and bookmarks are per participant, not global.
- Searches and exports obey the same visibility rules.
- Posts can be soft-removed by their author. The text is erased from the active database, while any existing reply thread stays intact.
- Personal return keys are never stored in plaintext on the server (a SHA-256 digest is stored instead). **Save yours:** lost keys cannot be recovered.
- This is **not end-to-end encryption**. The server/database administrator can access the contents. Anyone who knows the shared room code can create a new seat and see group messages. Use HTTPS, a strong passphrase, and secure hosting for sensitive use. The app does not include brute-force rate limiting, invite revocation, or recovery.

## Persistence and deployment

By default data is in `between_us.db` (a *new* database, separate from the previous `noteboard.db`). To specify a persistent disk mount, set `BETWEEN_US_DB_PATH=/path/on/persistent/volume/between_us.db`.

**Important:** Some managed Streamlit deployments have ephemeral file systems. SQLite is suitable for a single-server app only when backed by a durable volume. Otherwise data will disappear on restart. For multiple app instances or reliable cloud operation at scale, use a managed PostgreSQL database and proper application authentication instead. Streamlit session state is not a browser login cookie, so a page reload requires logging back in using your keys.

The interface uses Google Fonts; browser access to Google is required to load the optional web fonts (system fonts are the fallback). For an even more private setup, remove the Google Fonts `@import` from `app.py`.

## Other configuration

- `APP_TIMEZONE`: display timezone, defaults to `America/Toronto` (e.g., `Europe/London`). Stored timestamps are UTC.
- `BETWEEN_US_DB_PATH`: SQLite file path; defaults to `between_us.db`.

## Verification

Run `python -m unittest discover -s tests -v` to exercise the data-access and privacy rules.
