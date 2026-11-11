# /me setup — Digital Twin Calibration

This document guides the setup process. When a user runs `/me setup`, follow these steps exactly.

---

## Step 0: Confirm scope and processing

Explain the proposed message date range (start small; up to 30 days only when requested), authorized data categories, local output files and configured model provider. Message content and profiles may be sent to that provider when analyzed by the AI tool. Ask for explicit consent before fetching or analyzing. If the provider or account policy is unknown, pause and resolve it. Redact personal/client information and exclude confidential client chats. `.gitignore` protects normal Git adds only; it does not prevent AI-tool uploads.

## Step 1: Authenticate & Identify User

```bash
lark-cli auth login --domain im,vc,calendar,contact,docs,minutes
```

Then get the current user's identity:

```bash
lark-cli config show --format json
```

Extract: `user_name`, `user_open_id`. These are needed for all subsequent steps.

If already authenticated, skip to Step 2.

---

## Step 2: Retrieve only the approved date range

Use a restricted temporary directory. Keep the returned directory path for the following steps, without printing message contents. Retrieval failure removes the temporary file immediately. Validate/redact locally before allowing the AI tool to read the content; if you cannot safely redact it, ask the user for an approved redacted export instead. Do not copy sensitive raw data to `private/`.

```bash
umask 077
ME_SETUP_DIR="$(mktemp -d "${TMPDIR:-/tmp}/me-setup.XXXXXX")"
if ! lark-cli im +messages-search \
  --start "<approved start ISO8601>" \
  --end "<approved end ISO8601>" \
  --sender "<user_open_id>" \
  --page-all \
  --format json > "$ME_SETUP_DIR/messages.json"; then
  rm -rf -- "$ME_SETUP_DIR"
  exit 1
fi
printf '%s\n' "$ME_SETUP_DIR"
```

This filters for the user's own sent messages, which can still contain other people's or clients' confidential information. Check and redact it before model analysis.

---

## Step 3: Analyze Communication Patterns

From the message data, extract:

### Persona Analysis
- **Total messages** and daily average
- **Average message length** (characters)
- **Short/medium/long message ratio**
- **Group vs P2P ratio**
- **Active hours distribution** (find peak hours)
- **Top catchphrases** — scan for repeated short phrases (2-6 chars) that appear 3+ times
- **Emotional tone** — categorize messages into positive/curious/action/negative signals
- **Message type distribution** — text, image, file, post, sticker

### Voice Rules Extraction
- **Agreement patterns** — how does the user say "yes/ok" (e.g., 好的/好哦/OK/got it)
- **Gratitude patterns** — how do they say thanks
- **Disagreement patterns** — how do they push back
- **Formality level** — ratio of formal vs casual language
- **Unique expressions** — phrases that appear repeatedly and are distinctive
- **Nickname patterns** — do they use nicknames for colleagues?
- **Punctuation habits** — emoji frequency, exclamation marks, tildes, etc.

### Relationship Mapping
- **@mention frequency** — who does the user mention most often
- **Chat activity** — which groups have the most messages from this user
- **Chat categorization** — label each active group (core team / project / client / social / noise)
- **Key collaborators** — top 10 by interaction frequency, with inferred role

---

## Step 4: Generate Profile Files

Create `private/` directory and write three files:

### private/persona.md

```markdown
# [User Name] Communication Profile

> Generated on [date] from [N] messages over 30 days

## Stats
- Total messages: X
- Daily average: X
- Average length: X chars
- Group vs P2P: X% vs X%

## Active Hours
[hour distribution]

## Top Catchphrases
[table: phrase | count | context]

## Emotional Tone
[breakdown: positive X% / curious X% / action X% / negative X%]

## Message Style
[short/medium/long ratio, image frequency, etc.]
```

### private/voice-rules.md

```markdown
# [User Name] Voice Rules

## Length
- Default: [X] sentences, [X] chars average

## Word Choices
| Scenario | Uses | Avoids |
|----------|------|--------|
| Agreement | [extracted] | [inferred opposites] |
| Thanks | [extracted] | [formal alternatives] |
| Don't know | [extracted] | [formal alternatives] |

## Addressing
| Closeness | Style | Example |
|-----------|-------|---------|
| Close | [pattern] | [example] |
| Normal | [pattern] | [example] |
| External | [pattern] | [example] |

## Unique Expressions
[list of distinctive phrases with usage context]
```

### private/relationship-map.md

```markdown
# [User Name] Relationship Map

## Key Collaborators
| Person | Frequency | Role | How to address |
|--------|-----------|------|----------------|
[top 10 by interaction]

## Active Groups
| Group | Messages | Category | Priority |
|-------|----------|----------|----------|
[groups sorted by activity]

## Reply Priority
1. Must reply: DMs, @mentions
2. Should reply: core group discussions
3. Optional: general group chats
4. Ignore: bot messages, system notifications
```

---

## Step 5: Cleanup

After successful calibration, or if any subsequent step fails, remove the exact temporary directory returned in Step 2. Validate that it is the expected `me-setup.*` directory before removing it. An interrupted agent session may leave files behind; report this possibility instead of promising automatic cleanup. Do not claim that file removal deletes AI-tool transcripts, provider logs or backups. Apply those services' retention/deletion controls separately.

---

## Step 6: Confirm

Tell the user:
- Setup complete
- Show a summary of what was found (top 3 catchphrases, peak hours, key collaborators)
- State which profiles were saved locally and which provider processed the input; remind them that git-ignore is not an upload barrier
- They can now use `/me`, `/me catch-up`, `/me reply`, etc.
