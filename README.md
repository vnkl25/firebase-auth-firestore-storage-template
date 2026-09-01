## References

- Firebase Storage - Upload Files  
  https://firebase.google.com/docs/storage/web/upload-files

- Firebase Storage - Download Files  
  https://firebase.google.com/docs/storage/web/download-files

- Firebase Storage - List Files  
  https://firebase.google.com/docs/storage/web/list-files

# Firebase Security Rules Setup

This project uses Firebase Firestore and Storage with secure production rules.
The rules are managed locally and deployed to Firebase using the Firebase CLI.

## Firestore Rules

1. After running firebase init and selecting Firestore, the firestore.rules file is generated.
2. Open firestore.rules and replace the default rules with:
   
```
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    
    match /users/{userId} {
      // Anyone signed in can read, but only the owner can write
      allow read: if request.auth != null;
      allow write: if request.auth != null && request.auth.uid == userId;
    }

    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```
3. Deploy the Firestore rules:
   firebase deploy --only firestore:rules

## Storage Rules

1. After running firebase init and selecting Storage, the storage.rules file is generated.
2. Open storage.rules and replace the default rules with:

```
rules_version = '2';

service firebase.storage {
  match /b/{bucket}/o {

    match /pdfs/{userId}/{allPaths=**} {
      allow read, write: if request.auth != null
                         && request.auth.uid == userId;
    }

    match /{allPaths=**} {
      allow read, write: if false;
    }
  }
}
```

3. Deploy the Storage rules:
   firebase deploy --only storage

## Deploy Both Rules
To deploy both Firestore and Storage rules at the same time:

firebase deploy --only firestore:rules,storage

After making changes to either rule file, run the appropriate deployment command again.
