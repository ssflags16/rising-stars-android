# Rising Stars School: Android project for Play Store

Your HTML app is wrapped with Capacitor 8 (`www/index.html` is your file, unchanged). The `android/` folder is a complete Android Studio project targeting API 36. Icons and splash screens use the logo from your app.

I could not compile the final `.aab` here (no Android SDK in my workspace), so the project is untested on a device. Build it with one of the two options below.

## Read this first: known issues that can affect approval

1. **No server, so no shared data.** All records live in each phone's local storage. A parent who installs the app will not see their child's attendance or fees, because that data exists only on the phone where the admin typed it. As-is, the app works as a single-device tool (one office phone or tablet). For a real school app where parents log in on their own phones, it needs a backend (for example Firebase or Supabase). I can add that.
2. **Demo accounts.** The login screen lists `admin@school.pk / admin123` and similar, and the app loads sample students on first run. Anyone who installs the app gets admin access to their own copy. Play reviewers often reject apps that ship with demo data or open admin logins. Before submitting, I recommend removing the demo list and sample data.
3. **Exports and printing.** CSV export and receipt print use browser features (blob downloads, `window.print`) that Android WebView usually does not support. I haven't tested them in the app. WhatsApp links should open WhatsApp.
4. **Account deletion.** Play requires apps with sign-up to let users delete their account (in-app and via a web link). Local accounts can be deleted by an admin in the Users screen, but you may need to explain this in the Data safety form.

## Option A: Android Studio (on a PC)
1. Install Android Studio (it includes JDK 21 and the SDK).
2. Open the `android/` folder from this project.
3. Menu: **Build > Generate Signed App Bundle / APK > Android App Bundle**.
4. Create a new keystore (keep the file and passwords safe forever; losing them blocks updates). Choose **release**.
5. The file `android/app/release/app-release.aab` is what you upload to Play.

If you change `www/index.html` later: run `npm install` once, then `npx cap sync android`, then rebuild.

## Option B: GitHub (no PC setup needed; workflow untested)
1. Create a private GitHub repo and upload this whole folder.
2. Create a keystore once (needs Java):
   `keytool -genkeypair -v -keystore release.keystore -alias risingstars -keyalg RSA -keysize 2048 -validity 10000`
3. Convert it to text: `base64 -w0 release.keystore` (on Windows: `certutil -encode release.keystore out.txt`).
4. Repo > Settings > Secrets and variables > Actions, add: `KEYSTORE_BASE64` (the text), `KEYSTORE_PASSWORD`, `KEY_ALIAS` (`risingstars`). The key password must be the same as the keystore password.
5. Actions tab > "Build signed AAB" > Run workflow. Download `rising-stars-aab` when it finishes.

## Upload to Play
1. Create a Play Console developer account ($25, identity verification).
2. Create app, fill the listing from `play-store/store-listing.md`, upload `play-store/play-store-icon-512.png` and `play-store/feature-graphic-1024x500.png`, add phone screenshots.
3. Host `play-store/privacy-policy.html` publicly (GitHub Pages is free) and paste its URL. Fill in YOUR_EMAIL and YOUR_PHONE first.
4. Complete Data safety, Content rating, Target audience and Ads forms.
5. Upload the `.aab` to a closed test. New personal accounts need 12 or more testers for 14 days before applying for production.
6. Before each update, raise `versionCode` in `android/app/build.gradle`.

App ID is `pk.risingstars.school`. It cannot be changed after publishing; edit `capacitor.config.json` and re-create `android/` if you want another.
