# fAImily setup guide

This walks you through creating accounts and locking down each child's model
to their own account only. No groups required, since each kid gets a fully
personalized model anyway.

## 1. Create your accounts

1. Open `http://localhost:3000`.
2. The first account you create becomes the admin account. This should be
   you, the parent.
3. Go to Admin Panel > Users and create an account for each child. Use a
   simple username they'll remember, like their first name.

## 2. Pull and customize the base model

1. Pull a base model into Ollama (see `docs/model-guide.md` for sizing):
   ```
   docker exec -it faimily-ollama ollama pull llama3.2:3b
   ```
2. Copy one of the templates from `modelfiles/` for your child's age tier.
3. Edit the SYSTEM prompt to personalize it: their name, interests, reading
   level. Keep personalization conditional ("when relevant"), not forced
   into every answer.
4. Build the model:
   ```
   docker exec -it faimily-ollama ollama create faimily-kidname -f /path/to/Modelfile
   ```
   Naming convention: `faimily-[firstname]`, lowercase, no spaces.

## 3. Lock the model to that one child

This is the key step. By default, a newly created model in Open WebUI is
visible to whoever made it. You need to set it to Private and then grant
access only to your child's account.

1. In Open WebUI, go to Workspace > Models.
2. Find your child's model (`faimily-kidname`) and click the edit icon.
3. Find the Access Control / Visibility setting and set it to **Private**.
4. In the access grant section, add your child's specific user account
   under Read access. Do not add a group, just their individual account.
5. Save.

Repeat steps 2-5 for each child's model.

## 4. Hide the base model from kids

Your child should only ever see their own personalized model in the picker,
not the raw base model underneath it.

1. Go to Workspace > Models and find the base model (e.g. `llama3.2:3b`).
2. Set its visibility to Private as well.
3. Do not grant your child's account access to the base model directly.
   Their access to the underlying model flows through their personalized
   model, since it's built on top of it.

## 5. Confirm it worked

Open WebUI has an admin-only view that shows exactly what a user can see.

1. Go to Admin Panel > Users.
2. Hover over your child's row and click the preview access icon.
3. Confirm the only model listed is their own `faimily-kidname` model, not
   the base model and not any sibling's model.

## 6. Enable private search (optional but recommended)

SearXNG is already running as part of the stack. To connect it:

1. Go to Admin Panel > Settings > Web Search.
2. Set the search engine to SearXNG.
3. Point it at `http://searxng:8080` (the internal Docker network address,
   not localhost).
4. Save and test with a query in a chat.

## Troubleshooting

**My kid can see other models in the picker.**
Double check the base model's visibility is Private, not Public. A Public
base model is visible to everyone regardless of what you do with the
personalized models built on top of it.

**My kid can't see their own model.**
Confirm their user account, not a group, was added under Read access on
that specific model. Re-check step 3 above, this is the most common miss.

**Responses feel slow or repetitive.**
Your hardware may be undersized for the model you pulled. See the "when to
upgrade" section in `docs/model-guide.md`.

## A note on this guide

This guide reflects the access-control behavior in current Open WebUI
releases as of this writing. Open WebUI updates frequently. If a step
doesn't match what you see in your version, check the official docs at
docs.openwebui.com or open an issue in this repo so we can update the guide.
