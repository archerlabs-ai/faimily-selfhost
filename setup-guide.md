# fAImily setup guide

This walks you through creating accounts and locking down each child's model
to their own account only. No groups required, since each kid gets a fully
personalized model anyway.

## 1. Create your accounts

1. Open the web UI:
    - **On this machine** — open `http://localhost:3000`
    - **On another device (same network/wifi)** — open `http://<SERVER_IP>:3000`

        Find your server's (LAN) IP address (Ex: `192.168.x.x` or `10.x.x.x`):
        - **Linux** — run `ip addr show | grep 'inet '`
        - **macOS** — run `ipconfig getifaddr en0`
        - **Windows** — run `ipconfig` and look for `IPv4 Address` under your active network adapter

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

## 7. Set up remote access with Tailscale

Your fAImily stack only runs on your home network by default. For your kid
to reach it from school, a friend's house, or anywhere off your home wifi,
you need a way to connect back in without exposing it to the open internet.
We use Tailscale for this, since it keeps everything private and
device-to-device, no public URL, no third party seeing your traffic.

1. Create a free account at [tailscale.com](https://tailscale.com) (free
   tier covers up to 3 users and 100 devices, plenty for a family).
2. Install Tailscale on the machine running your Docker stack (the same
   Mac or server running `docker compose up`):
    ```
    curl -fsSL https://tailscale.com/install.sh | sh
    sudo tailscale up
    ```
    On Mac, you can also just install the Tailscale app from the App Store
    or tailscale.com/download and sign in through the menu bar icon.
3. Follow the link it gives you to authenticate in your browser.
4. Install the Tailscale app on each device that needs access: your kid's
   laptop, tablet, or phone. Sign in with the same Tailscale account.
5. Once both devices show as connected in your Tailscale admin console
   (admin.tailscale.com), your kid's device can reach your server using its
   Tailscale address, something like `http://100.x.x.x:3000`, from
   anywhere with internet access, no port forwarding required.
6. Optional: enable MagicDNS in the Tailscale admin console (DNS tab) so
   your server gets a memorable name instead of a number, like
   `http://faimily-server:3000`.

A few things worth knowing:

- Tailscale itself is not self-hosted. It relies on Tailscale's own
  coordination servers to connect your devices, though the actual chat
  traffic goes device-to-device, not through Tailscale's servers. This is a
  reasonable tradeoff for the privacy you get versus opening ports on your
  router.
- By default, devices need to re-authenticate every 90 days. For a device
  you trust long-term, like your own server, you can disable this in the
  admin console under Machines, but consider leaving it on for your kid's
  devices as an extra layer of control.
- Do not use Cloudflare Tunnel or any other public-facing tunnel for this
  setup. That exposes your server to the open internet, which works
  against the "nothing leaves your home network" design this whole stack
  is built around.

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
