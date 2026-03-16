# PMP 2FA Authentication

**Version:** 2.0.0  
**Author:** [Fahad Khalid](https://linktr.ee/fahadkhalid211)  
**GitHub:** [fahadkhalid211](https://github.com/fahadkhalid211/)  
**License:** GPLv2 or later  
**Requires:** WordPress 5.0+, PHP 5.6+, Paid Memberships Pro (free version)

---

## What This Plugin Does

PMP 2FA Authentication adds mandatory **Two-Factor Authentication (2FA)** to every login on your Paid Memberships Pro site. After a user enters their username and password correctly, they are taken to a verification screen where they must enter a **One-Time Password (OTP)** before they can access their account.

The OTP can be delivered via:
- **Email** — a branded HTML email sent through WordPress
- **SMS** — a text message sent through Twilio (requires a Twilio account)
- **Both** — the user can choose at login which method they prefer

---

## Features

| Feature | Details |
|---|---|
| Email OTP | Branded HTML email with your site name, sent via WordPress `wp_mail` |
| SMS OTP | Text message via Twilio REST API to any phone number worldwide |
| Both methods | User sees tabs at login to switch between Email and SMS |
| Trust this device | User can skip 2FA for a set number of days on a trusted device |
| Brute-force protection | Max 5 wrong OTP attempts before lockout, new code required |
| Rate limiting | Configurable max OTP sends per hour per user |
| Secure storage | OTPs are hashed with `wp_hash()` — never stored in plain text |
| Works everywhere | Compatible with PMP frontend `[pmpro_login]` shortcode AND `wp-login.php` |
| Revoke trusted devices | Admin can revoke any user's trusted devices; users can revoke their own |
| Admin settings panel | Full settings page with test email/SMS buttons |

---

## Installation

### Step 1 — Upload the Plugin

1. Download the plugin ZIP file
2. In your WordPress admin, go to **Plugins → Add New → Upload Plugin**
3. Choose the ZIP file and click **Install Now**
4. Click **Activate Plugin**

**Or via FTP:**
1. Extract the ZIP
2. Upload the `pmp-2fa-authentication` folder to `/wp-content/plugins/`
3. Go to **Plugins → Installed Plugins** and activate it

---

### Step 2 — Configure Settings

Go to **Settings → PMP 2FA Auth** in your WordPress admin.

#### General Settings

| Setting | Description | Default |
|---|---|---|
| **2FA Method** | Choose Email OTP, SMS OTP, or Both | Email OTP |
| **OTP Length** | Number of digits in the code (4–8) | 6 |
| **OTP Expiry** | How many minutes the code is valid (1–60) | 10 |
| **Max requests/hour** | How many times a user can request a new code per hour | 5 |
| **Trust this device** | Allow users to skip 2FA for N days on a trusted device | On |
| **Trust duration** | How many days a device stays trusted (1–365) | 30 |

#### Email Settings

| Setting | Description |
|---|---|
| **Email Subject** | Subject line of the OTP email |
| **From Name** | The sender name users see in their inbox |
| **From Email** | The sender email address |

> Click **Send Test Email** after saving to confirm emails are working.

---

## Setting Up SMS with Twilio

SMS delivery requires a free or paid [Twilio](https://www.twilio.com/) account. Follow the steps below to get your credentials.

### Step 1 — Create a Twilio Account

1. Go to [twilio.com](https://www.twilio.com/) and click **Sign up**
2. Fill in your details and verify your email address
3. Twilio will give you a **free trial account** with a small credit — enough for testing

### Step 2 — Get Your Account SID and Auth Token

1. After logging in, go to the **Twilio Console** dashboard: [console.twilio.com](https://console.twilio.com/)
2. On the dashboard you will see two values:
   - **Account SID** — looks like `ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
   - **Auth Token** — click the eye icon to reveal it
3. Copy both values — you will paste them into the plugin settings

![Twilio Console Dashboard showing Account SID and Auth Token](https://www.twilio.com/docs/static/img/console-dashboard.png)

### Step 3 — Get a Twilio Phone Number

1. In the Twilio Console, go to **Phone Numbers → Manage → Buy a number**
2. On a trial account, click **Get a trial phone number** (free)
3. Choose a number with **SMS capability**
4. Click **Buy** (free on trial) or confirm the number
5. Your new number will look like `+12025550123`
6. Copy this number — you will paste it as the **From Number** in plugin settings

> **Trial account note:** On a free Twilio trial, you can only send SMS to **verified phone numbers**. Go to **Phone Numbers → Verified Caller IDs** in the Twilio Console and add the phone numbers you want to test with.

### Step 4 — Enter Credentials in the Plugin

1. In WordPress, go to **Settings → PMP 2FA Auth**
2. Set **2FA Method** to **SMS only** or **Both**
3. Scroll to the **SMS Settings (Twilio)** section
4. Fill in:
   - **Account SID** — paste your SID from the Twilio Console
   - **Auth Token** — paste your Auth Token
   - **From Number** — paste your Twilio phone number in E.164 format (e.g. `+12025550123`)
5. Click **Save Settings**
6. Click **Send Test SMS** to confirm everything is working

> **E.164 format** means: country code + number with no spaces or dashes. Examples:
> - US: `+12025550123`
> - UK: `+447911123456`
> - Pakistan: `+923001234567`

### Step 5 — Add Phone Numbers to User Profiles

For SMS to work, each user needs a phone number saved on their account.

1. Go to **Users → All Users** and click **Edit** on a user
2. Scroll down to the **Two-Factor Authentication** section
3. Enter their phone number in E.164 format
4. Click **Update User**

Users can also edit their own phone number by going to **Users → Profile** in their admin or visiting their profile page.

> **If a user has no phone number saved**, the plugin automatically falls back to Email OTP so they are never locked out.

---

## How It Works for Users

1. User visits your login page (e.g. `yoursite.com/login`) and enters their username and password
2. The plugin intercepts the login and sends an OTP to their email or phone
3. The page automatically redirects to a verification screen
4. User enters the 6-digit code from their email/SMS
5. If correct, they are logged in and redirected to `/membership-account/`
6. If they enabled "Trust this device", they won't need to verify again for the configured number of days

---

## Trusting Devices

When the **Trust this device** option is enabled in settings, users will see a checkbox on the verification screen:

> ☑ Trust this device for 30 days

If checked and the OTP is verified successfully, that device is remembered. The user will be logged in directly without 2FA for the next 30 days (or however many days you configured).

Trusted device tokens are:
- Stored as a secure cookie (HttpOnly, SameSite=Strict)
- Bound to the user's browser/device fingerprint
- Automatically expire after the configured number of days

---

## Revoking Trusted Devices

### As an Admin

Go to **Settings → PMP 2FA Auth → Revoke Trusted Devices**:

- **Revoke My Devices** — clears all trusted devices for your own account
- **Revoke for a specific user** — type a username, email, or user ID → the plugin shows the user's name and how many trusted devices they have → click **Revoke**

### As a Regular User

Any user can revoke their own trusted devices from their WordPress profile:

1. Go to **Users → Profile** (or the WordPress admin profile page)
2. Scroll to the **Two-Factor Authentication — Trusted Devices** section
3. Click **Revoke All Trusted Devices**

After revoking, the user will need to complete 2FA verification on their next login.

---

## Frequently Asked Questions

**Does this work with the PMP frontend login shortcode?**  
Yes. It works with `[pmpro_login]` on any frontend page as well as the standard `wp-login.php` page.

**What if the OTP email goes to spam?**  
Check your **Email Settings** — make sure the From Name and From Email are set correctly. Consider using a dedicated email service like SendGrid, Mailgun, or Amazon SES with a WordPress SMTP plugin such as WP Mail SMTP for better deliverability.

**What happens if Twilio credentials are not set?**  
If SMS is selected but credentials are missing, the plugin automatically falls back to Email OTP. A yellow warning notice will appear in your WordPress admin until credentials are added.

**What if a user enters the wrong OTP too many times?**  
After 5 incorrect attempts, the OTP is invalidated and the user must request a new code using the **Resend Code** button.

**Can a user get locked out completely?**  
No. They can always request a new code via **Resend Code**, and if SMS fails the plugin falls back to email. If they've lost access to their email too, an admin can manually log them in or reset their password.

**Is this plugin compatible with caching plugins?**  
Yes. The 2FA state is managed via server-side WordPress transients and cookies, so page caching does not interfere.

**Where are OTP codes stored?**  
OTPs are never stored in plain text. They are immediately hashed using WordPress's `wp_hash()` function and stored as a transient. Even if your database were compromised, the raw OTP codes could not be recovered.

---

## Security Notes

- OTPs are generated using `random_int()` (cryptographically secure)
- Codes are hashed with `wp_hash()` before storage
- Timing-safe comparison with `hash_equals()` prevents timing attacks
- Brute-force protection: 5 attempts per code, then lockout
- Rate limiting: configurable max sends per hour
- Trusted device cookies: HttpOnly, SameSite=Strict, HTTPS-only on SSL sites
- Trusted device tokens are bound to browser user-agent

---

## Changelog

### 2.0.0
- Complete architecture rewrite for full PMP shortcode compatibility
- OTP dispatch moved to `template_redirect` for reliable `wp_mail` delivery
- State management via transients + cookies (no PHP sessions)
- SMS falls back to email automatically if Twilio credentials missing
- Admin warning notices when SMS is selected but not configured
- Revoke trusted devices for any user from admin settings
- Users can revoke their own devices from their profile page
- Full WordPress Plugin Check compliance

### 1.0.0
- Initial release

---

## Support

- **GitHub:** [github.com/fahadkhalid211](https://github.com/fahadkhalid211/)
- **Author:** [linktr.ee/fahadkhalid211](https://linktr.ee/fahadkhalid211)
