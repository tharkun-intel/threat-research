# Analysis: Gcash Clickjacking Campaign in Facebook Messenger

**Date:** 2026-01-12  

**Type:** Phishing / Viral Scam / Clickjacking

**Target:** Philippines (Gcash Users)

**TLP:** CLEAR

---

## 📌 TL;DR
* **Campaign:** A viral phishing scam targeting GCash users in the Philippines via Facebook Messenger, promising a fake "₱2,000 Anniversary Gift."
* **The Trap:** The site uses a "loop" script and history manipulation to prevent users from clicking "Back," forcing them to spam the link to 5-10 friends to "unlock" the prize.
* **Social Engineering:** Uses fake live comments, "winner" popups, and confetti effects to create false urgency and trust.
* **Code Origins:** The toolkit appears to be a "Scam-as-a-Service" template likely purchased on the dark web.
    * **Russian comments** were found in the source code.
    * **"Digital fossils"** (commented-out code) revealed previous campaigns targeting **India and Pakistan** (Jio, Airtel, Jazz) via WhatsApp.
* **Infrastructure:**
    * **Registrar:** West263 International (China), known for abuse-friendly bulk registrations.
    * **Analytics:** Uses Plausible Analytics to track victims while evading ad-blockers.
    * **Hosting:** Images are served via `599cdn[.]com` rather than official GCash servers.
* **Goal:** The phone number input is a dummy; the real goal is to drive massive traffic to an affiliate redirect (`go.php`) for ad fraud, gambling sites, or malware.
---

## 📜 The Story

It started with a notification from a friend on Facebook Messenger. The message promised a 'GCash Grand Giveaway'—a free ₱2,000 (~34 USD) anniversary gift for all users.

This immediately caught me off guard. I don't usually receive messages from people giving me money; it's usually the other way around (looking at you, bayad utang reminders). Naturally, as a curious (and broke) trentahin, I had no choice but to click the link and 'claim' my fortune.

<p align="center">
  <img width="507" height="334" alt="Phishing Message" src="https://github.com/user-attachments/assets/666f7137-d2fd-4170-9f94-3fc773382ef7" />
  <br>
  <em>Figure 1: The "GCash" phishing message I received.</em>
</p>

## 🕵️‍♂️ The Investigation

**Domain Analysis**

From the message, I got the domain below:  

`hxxps[:]//gcash6[.]c3se[.]xyz/?anniversary-giveaway=24`

I chucked the domain into URLScan to see where it lands and if there was any cached content (site screenshots, HTML, JS code). I found a screen capture of the scam site, its HTML file, and the JS scripts that the site loads. The domain mimics 'GCash', a popular e-wallet in the Philippines with millions of users. The domain was registered via `West263 International Limited`. They have a bit of a reputation, appearing regularly in abuse reports for bulk registrations on .shop and .top domains. Geolocation? China. 

<p align="center">
  <img width="1160" height="643" alt="image" src="https://github.com/user-attachments/assets/c0d0dbd9-ea39-4fe3-967a-229c397b8df0" />
  <br>
  <em>Figure 2: HMTL file and the javascript codes found in URLScaa.</em>
</p>

<p align="center">
  <img width="909" height="680" alt="image" src="https://github.com/user-attachments/assets/a249fb92-f6ac-404b-87f1-ac48735dc29b" />
  <br>
  <em>Figure 3: Screenshot of the domain, suspect details highlighted.</em>
</p>

<p align="center">
  <img width="812" height="862" alt="image" src="https://github.com/user-attachments/assets/e7a99daf-711e-41dc-9b4e-10fbc9142359" />
  <br>
  <em>Figure 4: Whois Info of the domain.</em>
</p>


**Code Analysis**

Analysis of the HTML file reveals the page is titled 'GCash ₱2000 Anniversary Grand Giveaway for All Users' and contains logic designed to trap users in a 'loop.'

