# Lanka Tube

Lanka Tube is a responsive YouTube discovery app. It searches public videos with the YouTube Data API, plays them in YouTube's embedded player, and can share video links live with other signed-in users through Firebase.

## Configure the app

Edit `firebase-config.js`:

```js
window.LANKA_TUBE_CONFIG = {
  youtubeApiKey: "YOUR_RESTRICTED_YOUTUBE_DATA_API_KEY",
  firebase: {
    apiKey: "YOUR_FIREBASE_WEB_API_KEY",
    authDomain: "YOUR_PROJECT.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    appId: "YOUR_FIREBASE_APP_ID"
  }
};
```

These are browser/client settings, not server secrets. Never put a Firebase service-account file, private key, or OAuth client secret in this project. A visitor can enter their own YouTube API key in the account menu instead; that key is saved only in that browser.

### YouTube search

1. In Google Cloud Console, create/select a project and enable **YouTube Data API v3**.
2. Create an API key. Restrict it to the YouTube Data API v3 and HTTP referrers for local development and the final GitHub Pages site.
3. Add it as `youtubeApiKey` in `firebase-config.js`.

Search returns up to 50 results per page. YouTube's API does not provide an endpoint to list every video on the platform; use search and Load more.

### Firebase login and shared links

1. Register a Web app in Firebase Console and copy its web app config into `firebase-config.js`.
2. In **Authentication → Sign-in method**, enable Google and Phone. Add `localhost` and the GitHub Pages host to **Authorized domains**.
3. Create a Firestore database.
4. Publish the rules from `firestore.rules` in **Firestore Database → Rules**. They allow public feed reads, require sign-in to add links, validate YouTube IDs and link fields, and allow authors to remove their own entries only.
5. Phone sign-in uses Firebase's SMS verification and reCAPTCHA; configure the Firebase phone-auth settings and any required billing/quotas in the project.

The Lanka Tube community feed is read in real time. Users share YouTube links; videos remain hosted and played by YouTube.

## Run locally

Serve this folder over HTTP (not `file://`), for example:

```powershell
python -m http.server 4173
```

Then open `http://localhost:4173`.

## Publish on GitHub Pages

1. Before publishing, add the YouTube API key and Firebase web app config to `firebase-config.js`. These values are included in the public website source; never put server credentials or private keys there. Restrict the YouTube key to YouTube Data API v3 and the site's HTTP referrer, such as `https://<username>.github.io/*`.
2. Create a GitHub repository, then upload/push this project to its `main` branch.
3. In the repository, open **Settings → Pages** and choose **GitHub Actions** as the build and deployment source.
4. The workflow in `.github/workflows/pages.yml` deploys the site on every push to `main`. Find the website URL in the workflow run or under **Settings → Pages**.
5. Add the published host (for example, `<username>.github.io`) to Firebase Authentication's **Authorized domains**. Confirm that the Firestore rules from `firestore.rules` are published.
