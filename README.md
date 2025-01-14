# 🔒 Firebase Firestore Security Rules Cheatsheet

## 📋 Basic Structure
```firestore
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Your security rules go here
  }
}
```

## 🔑 Permission Types
### Basic Permissions
```firestore
allow read: if <condition>;      // Read access
allow write: if <condition>;     // Write (create/update/delete)
allow create: if <condition>;    // Create only
allow update: if <condition>;    // Update only
allow delete: if <condition>;    // Delete only
```

## 👤 Authentication Checks
```firestore
// User authentication checks
allow read: if request.auth != null;  // Must be logged in
allow write: if request.auth.uid == userId;  // User can only modify own data
```

## 📊 Data Validation Techniques

### Field Value Checks
```firestore
// Validate specific field values
allow create: if request.resource.data.status == 'active';
allow update: if request.resource.data.age > 18;
```

### Type Validation
```firestore
// Check data types
allow create: if request.resource.data.score is number;
allow update: if request.resource.data.name is string;
```

### Length & Size Constraints
```firestore
// Limit field lengths
allow create: if request.resource.data.description.size() <= 500;
allow write: if request.resource.data.size() < 1024;  // Limit document size
```

## 🔍 Advanced Condition Matching

### Multiple Conditions
```firestore
allow write: if request.auth != null && 
             request.auth.token.email_verified == true &&
             request.resource.data.role == 'user';
```

### Comparing Existing and New Data
```firestore
// Prevent certain fields from changing
allow update: if request.resource.data.createdAt == resource.data.createdAt;
```

## 🛤️ Path-Based Permissions
```firestore
// Specific document path rules
match /users/{userId} {
  allow read, write: if request.auth.uid == userId;
}

// Nested collection rules
match /users/{userId}/posts/{postId} {
  allow read: if request.auth.uid == userId;
}
```

## 🧩 Custom Validation Functions
```firestore
// Define reusable validation functions
function isValidUser() {
  return request.auth != null && 
         request.resource.data.keys().hasAll(['name', 'email']);
}

// Use in rules
allow create: if isValidUser();
```

## 📝 Regex Validation
```firestore
// Email format validation
allow create: if request.resource.data.email.matches(
  '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}$'
);
```

## 🔬 Request Data Access
```firestore
request.auth.uid           // Current user's UID
request.time               // Current server timestamp
request.resource.data      // New data being written
resource.data              // Existing data
```

## 🚦 Role-Based Access Control
```firestore
// Example of role-based permissions
match /articles/{articleId} {
  allow read: if true;  // Public read
  
  allow create: if request.auth.token.role == 'writer';
  
  allow update: if request.auth.token.role == 'admin' || 
                 request.auth.uid == resource.data.authorId;
}
```

## 🛡️ Best Practices
1. Always validate authentication
2. Use principle of least privilege
3. Validate data types and formats
4. Prevent unauthorized data modifications
5. Keep rules simple and clear
6. Test rules thoroughly

## ⚠️ Common Pitfalls to Avoid
- Leaving collections completely open
- Overly permissive rules
- Neglecting authentication checks
- Complex, hard-to-read rules
- Not considering all write operations

## 📚 Learning Resources
- [Official Firebase Security Rules Documentation](https://firebase.google.com/docs/firestore/security/rules-conditions)
- [Firebase Security Rules Playground](https://firebase.google.com/docs/rules/simulator)

## 🔍 Quick Reference
- `request.auth`: Authentication context
- `resource.data`: Existing document data
- `request.resource.data`: New document data
- `get()`: Retrieve document data in rules
- `exists()`: Check document existence

## 📌 Pro Tips
- Use `.hasAny()` instead of `.has()`
- Prefer `in` operator for array checks
- Write granular, specific rules
- Regularly audit and update rules