Even if a victim hits the 'Back' button, a coding trick prevents them from leaving the page. Instead, the script forces them to spam different contacts on Facebook Messenger to reach '100%' on a progress bar. To build trust, the page displays hardcoded fake notifications and comments, complete with 'winner' popups and confetti effects. This core 'loop' logic was hidden within an obfuscated script. 

Based on the code, the goal is not to steal data directly. The phone number input is merely a dummy; the real money is in the traffic. By forcing a viral loop, the attacker generates massive volume for their final redirection (go.php), likely funneling victims to a gambling site, predatory loan app, or malware download.

<p align="center">
  <img width="1531" height="867" alt="image" src="https://github.com/user-attachments/assets/f6f19432-24f9-4f5f-a2dd-82f87c6f750b" />
  <br>
  <em>Figure 5: Deobfuscated code containing the "loop" logic.</em>
</p>

| Indicator | Type | Description |
| :--- | :--- | :--- |
| **Fake Domain** | URL | The page references `go.php` multiple times. Legitimate GCash promotions are hosted on `gcash.com`. |
| **Obfuscated JS** | Code | Usage of `unescape('%20...')` to hide the logic for the sharing requirement. |
| **Fake CDN** | Network | Images are loaded from `599cdn.com` and `cdnmi.com`, which are often associated with spam/scam landing pages, not official GCash assets. |
| **Deep Linking** | Protocol | Uses `fb-messenger://share/?link=` to trigger the mobile Messenger app directly, bypassing browser protections. |
| **Redirect Loop** | Behavior | `window.location.href = REDIRECT_URL` inside a timer ensures the user is moved to the final destination even if they do nothing. |
<p align="center">
  <br>
  <em>Table 1: Function contained within the HTML file.</em>
</p>

**Attribution & Artifacts** 

Scattered throughout the file are instructions written in Russian, suggesting the scam toolkit is Russian-made—likely purchased via a Telegram channel or dark web forum.

I also found a 'digital fossil': a commented-out text block targeting India and Pakistan. This block listed carriers like Jio, Airtel, and Jazz, formatted specifically for WhatsApp (using asterisks for bolding). This confirms that the toolkit isn't exclusive to the Philippines; it has likely been deployed in those regions as well, modified to target WhatsApp users instead of Facebook Messenger."

<p align="center">
  <img width="493" height="395" alt="image" src="https://github.com/user-attachments/assets/1bfd0b01-e809-4f44-91b3-ad1284a2389d" />
  <br>
  <em>Figure 6: Leftover notes in Russian, indicating that the scam toolkit is russian-made.</em>
</p>

<p align="center">
  <img width="1532" height="872" alt="image" src="https://github.com/user-attachments/assets/6d8db035-68f5-4a9a-bb31-f7bbc64574d7" />
  <br>
  <em>Figure 7: 'digital fossil' in the HTML file.</em>
</p>

## 🛡️ Indicators of Compromise (IOCs)

| Type | Value | Context |
| :--- | :--- | :--- |
| **Domain** | `gcash6.c3se[.]xyz` | **Landing Page:** The initial phishing URL clicked by the victim. |
| **Domain** | `gcash8.vayv[.]top` | **Canonical Domain:** The URL used for generating new share links (hardcoded in JS). |
| **Domain** | `tj.16gift[.]com` | **C2 / Analytics:** The "Traffic Jungle" domain used to track victim stats via Plausible. |
| **Domain** | `599cdn[.]com` | **Asset Hosting:** Hosting the fake GCash banners and social preview images. |
| **URL Path** | `/go.php` | **Redirector:** The script that redirects the user to the final payload (gambling/scam site). |
| **URL Path** | `/api/event` | **Beacon:** The endpoint receiving the analytics data. |
| **Signature** | `ph-gcash01` | **Campaign ID:** Found in the `data-domain` attribute; identifies this specific Philippines/GCash campaign. |
| **Signature** | `West263 International Limited` | **Registrar:** The organization used to register the phishing domains (known for abuse). |

### 🚫 Blocklist (Raw Text)
```text
gcash6.c3se[.]xyz
gcash8.vayv[.]top
tj.16gift[.]com
599cdn[.]com
