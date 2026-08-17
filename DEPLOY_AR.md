# دليل نشر SteelFlow ERP (Frontend + Backend + Database)

المشروع Next.js واحد فيه الفرونت والباك مع بعض، فهنرفعه كله مرة واحدة على **Vercel**، وقاعدة البيانات على **Neon** (PostgreSQL مجاني).

---

## الخطوة 1: رفع الكود على GitHub

1. روح على https://github.com/new واعمل ريبو جديد اسمه مثلاً `steelflow-erp` (خليه Private لو المشروع فيه بيانات حساسة).
2. **لا ترفع مجلد المشروع زي ما هو من غير .gitignore** — أنا ضفتلك ملف `.gitignore` جاهز جوه المشروع، هو بيمنع رفع `node_modules` و `.env` و `.next`.
3. من جوه مجلد المشروع نفذ:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/USERNAME/steelflow-erp.git
   git push -u origin main
   ```
   (غيّر `USERNAME` باسم حسابك)

⚠️ **مهم:** ملف `.env` فيه سيكرت تجريبي (`dev-secret-change-in-production...`) — متسبهوش يترفع أبداً، لازم يتحط في إعدادات Vercel بس مش في الكود.

---

## الخطوة 2: إنشاء قاعدة البيانات على Neon

1. روح https://neon.tech وسجل دخول (فيه تسجيل بـ GitHub مباشرة).
2. اعمل **New Project** → اختار اسم زي `steelflow` والمنطقة الأقرب ليك.
3. Neon هيديك **Connection String** شكله كده:
   ```
   postgresql://USER:PASSWORD@ep-xxxxx.region.aws.neon.tech/steelflow?sslmode=require
   ```
4. احتفظ بيه، هتحتاجه في الخطوة الجاية.

---

## الخطوة 3: نشر المشروع على Vercel

1. روح https://vercel.com وسجل دخول بحساب GitHub بتاعك.
2. اضغط **Add New → Project** واختار الريبو `steelflow-erp`.
3. Vercel هيكتشف إنه Next.js تلقائي، سيبه على الإعدادات الافتراضية.
4. قبل الضغط على Deploy، افتح **Environment Variables** وضيف:

   | Key | Value |
   |---|---|
   | `DATABASE_URL` | الكونكشن سترينج من Neon |
   | `NEXTAUTH_SECRET` | قيمة عشوائية جديدة (نفذ `openssl rand -base64 32` على أي جهاز فيه terminal، أو استخدم https://generate-secret.vercel.app/32) |
   | `NEXTAUTH_URL` | هتحطها بعد أول ديبلوي = رابط المشروع اللي هيديهولك Vercel، مثلاً `https://steelflow-erp.vercel.app` |

5. اضغط **Deploy**.

---

## الخطوة 4: تجهيز قاعدة البيانات (migrations + seed)

بعد أول ديبلوي، محتاج تشغّل الـ migrations على قاعدة بيانات Neon. أسهل طريقة من جهازك (لو عندك Node.js مثبت):

```bash
# في مجلد المشروع على جهازك، حط قاعدة بيانات Neon في .env مؤقتاً
npx prisma migrate deploy
npm run db:seed   # اختياري، لو عايز بيانات تجريبية
```

---

## الخطوة 5: تحديث NEXTAUTH_URL

ارجع لإعدادات Vercel → Environment Variables → عدّل `NEXTAUTH_URL` للرابط الحقيقي اللي طلع، وبعدين اعمل **Redeploy** (من تاب Deployments، تلات نقط → Redeploy).

---

## ملاحظات أمان قبل ما تدي حد يستخدم النظام فعلياً

- غيّر `NEXTAUTH_SECRET` لقيمة عشوائية فريدة (متستخدمش القيمة التجريبية اللي في `.env`).
- لو فيه يوزرات seed بباسورد افتراضي، غيّرها فوراً بعد أول تسجيل دخول.
- اعمل الريبو Private لو فيه بيانات عملاء أو تسعير حقيقي.

---

هل حصلت مشكلة في أي خطوة (build error، مشكلة في الـ env، إلخ)؟ ابعتلي رسالة الخطأ وأنا أساعدك تحلها.
