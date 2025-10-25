# DNS Migration Steps for GitHub Pages

## Step 2: Update www subdomain DNS

Update your DNS records at your domain registrar/DNS provider.

**Current CNAME record:**

```
www.stevestarkemusic.com  →  stevestarkemusic.webnode.page
```

**Change it to:**

```
www.stevestarkemusic.com  →  stevestarkemusic.github.io
```

**How to do this:**

1. Go to your domain registrar's DNS management page (Namecheap, GoDaddy, Cloudflare, etc.)
2. Find the DNS settings/DNS management section
3. Locate the CNAME record for `www`
4. Change the value from `stevestarkemusic.webnode.page` to `stevestarkemusic.github.io`
5. Save the changes

**Verify the change:**

```bash
dig www.stevestarkemusic.com
```

It should show the CNAME pointing to `stevestarkemusic.github.io` instead of webnode addresses.

---

## Step 3: Remove old A records from apex domain

Currently, your apex domain (`stevestarkemusic.com`) has 6 A records - a mix of old and new:

**GitHub Pages IPs (keep these):**

- `185.199.111.153`
- `185.199.108.153`
- `185.199.110.153`
- `185.199.109.153`

**Old host IPs (remove these):**

- `3.125.172.46`
- `3.73.27.108`

**How to do this:**

1. In your DNS provider's settings, find the A records for `stevestarkemusic.com`
2. Delete the A records pointing to `3.125.172.46` and `3.73.27.108`
3. Keep only the 4 GitHub Pages IP addresses
4. Save the changes

**Verify the change:**

```bash
dig stevestarkemusic.com +short
```

You should only see the 4 GitHub Pages IPs.

---

## Step 4: Wait for DNS propagation

DNS changes can take time to propagate across the internet:

- **Typical time:** Minutes to a few hours
- **Maximum time:** Up to 48 hours
- **TTL matters:** The TTL (Time To Live) on your old DNS records determines how long old values are cached

**Check propagation status:**

```bash
# Check your domain
dig stevestarkemusic.com

# Check www subdomain
dig www.stevestarkemusic.com

# Test the actual site
curl -I https://stevestarkemusic.com
curl -I https://www.stevestarkemusic.com
```

You can also use online tools like:

- <https://dnschecker.org>
- <https://whatsmydns.net>

**When it's complete:**

The site should:

- Load without 404 errors
- Show GitHub Pages in the server response
- Both `stevestarkemusic.com` and `www.stevestarkemusic.com` should work

---

## Step 5: Enable HTTPS

After DNS has fully propagated and GitHub Pages can verify your domain ownership, you can enable HTTPS.

**How to enable HTTPS:**

1. Go to your GitHub repository: `https://github.com/stevestarkemusic/stevestarkemusic.github.io`
2. Click on **Settings** (in the repository menu)
3. Click on **Pages** (in the left sidebar)
4. Under "Custom domain", you should see `stevestarkemusic.com` verified
5. Check the box that says **"Enforce HTTPS"**

**Important notes:**

- You **cannot** enable "Enforce HTTPS" until DNS is pointing to GitHub Pages and verified
- If the option is grayed out, wait a bit longer for DNS propagation (can take a few hours)
- GitHub will automatically provision a free SSL certificate from Let's Encrypt
- Certificate provisioning can take a few minutes to a few hours
- Once enabled, GitHub automatically redirects HTTP to HTTPS
- The certificate renews automatically - no maintenance required

**Verify HTTPS is working:**

```bash
# Check that HTTPS works
curl -I https://stevestarkemusic.com

# Should see:
# - HTTP/2 200 (or 301/302 redirect)
# - server: GitHub.com (or similar GitHub Pages server header)
```

Visit your site in a browser and check for the padlock icon in the address bar.
