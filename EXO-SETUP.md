# ExO Example Space – Setup Status

This branch sets up the "ExO Example Space – October 2026" example in a shared Contentful space. **Steps 1–4 are done. You need to do steps 5, 6 and 7.**

## Status at a glance

| Step | What | Status | Who |
|---|---|---|---|
| 1 | Check out the example branch | ✅ Done | Already on this branch (`exo-space-setup`) |
| 2 | `bun install` | ✅ Checked it works | You run it locally as part of step 6 |
| 3 | Fill in `.env` | ✅ Values listed below | You create your own `.env` as part of step 6 |
| 4 | Import content into Contentful | ✅ **Done, nothing to do** | – |
| 5 | Set up the preview configuration | ⬜ **To do** | You, in the Contentful web app |
| 6 | Run the app locally (`bun run dev`) | ⬜ **To do** | You, on your machine |
| 7 | Open and explore an Experience | ⬜ **To do** | You, in the Contentful web app |

## Where everything lives

| | |
|---|---|
| Contentful space | `mciu72h31c4n` (Exo POCs) |
| Contentful environment | `EXO-Example-Oct-2` |
| API key to use | **"OFX ExO POC"** (already has access to `EXO-Example-Oct-2`) |
| Repo branch | `exo-space-setup` (based on `final-state`) |
| Import file used | `content-exports/step-05-end-state.json` |

## Before you start

- The Contentful CLI is only needed if you re-import. The steps below don't need it.
- Switch on **New navigation** in the Preview Center for your user. This is per user, and without it you won't see the ExO editors.
- Install [bun](https://bun.sh): `curl -fsSL https://bun.sh/install | bash`

---

## ✅ Completed steps

### Step 1 – Example branch
The original guide says to check out `final-step`, but that branch doesn't exist. The right branch is **`final-state`**. It's the only one with `content-exports/step-05-end-state.json`. This branch (`exo-space-setup`) is based on it and adds this document.

### Step 2 – Dependencies
`bun install` runs cleanly on this branch.

### Step 3 – `.env`
`.env` is gitignored, so it isn't in the repo. The values to use are in step 6.

### Step 4 – Content import (shared, already in Contentful)
Before the import, `EXO-Example-Oct-2` was empty (only the `en-US` locale), so nothing was overwritten. Now in the environment:

| Entity | Count | Published |
|---|---|---|
| Locales | 2 (`en-US`, `de-DE`) | – |
| Content types | 8 | yes |
| Entries | 40 | 40 |
| Assets | 18 | 18 |
| Design tokens | 22 | – |
| Data assemblies | 12 | 8 |
| Components | 5 (`card`, `Flex`, `Button`, `Image`, `Text`) | 0 (draft) |
| Experience templates | 1 (`page`) | 0 (draft) |
| Experiences | 1 (`Example Page`) | 0 (draft) |

The components, the template and the experience are drafts. Preview works with drafts, so you don't need to publish anything for step 7.

> **Don't re-run the import** unless the environment has been reset. If you do: the first run can fail for the `card` component, one data assembly and the experience ("Some of the referenced entities do not exist"). Running the same command a second time fixes it.

---

## ⬜ Remaining steps

### Step 5 – Set up the preview configuration
In the Contentful web app, open space `mciu72h31c4n`, then **Settings → Content preview**:

1. Create a new preview platform, for example `ExO Example Local`.
2. Set the entity type to **Template**.
3. Click **Add a preview URL** and choose **Fallback URL**.
4. Paste in:
   ```
   http://localhost:3000/api/draft/enable?secret=this-is-a-secret&locale={locale}&id={experience.sys.id}
   ```
5. Save.

Notes:
- Preview platforms are shared across the whole space, so only one person needs to create this one. Check whether a teammate already has.
- Don't edit the existing **"OFX Localhost"** platform. It belongs to another project, and each platform can only have one fallback URL.
- The `secret` in the URL must match `DRAFT_MODE_SECRET` in your `.env` (step 6).

### Step 6 – Run the app locally
```bash
git clone <this repo>
cd cx-simple-exo
git checkout exo-space-setup
bun install
cp .env.example .env
```

Fill in `.env`. Get the two tokens from **Settings → API keys → "OFX ExO POC"** in space `mciu72h31c4n`:
```
SPACE_ID=mciu72h31c4n
ENVIRONMENT_ID=EXO-Example-Oct-2
CDA_TOKEN=<Content Delivery API access token>
CPA_TOKEN=<Content Preview API access token>
DRAFT_MODE_SECRET=this-is-a-secret
```

Start the app:
```bash
bun run dev
```
It should be running at http://localhost:3000.

### Step 7 – Open and explore an Experience
1. In the Contentful web app, switch to the **`EXO-Example-Oct-2`** environment.
2. Open the **Example Page** experience. The preview pane should load your local app, through the platform from step 5.
3. Try creating a new Experience from the `page` template.
4. Look at the template and component configuration. `card` is built from the atomic `Flex`, `Image`, `Text` and `Button` components and their data assembly bindings.

---

## Troubleshooting

| What you see | Cause / fix |
|---|---|
| Preview shows a 500, and the dev log has a 404 from `preview.xdn.contentful.com/.../experiences/...` | The API key can't read `EXO-Example-Oct-2`. Use the "OFX ExO POC" key, or add the environment to your key. Also check `ENVIRONMENT_ID`. |
| `/api/draft/enable` doesn't redirect to the page | The `secret` in the preview URL doesn't match `DRAFT_MODE_SECRET`. |
| Preview pane is blank or can't connect | `bun run dev` isn't running, or isn't on port 3000. |
| Dev log warning: `No experience template registered for id "page"` | Expected with this example code. The page still shows its content. |
| ExO editors don't show up in Contentful | Switch on **New navigation** in the Preview Center for your user. |
