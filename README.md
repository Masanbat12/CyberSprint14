# CyberQuest 14

אפליקציית ווב בעברית ללימוד סייבר מאפס ב-14 ימים. כל יום יש שיעור של כ-5 דקות קריאה, ואחריו שתי מעבדות קצרות: באחת תוקפים (Red Team) ובשנייה מגינים (Blue Team). בסוף המסלול יש מבחן מסכם ותעודה.

האתר באוויר: https://cybersprint14.netlify.app

## מה לומדים

הנושאים לפי הסדר: רשתות ו-HTTPS, הצפנה ו-Hashing, סיסמאות ו-MFA, פישינג, הנדסה חברתית, נוזקות, כופרה ותגובה לאירועים, OWASP Top 10, SQL Injection, XSS, CSRF וניהול סשנים, חומות אש והקשחה, ניתוח לוגים ו-SOC, ויום אחרון שמחבר הכל לשרשרת תקיפה אחת.

התוכן נשען על OWASP Top 10:2025, NIST CSF 2.0, NIST SP 800-63B-4, NIST SP 800-61 Rev. 3 ו-MITRE ATT&CK, וכל שיעור מציין את הסעיפים הרלוונטיים.


https://github.com/user-attachments/assets/5e07451e-115c-4e05-9edf-0b7a358757cd



## מה יש באפליקציה

- 28 מעבדות בסימולציה, שתיים לכל יום. בין השאר: פיצוח Hash אמיתי מול רשימת מילים, סימון חבילות שדולפות ברשת Wi-Fi פתוחה, איתור סימנים חשודים במייל פישינג, הזרקת SQL לטופס התחברות, XSS בתגובה לבלוג ו-IDOR. שום דבר לא נשלח לשרת. כל המעבדות רצות בדפדפן.
- סיכום מורחב לכל יום, שנפתח רק למי שרוצה להעמיק. יש בו מילון מונחים, הסבר שלב אחר שלב, הטכניקות מצד התוקף, צ'קליסט הגנה שאפשר לסמן, מיתוסים נפוצים, שאלות לבדיקה עצמית ומקורות. אפשר להדפיס אותו או לשמור כ-PDF.
- ארגז כלים עם בודק חוזק סיסמה לפי NIST, מפרק כתובות URL שמראה מי הבעלים האמיתי של הדומיין, ומחשבון SHA-256.
- XP, שש דרגות, רצף ימים, 12 תגים ורובוט מלווה בשם ביט.
- מבחן מסכם של 21 שאלות. ציון עובר הוא 80, ומי שעובר מקבל תעודה עם השם שלו.
- תזכורת יומית בהתראת דפדפן, בקובץ יומן (`.ics`) או ב-Google Calendar.

ההתקדמות נשמרת ב-LocalStorage, בנפרד בכל דפדפן ומכשיר. כדי לעבור מכשיר מייצאים קובץ במסך ההגדרות ומייבאים אותו במכשיר החדש.

## הרצה מקומית

אין תלויות ואין שלב Build. פותחים את `site/index.html` בדפדפן.

כדי לבדוק את האתר עם אותן כותרות אבטחה שהוא מקבל ב-Netlify, יש שרת קטן שקורא את `site/_headers`:

```
python serve_with_headers.py 8715
```

ואז פותחים את http://localhost:8715

## פרסום

רק התיקייה `site` עולה לאוויר.

