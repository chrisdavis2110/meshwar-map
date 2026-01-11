# Security Configuration

## DELETE Endpoint Protection

The `/api/samples` DELETE endpoint is now protected with authentication to prevent unauthorized data deletion.

### Setup Instructions

1. **Generate a secure token:**
   ```bash
   # Generate a random 32-character token
   openssl rand -hex 32
   ```
   
   Example output: `a7f3b9e2c4d1f8e6a9b3c5d7e1f4a8b2c6d9e3f7a1b4c8d2e6f9a3b7c1d5e8f2`

2. **Add to Cloudflare Pages:**
   - Go to your Cloudflare Dashboard
   - Select your Pages project (meshwar-map)
   - Go to **Settings** → **Environment variables**
   - Add a new variable:
     - **Variable name:** `ADMIN_TOKEN`
     - **Value:** Your generated token (paste from step 1)
     - **Environment:** Production (and Preview if needed)
   - Click **Save**

3. **Redeploy the site:**
   - Cloudflare Pages will automatically redeploy with the new environment variable
   - The DELETE endpoint will now require authentication

### Using the DELETE Endpoint

**Before (INSECURE - anyone could do this):**
```bash
curl -X DELETE https://meshwar-map.pages.dev/api/samples
```

**After (SECURE - requires your token):**
```bash
curl -X DELETE https://meshwar-map.pages.dev/api/samples \
  -H "Authorization: Bearer YOUR_ADMIN_TOKEN_HERE"
```

Replace `YOUR_ADMIN_TOKEN_HERE` with the token you set in Cloudflare.

### Testing

**Without token (should fail):**
```bash
curl -X DELETE https://meshwar-map.pages.dev/api/samples
```
Response: `{"error":"Unauthorized: Invalid or missing authentication token"}`

**With valid token (should succeed):**
```bash
curl -X DELETE https://meshwar-map.pages.dev/api/samples \
  -H "Authorization: Bearer a7f3b9e2c4d1f8e6a9b3c5d7e1f4a8b2c6d9e3f7a1b4c8d2e6f9a3b7c1d5e8f2"
```
Response: `{"success":true,"message":"All data cleared"}`

## Important Notes

- **Keep your token secret!** Don't commit it to git or share it publicly
- Store it in a password manager or secure note
- The GET and POST endpoints remain public (anyone can view/upload data)
- Only DELETE requires authentication
- If you lose your token, generate a new one and update it in Cloudflare

## Why This Matters

Without authentication, anyone could run a simple curl command to delete all your wardrive data:
```bash
# This is now BLOCKED! 🛡️
curl -X DELETE https://meshwar-map.pages.dev/api/samples
```

The authentication prevents malicious actors, bots, or accidental deletions from destroying your data.
