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

## 6. Connect from other devices on your network

Once the stack is running, any device on your home network can reach the web UI:

1. On the device you want to connect from (phone, tablet, laptop), open a browser.
2. Go to `http://<SERVER_IP>:3000` — replace `<SERVER_IP>` with the IP you found in step 1.
3. Log in with the account you created for that child (or create a new one if you haven't yet).

Docker binds port 3000 to `0.0.0.0` by default, which means it's already accessible from
other devices on your LAN. No firewall or router config is needed for local network access.

You can bookmark this URL on each device so nobody has to type the IP every time.

## 7. Enable private search (optional but recommended)

SearXNG is already running as part of the stack. To connect it:

1. Go to Admin Panel > Settings > Web Search.
2. Set the search engine to SearXNG.
3. Point it at `http://searxng:8080` (the internal Docker network address,
   not localhost).
4. Save and test with a query in a chat.

## 8. Access from outside your home (optional)

If you want to access fAImily when you're away from home, here are the two simplest
free options:

**Tailscale** — a private mesh VPN that connects your devices securely.

1. Install [Tailscale](https://tailscale.com/download) on your server and each device.
2. Sign in to the same Tailscale account on everything.
3. From a remote device, open `http://<TAILSCALE_IP>:3000` (use the server's Tailscale IP,
   which starts with `100.x.x.x`).
4. Tailscale handles all the encryption and auth. No open ports needed.

**Cloudflare Tunnel** — a free tunnel that exposes your local server through Cloudflare's
network without opening ports on your router.

1. Install `cloudflared` on the server.
2. Run: `cloudflared tunnel --url http://localhost:3000` for a quick ephemeral tunnel.
3. Or set up a [permanent tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
   with a custom domain.
4. Access your instance at the Cloudflare-provided URL or your own domain.

## Troubleshooting

**My kid can see other models in the picker.**
Double check the base model's visibility is Private, not Public. A Public
base model is visible to everyone regardless of what you do with the
personalized models built on top of it.

**My kid can't see their own model.**
Confirm their user account, not a group, was added under Read access on
that specific model. Re-check step 3 above, this is the most common miss.

**I can't reach the web UI from another device on my network.**
Make sure you're using the server's LAN IP address, not `localhost`. Both devices must
be on the same Wi-Fi or wired network. If you modified the `ports:` line in
`docker-compose.yml` to use `127.0.0.1:3000:8080` instead of `3000:8080`, revert that
change — `127.0.0.1` restricts connections to the local machine only.

**Responses feel slow or repetitive.**
Your hardware may be undersized for the model you pulled. See the "when to
upgrade" section in `docs/model-guide.md`.

## A note on this guide

This guide reflects the access-control behavior in current Open WebUI
releases as of this writing. Open WebUI updates frequently. If a step
doesn't match what you see in your version, check the official docs at
docs.openwebui.com or open an issue in this repo so we can update the guide.
