# 🚀 Flask Vision Board - Git Action & Ansible CI/CD Pipeline

אפליקציית פלאסק (Flask) מעוצבת, צבעונית ואינטראקטיבית המציגה לוח חזון ומסרים חיוביים על מחשבה יוצרת מציאות. 
פרויקט זה מדגים תהליך אוטומציה ו-CI/CD מלא (End-to-End) המשלב את **Jenkins** לבנייה ודחיפה של אימג' דוקר, ו-**Ansible** לפריסה אוטומטית (Deployment) מהירה בשרת היעד.

---

## 📂 מבנה הפרויקט (Project Structure)

```text
├── main5.py                 # קוד האפליקציה בפייתון (Flask)
├── requirements.txt         # חבילות פייתון נדרשות (Flask)
├── Dockerfile               # הוראות הבנייה לאימג' של דוקר
├── Jenkinsfile              # ניהול צינור התהליכים (Pipeline) בג'נקינס
├── inventory.ini            # הגדרת שרתי היעד עבור אנסיבל (Localhost)
├── deploy-playbook.yml      # קובץ הפקודות והמודולים של אנסיבל לפריסה
└── templates/
    └── index.html             # דף ה-HTML הצבעוני והמעוצב
```

---

## 🛠️ דרישות קדם על שרת הג'נקינס (Prerequisites)

כדי שהצינור (Pipeline) ירוץ בהצלחה, יש לוודא שהכלים הבאים מותקנים ומוגדרים על השרת שבו רץ ג'נקינס:

1. **Docker**: מותקן ופעיל. יש לוודא שלמשתמש `jenkins` יש הרשאות להריץ פקודות דוקר:
   ```bash
   sudo usermod -aG docker jenkins && sudo systemctl restart jenkins
   ```
2. **Ansible**: מותקן על השרת:
   ```bash
   sudo apt update && sudo apt install ansible -y
   ```
3. **אוסף ה-Docker של Ansible**: חיוני כדי שאנסיבל יוכל לשלוט בדוקר בצורה מובנית:
   ```bash
   ansible-galaxy collection install community.docker
   ```
4. **ספריית Docker לפייתון**:
   ```bash
   sudo apt install python3-docker -y
   ```

---

## 🔐 הגדרות אבטחה בג'נקינס (Jenkins Credentials)

לפני הרצת הצינור, יש להגדיר את פרטי הגישה ל-DockerHub בתוך ג'נקינס:
1. ניווט אל: **Manage Jenkins** ➔ **Credentials** ➔ **System** ➔ **Global credentials**.
2. לחיצה על **Add Credentials**.
3. בחירת סוג (Kind): **Username with password**.
4. בשדה **Username**: מזינים את שם המשתמש בדוקרהאב (`zehavab`).
5. בשדה **Password**: מזינים את ה-Access Token (או הסיסמה) של דוקרהאב.
6. בשדה **ID**: כותבים בדיוק **`dockerhub-creds`** (זהו המפתח לפיו ה-Jenkinsfile מזהה את הפרטים).

---

## 🔄 שלבי הצינור באוטומציה (Pipeline Stages)

ה-`Jenkinsfile` מנהל ארבעה שלבים מרכזיים באופן אוטומטי בכל לחיצה על **Build Now**:

1. **Build Docker Image**:
   ג'נקינס מושך את הקוד העדכני, קורא את ה-`Dockerfile`, ואורז את אפליקציית ה-Flask לאימג' דוקר מקומי שמתוייג עם מספר הריצה הייחודי (`BUILD_NUMBER`) ועם תג `latest`.
   
2. **Push to DockerHub**:
   ג'נקינס משתמש בכספת המאובטחת (`dockerhub-creds`), מתחבר לחשבון DockerHub של `zehavab` ודוחף את האימג' הארוז לענן לגיבוי והפצה.

3. **Deployment with Ansible**:
   ג'נקינס מפעיל את ה-Playbook של אנסיבל. אנסיבל ניגש לקובץ ה-Inventory, מתחבר לשרת המטרה ומבצע בצורה דקלרטיבית ובטוחה:
   * משיכת (`Pull`) האימג' העדכני ביותר מ-DockerHub.
   * עצירה ומחיקה של הקונטיינר הישן (אם קיים) כדי לשחרר את פורט 5000.
   * הרצת הקונטיינר החדש עם מדיניות הפעלה מחדש אוטומטית (`restart: always`).

4. **Post-Actions (Cleanup)**:
   בסיום התהליך (בין אם הצליח או נכשל), מתבצע ניקוי של האימג'ים המקומיים על שרת הג'נקינס כדי למנוע הצטברות קבצים וניצול מיותר של דיסק, ומבוצעת התנתקות מאובטחת (`docker logout`).

---

## 🌐 בדיקת האפליקציה (Verification)

לאחר שצינור התהליך מסתיים בסטטוס **Success** (צבע ירוק), ניתן לפתוח את הדפדפן ולהיכנס לכתובת:
```text
http://localhost:5000
```
שם יופיע לוח החזון הצבעוני והחיובי שלך, חי, פעיל ורץ בתוך קונטיינר מבודד! 🎉

---
✨ *הפרויקט נבנה במטרה להציג יכולות אפיון, אוטומציה וחיבור בין כלי DevOps מובילים בתעשייה.* ✨
