# LabourLaw.ge — გამოქვეყნება GitHub + Cloudflare Pages-ით

საიტი სუფთა სტატიკური HTML-ია. სქემა ასეთია:

```
ლოკალური ფაილები ──git push──▶ GitHub (main)  ──ავტომატურად──▶ Cloudflare Pages ──▶ labourlaw.ge
```

ერთხელ დაყენების შემდეგ **ყოველი `git push` ავტომატურად აქვეყნებს ცვლილებებს**
საიტზე 1–2 წუთში. Hostinger-ზე ფაილების ატვირთვა აღარ სჭირდება.

---

## ნაწილი 1 — GitHub (ერთჯერადი)

1. თუ GitHub ანგარიში არ გაქვს: https://github.com/signup
2. ტერმინალში (ამ საქაღალდეში) გაუშვი:
   ```
   gh auth login
   ```
   აირჩიე: `GitHub.com` → `HTTPS` → `Login with a web browser`.
   ბრაუზერში ჩაწერე კოდი, რომელსაც ტერმინალი გაჩვენებს.
3. რეპოზიტორიის შექმნა და ატვირთვა:
   ```
   gh repo create labourlaw.ge --private --source=. --remote=origin --push
   ```
   (რეპოზიტორია GitHub-ზე: `gatenashvili/labourlaw.ge`)

---

## ნაწილი 2 — Cloudflare (ერთჯერადი)

### 2.1 ანგარიში და დომენის დამატება
1. https://dash.cloudflare.com/sign-up — შექმენი უფასო ანგარიში.
2. Dashboard → **Add a domain** → ჩაწერე `labourlaw.ge` → **Free** გეგმა → Continue.
3. Cloudflare გაჩვენებს **2 nameserver-ს** (მაგ. `xxx.ns.cloudflare.com` და `yyy.ns.cloudflare.com`).
   ჩაიწერე ეს ორივე.

### 2.2 Nameserver-ების შეცვლა domenebi.ge-ზე
1. შედი https://domenebi.ge → ჩემი დომენები → `labourlaw.ge` → **DNS / Nameservers**.
2. წაშალე Hostinger-ის nameserver-ები (`ns1.dns-parking.com`, `ns2.dns-parking.com` ან მსგავსი).
3. ჩაწერე Cloudflare-ის ორი nameserver.
4. შეინახე. გავრცელებას სჭირდება 10 წუთიდან 24 საათამდე.
   Cloudflare-ის Dashboard-ში დომენი „Active" გახდება, როცა მზად იქნება.

### 2.3 Pages პროექტის შექმნა
1. Cloudflare Dashboard → **Workers & Pages** → **Create application**.
2. ახალი ინტერფეისი ჯერ Workers-ის ვარიანტებს გაჩვენებს. ბარათის ქვეშ დააჭირე
   **Continue to Pages** („Need to use the legacy Pages workflow?").
3. **Import an existing Git repository** → **Get started** → GitHub.
4. დააკავშირე GitHub ანგარიში და აირჩიე რეპოზიტორია `labourlaw.ge` → **Begin setup**.
5. პარამეტრები:
   - Project name: `labourlaw-ge`
   - Production branch: `main`
   - Framework preset: `None`
   - Build command: **ცარიელი** (არაფერი)
   - Build output directory: **ცარიელი**
6. **Save and Deploy**. 1 წუთში მიიღებ მისამართს `labourlaw-ge.pages.dev`.

### 2.4 დომენის მიბმა Pages-ზე
1. Pages პროექტი → **Custom domains** → **Set up a custom domain** → `labourlaw.ge` → Activate.
2. გაიმეორე `www.labourlaw.ge`-სთვისაც.
3. თუ სტატუსი „Verifying"-ზე ჩერდება და **Complete DNS setup** უწერია, DNS-ში ძველი
   (Hostinger-ის) ჩანაწერები უშლის ხელს. **Domains → labourlaw.ge → DNS → Records**:
   - წაშალე `@`-ის და `www`-ის **A** / **AAAA** ჩანაწერები (MX და TXT არ შეეხო).
   - დაამატე **CNAME** `@` → `labourlaw-ge.pages.dev` (Proxy ჩართული).
   - დაამატე **CNAME** `www` → `labourlaw-ge.pages.dev` (Proxy ჩართული).
   - Custom domains-ში `⋯` → **Retry**. რამდენიმე წუთში ორივე „Active" გახდება.
4. სასურველია: Domains → labourlaw.ge → **SSL/TLS** → **Edge Certificates** → **Always Use HTTPS: On**.

---

## ყოველდღიური სამუშაო პროცესი

ფაილი შეცვალე (ან Claude-ს სთხოვე), შემდეგ:

```
git add -A
git commit -m "რა შეიცვალა"
git push
```

1–2 წუთში ცვლილება ცოცხალ საიტზეა. სტატუსის ნახვა: Cloudflare → Workers & Pages → labourlaw-ge → Deployments.

## რა არის რეპოზიტორიაში

- `*.html` — საიტის გვერდები (ყველა CSS/JS და სურათი ჩაშენებულია ფაილებშივე).
- `404.html` — Cloudflare Pages ამას ავტომატურად აჩვენებს არარსებულ მისამართებზე.
- `.gitattributes` / `.gitignore` — git-ის ტექნიკური პარამეტრები.