ב-Netlify Drop (https://app.netlify.com/drop) גוררים את `site`. לעדכון אתר קיים נכנסים ללשונית Deploys של האתר וגוררים את `site` לאזור שבתחתית העמוד. Netlify Drop מדלג על תיקיות ששמן מתחיל בנקודה, ולכן `security.txt` יושב בשורש של `site`, ו-`_redirects` מפנה אליו מ-`/.well-known/security.txt`.

בחיבור הריפו ל-Netlify דרך GitHub, הקובץ `netlify.toml` כבר מגדיר `publish = "site"`.

באייפון אפשר להוסיף את האתר למסך הבית: פותחים אותו ב-Safari, לוחצים על כפתור השיתוף ובוחרים "הוסף למסך הבית".

## אבטחה

האתר סטטי לגמרי. אין בו ספריות צד שלישי, CDN, עוגיות, מעקב, בקשות רשת, מפתחות API או סודות.

כותרות האבטחה מוגדרות ב-`site/_headers`:

| כותרת | מה היא עושה |
|---|---|
| `Content-Security-Policy` | `default-src 'none'`, וסקריפטים ועיצוב רק מהדומיין עצמו. אין `unsafe-inline` ואין `eval`. |
| `require-trusted-types-for 'script'` | הדפדפן חוסם כתיבה של מחרוזות ל-`innerHTML` ולשאר ה-sinks המסוכנים, גם אם באג כזה ייכנס לקוד בעתיד. |
| `frame-ancestors 'none'` ו-`X-Frame-Options: DENY` | אי אפשר להטמיע את האתר ב-iframe (הגנה מ-Clickjacking). |
| `Strict-Transport-Security` | HTTPS בלבד. בכתובות `netlify.app` הערך בפועל הוא שנה, כי Netlify קובעת אותו בעצמה. |
| `X-Content-Type-Options: nosniff` | הדפדפן לא מנחש סוג קובץ. |
| `Referrer-Policy: no-referrer` | כתובת הדף לא נשלחת לאתרים שמקשרים אליהם. |
| `Permissions-Policy` | מצלמה, מיקרופון, מיקום, תשלומים ושאר היכולות שהאתר לא צריך כבויים. |
| `Cross-Origin-Opener-Policy`, `-Embedder-Policy`, `-Resource-Policy` | בידוד מחלונות ומאתרים אחרים. |

בקוד עצמו כל טקסט נכנס לדף דרך `createElement` ו-`textContent`. אין שימוש ב-`innerHTML`, ב-`eval` או ב-`document.write`. קובץ התקדמות שמייבאים מוגבל ל-200KB, והפונקציה `sanitizeState` שומרת ממנו רק שדות מוכרים עם ערכים תקינים.

### השגיאות ב-Console

בתוכנית החינמית Netlify מזריקה לכל דף סקריפט של תג "Powered by Netlify" (`/.netlify/scripts/hud`), ואי אפשר לכבות אותו בלי לשדרג. הסקריפט כותב HTML ל-`iframe.srcdoc` בלי Trusted Types, וה-CSP חוסם אותו. לכן התג לא מוצג למבקרים, וב-Console מופיעות שלוש שגיאות `TrustedHTML`. החלטתי להשאיר את זה כך ולא להחליש את ה-CSP בשביל התג.

### הוספת משאב חיצוני

משנים את `Content-Security-Policy` בשני מקומות: ב-`site/_headers` ובתגית ה-meta שב-`site/index.html`. הדפדפן אוכף את שתיהן.

| מוסיפים | משנים ב-CSP |
|---|---|
| Google Fonts | `style-src 'self' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com` |
| סקריפט מ-CDN | `script-src 'self' https://cdn.jsdelivr.net`, עם גרסה מדויקת ו-`integrity="sha384-..." crossorigin="anonymous"` |
| Plausible Analytics | `script-src 'self' https://plausible.io; connect-src 'self' https://plausible.io` |
| Netlify Forms | `form-action 'self'` |
| תמונות מאתר אחר | `img-src 'self' data: blob: https://images.example.com` |

`Cross-Origin-Embedder-Policy: require-corp` יחסום משאב חיצוני ששרת המקור שלו לא שולח `Cross-Origin-Resource-Policy: cross-origin`. במקרה כזה מחליפים את הערך ל-`credentialless` או מסירים את הכותרת.

## עריכת התוכן

כל התוכן נמצא ב-`site/app.js`:

- `const DAYS`: השיעורים והמעבדות. ההערה שמעל המערך מתארת את מבנה היום ואת סוגי השלבים במעבדה.
- `const DEEP`: הסיכומים המורחבים, לפי מספר היום. גם כאן ההערה שמעל מתארת את השדות.
- `const EXAM`: שאלות המבחן המסכם.

## מבנה הקבצים

```
learning web/
├── site/                 # התיקייה שעולה לאוויר
│   ├── index.html        # שלד הדף ו-CSP גיבוי בתגית meta
│   ├── styles.css        # העיצוב
│   ├── app.js            # הקוד והתוכן (DAYS, DEEP, EXAM)
│   ├── _headers          # כותרות האבטחה
│   ├── _redirects        # /.well-known/security.txt אל /security.txt
│   └── security.txt      # פרטי קשר לדיווח על חולשות (RFC 9116)
├── netlify.toml          # פריסה מ-GitHub: publish = "site"
├── index.single-file.backup.html  # הגרסה הישנה בקובץ אחד
└── README.md
```

## יוצר

מסנבט מולו (Masanbat Mulu), masanbat12@gmail.com

מצאתם חולשה באתר? אשמח לדיווח פרטי במייל. הפרטים נמצאים גם ב-[security.txt](https://cybersprint14.netlify.app/.well-known/security.txt).
