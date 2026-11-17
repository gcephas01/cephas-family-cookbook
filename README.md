# Cephas Family Cookbook

A private family recipe collection backed by Firebase Authentication and Cloud Firestore. Hosted free on GitHub Pages.

## Deploy (no build required)

1. Upload `index.html` and `README.md` to the root of `gcephas01/cephas-family-cookbook` using GitHub's **Add file → Upload files**.
2. Open repository **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, `main`, `/ (root)`, then **Save**.
4. Visit `https://gcephas01.github.io/cephas-family-cookbook/` after GitHub finishes publishing.
5. In Firebase Console → Authentication → Settings → Authorized domains, add `gcephas01.github.io` if it is not already present. Do not include `https://` or the path.
6. Sign in with one of the two Firebase Authentication accounts you created.

## Important privacy and limitations

- The GitHub repository is public, but recipe data lives in Firestore and must be protected by **published Firestore security rules** that restrict access to the two approved account UIDs. Do not upload passwords or service-account keys.
- This version saves recipes in Firestore, and both users can view and edit them. **Meal selections and grocery checkmarks are stored locally in each browser**, so those particular states do not yet sync across phones.
- Grocery quantities combine only when the ingredient name **and** unit match exactly (case-insensitive). `1 cup flour` and `100 g flour` remain separate.
- Photos are optional HTTPS image links; this version does not upload photos or scan handwriting.
- There is no offline editing or version history yet. Keep backup copies of important recipes.
- Firebase config in the website is public client configuration, not an admin secret. Security relies on Firestore rules.

## Suggested Firestore rules

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function family() {
      return request.auth != null && request.auth.uid in [
        "YOUR_UID", "YOUR_WIFES_UID"
      ];
    }
    match /recipes/{recipeId} {
      allow read, write: if family();
    }
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

Replace both UID placeholders and publish. If you previously published broader rules for the entire database, consider narrowing them to `/recipes/{recipeId}` as shown above.
