# בינה לי

האתר המלווה לסדנת הבינה המלאכותית — פיד כלים, פרומפטים, יצירות משותפות ולייקים.

האתר הוא קובץ `index.html` יחיד (התמונות מוטמעות בו), והנתונים נשמרים ב-**Firebase Firestore**.

## הפעלה ראשונה של Firebase (חד-פעמי)

יש לעבור בקונסולת Firebase של הפרויקט `bina-li`:

### 1. יצירת Firestore

1. נכנסים ל־[console.firebase.google.com](https://console.firebase.google.com) ובוחרים את הפרויקט **bina-li**.
2. בתפריט הצד: **Build → Firestore Database → Create database**.
3. בוחרים **Production mode** (לא Test mode).
4. בוחרים אזור (Location), למשל `europe-west1`, ומאשרים.

### 2. הפעלת אימות אנונימי

1. **Build → Authentication → Get started**.
2. בכרטיסיית **Sign-in method** לוחצים על **Anonymous**.
3. מפעילים (Enable) ושומרים.

האתר משתמש בכניסה אנונימית רק כדי לזהות מכשיר/דפדפן לצורך מניעת לייק כפול. אין טופס הרשמה ואין צורך בסיסמה.

### 3. פרסום חוקי האבטחה

1. **Build → Firestore Database → Rules**.
2. מוחקים את התוכן הקיים ומדביקים את התוכן של הקובץ [`firestore.rules`](firestore.rules).
3. לוחצים **Publish**.

> אין להשאיר את החוקים במצב `allow read, write: if true`. זה מאפשר לכל מבקר באתר לשנות או למחוק את כל הפיד.

### 4. הוספת הדומיין של GitHub Pages

1. **Build → Authentication → Settings → Authorized domains**.
2. מוסיפים: `talshor-git.github.io`
3. אם בעתיד יחובר דומיין משלכם — מוסיפים גם אותו.

### 5. טעינה ראשונה

פותחים את האתר. בפעם הראשונה, אם אוסף `posts` ריק, האתר זורע אוטומטית את 8 הפוסטים הראשונים מתוך `SEED` שבקוד.

אם מופיע "הפיד לא נטען":
- פותחים את כלי המפתחים (F12) ובודקים את ה-Console.
- `auth/configuration-not-found` = אימות אנונימי לא הופעל.
- `permission-denied` = חוקי Firestore לא פורסמו.
- `not-found` / `Failed to get document` = מסד הנתונים עוד לא נוצר.

## מבנה הנתונים

אוסף אחד: `posts`. כל מסמך הוא פוסט:

```
posts/{postId}
  type: "tool" | "prompt"
  created: number (מילישניות)
  title, desc, url, image, usage, snippet: string
  tags: string[]
  likes: number
  likedBy: string[]          // uid-ים שכבר עשו לייק
  links: [                   // "תגובות" — יצירות משותפות
    { id, name, url, title, likes, likedBy: string[], created, createdBy }
  ]
  createdBy: string          // uid של היוצר
```

- **פוסט** = מסמך ב-`posts`.
- **תגובה / יצירה** = איבר במערך `links` של הפוסט.
- **לייק** = `likes` בתוספת ה-uid של המשתמש ב-`likedBy`, כדי שלא ייספר פעמיים.
- העדכונים מגיעים בזמן אמת דרך `onSnapshot`, בלי רענון עמוד.

## מחיקת תוכן

מכיוון שהאתר לא מאפשר מחיקה מהדפדפן, מוחקים פוסטים או יצירות ישירות בקונסולה:
**Firestore Database → posts → בוחרים מסמך → Delete**.

## עדכון האתר

```bash
git add -A
git commit -m "update"
git push
```

GitHub Pages מתעדכן אוטומטית תוך דקה־שתיים.

## אחסון ותמחור

בפרויקט קטן של סדנה, השימוש צפוי להישאר בתוך המכסה החינמית של Firebase (Firestore ו-Authentication). אין צורך בכרטיס אשראי עבור המכסה החינמית, אך מומלץ לעקוב אחרי **Usage** בקונסולה.
