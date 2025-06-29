
## 🚀 Deploy Dynamic Next.js 15 App to cPanel (Node.js - Standalone Method)

This guide explains how to deploy a **dynamic Next.js 15 App Router project** to **cPanel** using a **custom Node.js server** and the `standalone` output build.

---

### ✅ Prerequisites

* A working **Next.js 15 project**
* Access to **cPanel** with **Node.js App support**
* Node.js version **18+**

---

### 🛠 Step 1: Project Setup (Locally)

> 📦 Make sure everything is installed before building or deploying.

---

#### 1. Update `next.config.js`

Add the following to enable standalone output:

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'standalone', // ✅ added this line for cPanel deployment
};

module.exports = nextConfig;
```

> 📦 This tells Next.js to bundle the app into `.next/standalone` for a minimal Node.js runtime.

---

#### 2. Create `server.js` of server.ts

This file runs the app using Node.js:

```js
import { createServer } from 'http';
import { parse } from 'url';
import next from 'next';

const port = parseInt(process.env.PORT || '3000', 10);
const dev = process.env.NODE_ENV !== 'production';
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  createServer((req, res) => {
    const parsedUrl = parse(req.url, true);
    handle(req, res, parsedUrl);
  }).listen(port);

  console.log(
    `> Server listening at http://localhost:${port} as ${
      dev ? 'development' : process.env.NODE_ENV
    }`
  );
});
```

> ⚙️ Required for custom deployment. This is your main entry point for Node.js.
> ⚠️ If you're using `server.ts`, make sure to convert the example to TypeScript. You can also visit the official guide on [Custom Server](https://nextjs.org/docs/app/guides/custom-server), copy the example ts code, and adapt it as needed.

---

#### 3. Update `package.json` Scripts

Add a custom start command for production:

```json
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start:custom": "NODE_ENV=production node server.js" // ✅ added for custom deployment
}
```

> ▶️ Run with: `npm run start:custom`

---

### 🔨 Step 2: Preparing the Project for cPanel Deployment

> 📁 This step ensures your app is correctly bundled with all required assets, ready to be uploaded to your cPanel server.

---

#### 1. Build the Project

Run the build command to generate the production-ready output:

```bash
npm run build
```

> 🔨 This produces the `.next/standalone` folder containing your app’s minimal Node.js runtime.
> ⚠️ However, **public assets and CSS files are not included automatically**, so styles and static files won’t work correctly yet.

---

#### 2. Prepare the Deployment Folder to Include CSS & Public Assets

To fix missing styles and static files, manually copy the necessary folders into the standalone build:

```
/standalone/
├── .next/                    ← From: .next/standalone/.next
│   ├── static/               ← ✅ Copy manually from: .next/static (CSS, JS, fonts)
│   └── server/               ← Compiled server files
├── node_modules/             ← From: .next/standalone/node_modules
├── package.json              ← From: .next/standalone/package.json
├── server.js                 ← Your custom Node.js server file
├── public/                   ← ✅ Copy manually from your project root
```

> ✅ Copying `.next/static` and `public` folders ensures all CSS, images, fonts, and other assets are available for your app after deployment, enabling proper styling and routing.

---

#### 3. Create Deployment Zip

Compress the `/standalone` folder into a `.zip` file, **excluding the `node_modules` directory**.

---

### Step 3: Set Up Node.js App in cPanel

1. Go to **cPanel > Setup Node.js App**
2. Click **Create Application**

   * Node version: **18+**
   * App Mode: **Production**
   * App Root: `nextjs15-app`
   * Startup file: `server.js`
3. Run `npm install` inside the app directory
4. Click **Run Application**

---

### 🌐 Your app is now live!

All pages, CSS, API routes, and images should now work perfectly.

> ✅ This is the recommended way to deploy dynamic Next.js 15 apps on shared hosting like cPanel.

---

### 📘 Reference

* Custom Server: [https://nextjs.org/docs/app/guides/custom-server](https://nextjs.org/docs/app/guides/custom-server)
* Output: Standalone: [https://nextjs.org/docs/pages/api-reference/next-config-js/output](https://nextjs.org/docs/pages/api-reference/next-config-js/output)

---
