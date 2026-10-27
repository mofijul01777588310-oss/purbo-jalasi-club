# পূর্ব জালাসী তারুণ্যের স্বেচ্ছাসেবী ক্লাব — ওয়েবসাইট

React + Vite + Supabase ভিত্তিক মোবাইল-ফ্রেন্ডলি ক্লাব ওয়েব অ্যাপ।

## সুবিধা
- ইমেইল/পাসওয়ার্ড দিয়ে রেজিস্ট্রেশন ও লগইন
- নতুন অ্যাকাউন্ট `pending` থাকে; অ্যাডমিন অনুমোদনের পর পোস্ট করতে পারে
- সদস্য প্রোফাইল, ছবি, সদস্য নম্বর ও ডিরেক্টরি
- ছবি-সহ পোস্ট ফিড
- অ্যাডমিন প্যানেল: সদস্য অনুমোদন/প্রত্যাখ্যান/সাসপেন্ড, ভূমিকা বদল, পোস্ট মুছে ফেলা
- Supabase Row Level Security (RLS) নীতিমালা
- ফোন নম্বর সাধারণ সদস্য ডিরেক্টরিতে প্রকাশ করা হয় না
- responsive green/white layout

## ১) Supabase সেটআপ
1. https://supabase.com এ একটি নতুন প্রজেক্ট তৈরি করুন।
2. Project Settings → API থেকে Project URL এবং anon/public key নিন।
3. SQL Editor খুলে `supabase/schema.sql` ফাইলের সম্পূর্ণ SQL চালান।
4. Authentication → Providers → Email চালু রাখুন। প্রোডাকশনে ইমেইল confirmation চালু রাখা ভালো।
5. Authentication → URL Configuration-এ আপনার localhost ও production site URL যোগ করুন।

## ২) প্রথম অ্যাডমিন বানানো
1. ওয়েবসাইটে নিজের ইমেইল দিয়ে রেজিস্ট্রেশন করুন।
2. Supabase Authentication → Users-এ গিয়ে ইমেইল যাচাই হয়েছে কি না নিশ্চিত করুন।
3. SQL Editor-এ নিচের query চালান; ইমেইলটি নিজেরটি দিয়ে বদলাবেন।

```sql
update public.member_accounts
set role = 'admin', status = 'approved', member_no = 'PJT-ADMIN'
where user_id = (select id from auth.users where email = 'YOUR_EMAIL_HERE');
```

এটি শুধু প্রথম অ্যাডমিন তৈরির জন্য। এই query ও service-role key কখনো ওয়েবসাইটের JavaScript-এ রাখবেন না।

## ৩) লোকাল কম্পিউটারে চালানো
Node.js LTS ইনস্টল করুন। প্রজেক্ট ফোল্ডারে:

```bash
npm install
cp .env.example .env
```

`.env` ফাইলে আপনার প্রকৃত Supabase তথ্য দিন:

```env
VITE_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
VITE_SUPABASE_ANON_KEY=YOUR_SUPABASE_ANON_KEY
```

তারপর:

```bash
npm run dev
```

টার্মিনালে দেখানো local URL ব্রাউজারে খুলুন।

## ৪) অনলাইনে প্রকাশ
GitHub-এ প্রজেক্ট আপলোড করে Vercel বা Netlify-এ import করুন। Build command `npm run build`, output directory `dist`। Hosting settings-এ একই `VITE_SUPABASE_URL` ও `VITE_SUPABASE_ANON_KEY` environment variables যোগ করুন। Supabase Auth URL Configuration-এ production domain যোগ করুন।

## গুরুত্বপূর্ণ নিরাপত্তা নোট
- Supabase `anon` key ব্রাউজারে ব্যবহার করা স্বাভাবিক; RLS অবশ্যই চালু থাকতে হবে।
- `service_role` key কখনো frontend, GitHub বা public repository-তে রাখবেন না।
- ফোন নম্বর ও ইমেইল ব্যক্তিগত তথ্য। অ্যাপের ডিরেক্টরিতে ফোন দেখানো হয় না; অ্যাডমিন ভিউতে অনুমোদিত অ্যাডমিন দেখতে পারেন।
- ছবি bucket-এ public URL ব্যবহার করা হয়েছে, তাই আপলোড করা ছবি লিংক জানা থাকলে দেখা যেতে পারে। একান্ত ব্যক্তিগত ছবি রাখবেন না।
- বাস্তব ব্যবহারের আগে আলাদা test account দিয়ে registration, approval, posting, profile editing, admin permissions ও storage policy পরীক্ষা করুন।
- Supabase-এর Free plan, ইমেইল সেটিংস এবং hosting provider-এর সীমা/শর্ত সময়ের সাথে বদলাতে পারে।
