# 🚀 Next.js + TypeScript + Tailwind CSS Starter Setup

## 📁 Folder Structure
```
client/
├── public/
├── src/
│   ├── app/
│   │   ├── (auth)/                        
│   │   │   ├── _assets/
│   │   │   ├── forgot-password/
│   │   │   ├── reset-password/
│   │   │   ├── sign-in/
│   │   │   ├── sign-up/
│   │   │   ├── auth.global.module.css     # Route-specific global styles
│   │   │   └── layout.tsx                 # Layout specific to auth routes
│   │   │
│   │   ├── (home-page)/
│   │   │   ├── _assets/ 
│   │   │   ├── _components/
│   │   │   │   ├── footer.tsx
│   │   │   │   ├── header.tsx
│   │   │   │   └── hero.tsx
│   │   │   └── page.tsx
│   │   │
│   │   ├── (main-app)/                     # Main app with routes, components, ...
│   │   │
│   │   ├── [...not_found]/                 # Catch-all route for 404
│   │   │   ├── page.tsx
│   │   │   └── layout.tsx                  # Layout for 404 if needed
|   ├── globals.css
|   ├── layout.tsx
│   └── not-found.tsx
│   ├── icons/                              
│   └── utils/                              
│
├── .gitignore
├── eslint.config.js
├── next-env.d.ts
├── next.config.ts
├── package-lock.json
└── package.json
```

---

## 🔳 Global Layout
`./src/app/layout.tsx`
```typescript
import type { Metadata } from "next";
import { Gochi_Hand, Londrina_Shadow } from "next/font/google";
import localFont from 'next/font/local';
import cn from "@/utils/cn";
import "./globals.css";
import Header from "@/components/header";
import Footer from "@/components/footer";


const londrina_shadow = Londrina_Shadow({
  variable: "--font-londrina_shadow",
  weight: ['400'],
  display: 'swap',
  subsets: ["latin"],
});

const gochi_hand = Gochi_Hand({
  variable: "--font-gochi_hand",
  weight: ['400'],
  display: 'swap',
  subsets: ["latin"],
});

const ggSans = localFont({
  src: [
    { path: '../../public/fonts/ggSans-regular.woff2', weight: '400', style: 'normal' },
    { path: '../../public/fonts/ggSans-medium.woff2', weight: '500', style: 'normal' },
    { path: '../../public/fonts/ggSans-semibold.woff2', weight: '600', style: 'normal' },
    { path: '../../public/fonts/ggSans-bold.woff2', weight: '700', style: 'normal' },
  ],
  variable: '--font-ggsans',
  display: 'swap',
});



export const metadata: Metadata = {
  metadataBase: new URL("https://app-name.vercel.app"),
  title: {
    default: "AppName - Short App Description",
    template: "AppName | %s",
  },
  description: "App Description",
  icons: {
    icon: "/logo.png",
  },
  openGraph: {
    title: "AppName - Short App Description",
    description: "App Description",
    images: ["/AppName-og.png"],
  },
  keywords: [],
};


export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body
        className={cn(
          londrina_shadow.variable, gochi_hand.variable, ggSans.variable,
          gochi_hand.className,
          "antialiased min-h-screen flex flex-col items-center text-primary"
        )}
      >
        <Header />
          <main className="flex-grow w-full flex flex-col items-center">
            {children}
          </main>
        <Footer />
      </body>
    </html>
  );
}
```

---

## 🚫 Custom Global 404
`./src/app/[...not_found]/page.tsx`
```typescript
import { notFound } from "next/navigation";


export default function NotFoundCatchAll() {
  notFound()
}
```

`./src/app/not-found.tsx`
```typescript
export default function NotFound() {
  return (
    <h1>Error 404</h1>
  );
}
```

---

## 📚 ClassNames Utility
`./src/app/utils/cn.ts`
```typescript
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";


export default function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

---

## ⭐ Custom SVG React Icons 
`./src/app/icons/icons.tsx`
```typescript
import { SVGProps } from "react";


const Icons = {
  Icon1: (props: SVGProps<SVGSVGElement>) => (
    <svg
      aria-hidden="true"
      role="img"
      xmlns="http://www.w3.org/2000/svg"
      width={24}
      height={24}
      fill="none"
      viewBox="0 0 24 24"
      {...props}
    >
      <path
        fill="currentColor"
        d="..."
      />...
    </svg>
  ),

Icon2: (props: SVGProps<SVGSVGElement>) => (
    <svg
      aria-hidden="true"
      role="img"
      xmlns="http://www.w3.org/2000/svg"
      width={24}
      height={24}
      fill="none"
      viewBox="0 0 24 24"
      {...props}
    >
      <path
        fill="currentColor"
        d="..."
      />...
    </svg>
  ),
...
}


export default Icons;
```
