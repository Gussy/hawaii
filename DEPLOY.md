# Deploying to Cloudflare Pages with Zero Trust Access

## Overview

This guide covers deploying the Hawaii trip itinerary as a **private** site using:

- **Cloudflare Pages** for hosting (connected to your GitHub repo for automatic deploys)
- **Cloudflare Zero Trust Access** to restrict access to only you and your girlfriend

## Prerequisites

- A [Cloudflare account](https://dash.cloudflare.com/sign-up) (free plan works)
- The app pushed to a GitHub repository
- Your girlfriend's email address for the Access policy

## 1. Push to GitHub

Make sure your code is pushed to GitHub:

```bash
git remote add origin git@github.com:YOUR_USERNAME/hawaii.git
git push -u origin main
```

## 2. Deploy to Cloudflare Pages

1. Go to [Cloudflare Dashboard](https://dash.cloudflare.com) → **Workers & Pages**
2. Click **Create** → **Pages** → **Connect to Git**
3. Authorize Cloudflare to access your GitHub account
4. Select the `hawaii` repository
5. Configure build settings:
   - **Project name**: `hawaii` (this determines your URL: `hawaii.pages.dev`)
   - **Production branch**: `main`
   - **Framework preset**: None
   - **Build command**: *(leave empty)*
   - **Build output directory**: `/`
6. Click **Save and Deploy**

Your site will deploy to `https://hawaii.pages.dev` (or whatever project name you chose).

> Every push to `main` will trigger an automatic redeploy.

## 3. Set Up Zero Trust Access

This gates your site behind authentication so only approved email addresses can view it.

### 3a. Enable Zero Trust

1. Go to [Cloudflare Dashboard](https://dash.cloudflare.com) → **Zero Trust** (left sidebar)
2. If prompted, set up your Zero Trust organization (free plan supports up to 50 users)
3. Choose a team name (e.g., `your-name`) — this creates `your-name.cloudflareaccess.com`

### 3b. Create an Access Application

1. In Zero Trust dashboard → **Access** → **Applications**
2. Click **Add an application** → **Self-hosted**
3. Configure:
   - **Application name**: `Hawaii Trip`
   - **Session duration**: `24 hours` (or your preference)
   - **Application domain**:
     - Subdomain: `hawaii`
     - Domain: `pages.dev`
     - (This should match your Pages URL: `hawaii.pages.dev`)
4. Click **Next**

### 3c. Create an Access Policy

1. **Policy name**: `Allowed travelers`
2. **Action**: Allow
3. Under **Configure rules** → **Include**:
   - **Selector**: `Emails`
   - **Value**: your email address
   - Click **+ Add include** → **Selector**: `Emails` → **Value**: your girlfriend's email
4. Click **Next** → **Add application**

### 3d. How It Works

By default, Cloudflare Access uses **One-time PIN (OTP)** authentication:

1. When someone visits `hawaii.pages.dev`, they see a Cloudflare Access login page
2. They enter their email address
3. If their email is in the allow list, they receive a one-time code via email
4. They enter the code and get access for the session duration (e.g., 24 hours)

No passwords, no OAuth setup — just email verification.

## 4. Restrict the Google Maps API Key

The app includes a Google Maps Embed API key. You should restrict it to your Pages domain:

1. Go to [Google Cloud Console](https://console.cloud.google.com) → **APIs & Services** → **Credentials**
2. Find your Maps Embed API key
3. Under **Application restrictions**:
   - Select **HTTP referrers (websites)**
   - Add: `hawaii.pages.dev/*`
   - Also add `*.hawaii.pages.dev/*` (for preview deployments)
4. Under **API restrictions**:
   - Select **Restrict key**
   - Enable only **Maps Embed API**
5. Click **Save**

## 5. Custom Domain (Optional)

If you want a custom domain instead of `*.pages.dev`:

1. In Cloudflare Pages → your project → **Custom domains**
2. Click **Set up a custom domain**
3. Enter your domain (e.g., `trip.yourdomain.com`)
4. Follow the DNS setup instructions
5. Update your Zero Trust Access application domain to match

## Troubleshooting

### "Access Denied" after setting up Zero Trust

- Verify the application domain exactly matches your Pages URL
- Check that the email addresses in the policy are correct
- Try clearing cookies or using an incognito window

### Girlfriend not receiving OTP email

- Check spam/junk folder
- Verify the email address is spelled correctly in the Access policy
- OTP emails come from `noreply@cloudflareaccess.com`

### Changes not deploying

- Ensure you pushed to the `main` branch
- Check the Cloudflare Pages dashboard for build status and logs
