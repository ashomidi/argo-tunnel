To set up **Cloudflare Argo Tunnel** on your **Ubuntu 22.04** server and forward traffic to port **9443**, follow these steps:

---

### **1. Install Cloudflared**

Run the following commands to download and install **Cloudflared**:

```bash
curl -fsSL https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 -o cloudflared
sudo chmod +x cloudflared
sudo mv cloudflared /usr/local/bin/
```

Verify installation:

```bash
cloudflared --version
```

---

### **2. Authenticate with Cloudflare**

Authenticate with Cloudflare using the command below:

```bash
cloudflared tunnel login
```

This will generate a unique URL—open it in your browser, log in to Cloudflare, and select your domain.

---

### **3. Create and Configure the Tunnel**

#### **Step 1: Create a New Tunnel**

Run the following command to create a new **Argo Tunnel**:

```bash
cloudflared tunnel create my-tunnel
```

This will generate a **UUID** (something like `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`), which is your **Tunnel ID**.

#### **Step 2: Create a Configuration File**

Create the configuration directory:

```bash
sudo mkdir -p /etc/cloudflared
```

Then, create the Cloudflare **config file**:

```bash
sudo nano /etc/cloudflared/config.yml
```

Add the following content:

```yaml
tunnel: YOUR_TUNNEL_UUID
credentials-file: /root/.cloudflared/YOUR_TUNNEL_UUID.json

ingress:
  - hostname: your-domain.com
    service: http://localhost:9443
  - service: http_status:404
```

Replace:

- `YOUR_TUNNEL_UUID` with your tunnel’s UUID.
- `your-domain.com` with your actual domain.

---

### **4. Route the Tunnel to a Domain**

You need to configure a **CNAME DNS record** on Cloudflare.

Run:

```bash
cloudflared tunnel route dns my-tunnel your-domain.com
```

This will automatically create a **CNAME record** in your Cloudflare dashboard.

---

### **5. Start and Enable the Tunnel as a Systemd Service**

Run the following command to install the tunnel as a service:

```bash
sudo cloudflared service install
```

Start and enable the service:

```bash
sudo systemctl start cloudflared
sudo systemctl enable cloudflared
```

Check the status:

```bash
sudo systemctl status cloudflared
```

---

### **6. Verify the Setup**

Try accessing your domain (`https://your-domain.com`). It should forward traffic to port **9443**.

If something goes wrong, check logs:

```bash
journalctl -u cloudflared -f
```

---
