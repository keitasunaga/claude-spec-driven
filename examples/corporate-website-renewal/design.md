--------------------------------------------------------------------------------
# Project Design: Corporate Website Renewal / プロジェクト設計: 企業ホームページリニューアル

## 1. Overview / 概要
This document provides the technical design specification for the Corporate Website Renewal project using Next.js. It defines the modern, scalable architecture leveraging Next.js 14's App Router, server components, and edge runtime capabilities.

本文書は、Next.jsを使用した企業ホームページリニューアルプロジェクトの技術設計仕様を提供します。Next.js 14のApp Router、サーバーコンポーネント、エッジランタイム機能を活用した、モダンでスケーラブルなアーキテクチャを定義します。

## 2. Architecture Overview / アーキテクチャ概要

### 2.1 Technology Stack / 技術スタック
Detailed technology choices with specific versions:

- **Language / 言語**: 
  - Primary: TypeScript 5.3.2
  - Node.js: 20.10.0 LTS
  
- **Framework / フレームワーク**:
  - Frontend: Next.js 14.0.4 (App Router)
  - UI Components: Shadcn/ui (latest)
  - Testing: Jest 29.7.0, React Testing Library 14.1.2
  - E2E Testing: Playwright 1.40.1
  
- **Core Libraries / コアライブラリ**:
  - Styling: Tailwind CSS 3.3.6, tailwindcss-animate 1.0.7
  - State Management: Zustand 4.4.7
  - Forms: React Hook Form 7.48.2, Zod 3.22.4
  - Animation: Framer Motion 10.16.16
  - Internationalization: next-intl 3.4.0
  - Icons: Lucide React 0.294.0
  - Date Handling: date-fns 2.30.0
  
- **Development Tools / 開発ツール**:
  - Package Manager: pnpm 8.12.1
  - Code Formatter: Prettier 3.1.1
  - Linter: ESLint 8.55.0, @typescript-eslint 6.14.0
  - Git Hooks: Husky 8.0.3, lint-staged 15.2.0
  - CSS Tools: PostCSS 8.4.32, Autoprefixer 10.4.16
  
- **Infrastructure / インフラ**:
  - CMS: Contentful / Sanity.io (TBD based on client preference)
  - Analytics: Google Analytics 4, Vercel Analytics
  - Deployment: Vercel (Production), Vercel Preview (Staging)
  - CDN: Vercel Edge Network
  - Email Service: SendGrid / AWS SES

### 2.2 System Architecture Diagram / システムアーキテクチャ図

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Client Browser                               │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐ ┌────────────┐│
│  │   Next.js   │  │   Tailwind   │  │  Framer     │ │  React     ││
│  │  App Router │  │     CSS      │  │  Motion     │ │  Hook Form ││
│  └─────────────┘  └──────────────┘  └─────────────┘ └────────────┘│
└─────────────────────────────────────────────────────────────────────┘
                                    │
                        ┌───────────┴───────────┐
                        │    Vercel Edge        │
                        │  ┌───────────────┐    │
                        │  │  Middleware   │    │
                        │  │  (Auth, i18n) │    │
                        │  └───────────────┘    │
                        └───────────┬───────────┘
                                    │
┌─────────────────────────────────────────────────────────────────────┐
│                      Next.js Server Components                       │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐ ┌────────────┐│
│  │    Pages    │  │     API      │  │   Server    │ │   Static   ││
│  │   Routes    │  │   Routes     │  │  Actions    │ │ Generation ││
│  └─────────────┘  └──────────────┘  └─────────────┘ └────────────┘│
└─────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┴────────────────┐
                    │          Services              │
┌─────────────────────────────────────────────────────────────────────┐
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐ ┌────────────┐│
│  │  Contentful │  │   SendGrid   │  │   Google    │ │   Vercel   ││
│  │     CMS     │  │     Email    │  │  Analytics  │ │  Analytics ││
│  └─────────────┘  └──────────────┘  └─────────────┘ └────────────┘│
└─────────────────────────────────────────────────────────────────────┘
```

### 2.3 Component Architecture / コンポーネントアーキテクチャ
- **Server Components by Default**: Leverage RSC for better performance
- **デフォルトでサーバーコンポーネント**: パフォーマンス向上のためRSCを活用
- **Client Components**: Only for interactivity (forms, animations)
- **クライアントコンポーネント**: インタラクティブ要素のみ（フォーム、アニメーション）

## 3. Module and Component Design / モジュールとコンポーネント設計

### 3.1 Directory Structure / ディレクトリ構造
```
src/
├── app/                          # App Router pages
│   ├── [locale]/                 # Internationalization root
│   │   ├── (marketing)/          # Marketing pages group
│   │   │   ├── page.tsx          # Homepage
│   │   │   ├── about/
│   │   │   ├── products/
│   │   │   └── services/
│   │   ├── (content)/            # Content pages group
│   │   │   ├── blog/
│   │   │   └── news/
│   │   └── layout.tsx
│   ├── api/                      # API routes
│   │   ├── contact/
│   │   └── webhook/
│   └── globals.css
├── components/
│   ├── ui/                       # Shadcn/ui components
│   ├── layout/                   # Layout components
│   │   ├── header.tsx
│   │   ├── footer.tsx
│   │   └── navigation.tsx
│   ├── features/                 # Feature-specific components
│   │   ├── hero/
│   │   ├── contact-form/
│   │   └── product-showcase/
│   └── shared/                   # Shared components
├── lib/                          # Utility functions
│   ├── contentful.ts             # CMS integration
│   ├── analytics.ts              # Analytics helpers
│   └── utils.ts                  # General utilities
├── hooks/                        # Custom React hooks
├── types/                        # TypeScript type definitions
├── styles/                       # Global styles
└── config/                       # Configuration files
```

### 3.2 Core Components / コアコンポーネント

#### 3.2.1 Layout Components (`src/components/layout/`)
```typescript
// header.tsx
export function Header({ locale }: { locale: string }) {
  // Server component for navigation
  // Fetches navigation data from CMS
  // Implements responsive design
}

// navigation.tsx  
"use client"
export function MobileNavigation() {
  // Client component for mobile menu
  // Hamburger menu with animations
  // Accessibility compliant
}
```

#### 3.2.2 Feature Components (`src/components/features/`)
```typescript
// hero/hero-section.tsx
export async function HeroSection() {
  // Server component fetching hero content
  // Optimized image loading with next/image
  // Lazy loading for below-fold content
}

// contact-form/contact-form.tsx
"use client"
export function ContactForm() {
  // Client component with React Hook Form
  // Zod validation schemas
  // Multi-step form with progress indicator
  // File upload support
}
```

### 3.3 API Design / API設計

#### 3.3.1 Contact API (`src/app/api/contact/route.ts`)
```typescript
export async function POST(request: Request) {
  // Validate request body with Zod
  // Send email via SendGrid
  // Store inquiry in database
  // Return confirmation
}
```

#### 3.3.2 CMS Webhook (`src/app/api/webhook/contentful/route.ts`)
```typescript
export async function POST(request: Request) {
  // Validate webhook signature
  // Trigger ISR revalidation
  // Update cache
}
```

## 4. Data Models / データモデル

### 4.1 Content Types / コンテンツタイプ
```typescript
// types/content.ts
interface Page {
  id: string
  slug: string
  title: LocalizedString
  description: LocalizedString
  content: RichText
  seo: SEOMetadata
}

interface BlogPost {
  id: string
  slug: string
  title: LocalizedString
  excerpt: LocalizedString
  content: RichText
  author: Author
  publishedAt: Date
  categories: Category[]
  tags: Tag[]
  featuredImage: Asset
}

interface Product {
  id: string
  name: LocalizedString
  description: LocalizedString
  features: Feature[]
  specifications: Specification[]
  media: Asset[]
  relatedProducts: Product[]
}

interface LocalizedString {
  ja: string
  en: string
}
```

### 4.2 Form Schemas / フォームスキーマ
```typescript
// lib/validations/contact.ts
export const contactSchema = z.object({
  name: z.string().min(2).max(100),
  email: z.string().email(),
  company: z.string().optional(),
  phone: z.string().optional(),
  inquiryType: z.enum(['general', 'product', 'support', 'partnership']),
  message: z.string().min(10).max(1000),
  attachments: z.array(z.instanceof(File)).max(3).optional(),
  consent: z.boolean().refine(val => val === true)
})
```

## 5. Performance Optimization / パフォーマンス最適化

### 5.1 Rendering Strategy / レンダリング戦略
- **Static Generation (SSG)**: Marketing pages, product pages
- **静的生成**: マーケティングページ、製品ページ
- **Incremental Static Regeneration (ISR)**: Blog posts, news
- **増分静的再生成**: ブログ記事、ニュース
- **Dynamic Rendering**: User-specific content
- **動的レンダリング**: ユーザー固有のコンテンツ

### 5.2 Optimization Techniques / 最適化技術
```typescript
// Image optimization
import Image from 'next/image'

// Font optimization
import { Inter, Noto_Sans_JP } from 'next/font/google'

// Component code splitting
const DynamicHeroVideo = dynamic(
  () => import('@/components/features/hero/hero-video'),
  { loading: () => <VideoSkeleton /> }
)
```

## 6. Internationalization / 国際化

### 6.1 i18n Configuration
```typescript
// config/i18n.ts
export const i18n = {
  defaultLocale: 'ja',
  locales: ['ja', 'en'] as const,
} as const

// middleware.ts
export function middleware(request: NextRequest) {
  // Locale detection and routing
  // Cookie-based preference storage
}
```

## 7. SEO Implementation / SEO実装

### 7.1 Metadata API
```typescript
// app/[locale]/layout.tsx
export async function generateMetadata({ params }) {
  return {
    title: {
      default: 'Company Name',
      template: '%s | Company Name'
    },
    description: '...',
    openGraph: { ... },
    twitter: { ... },
    alternates: {
      canonical: url,
      languages: {
        'ja': '/ja',
        'en': '/en'
      }
    }
  }
}
```

## 8. Security Measures / セキュリティ対策

### 8.1 Security Headers
```typescript
// next.config.js
const securityHeaders = [
  {
    key: 'X-DNS-Prefetch-Control',
    value: 'on'
  },
  {
    key: 'X-XSS-Protection',
    value: '1; mode=block'
  },
  {
    key: 'X-Frame-Options',
    value: 'SAMEORIGIN'
  },
  {
    key: 'Content-Security-Policy',
    value: ContentSecurityPolicy.replace(/\s{2,}/g, ' ').trim()
  }
]
```

## 9. Testing Strategy / テスト戦略

### 9.1 Unit Tests
```typescript
// __tests__/components/contact-form.test.tsx
describe('ContactForm', () => {
  it('validates required fields', async () => {
    // Test form validation
  })
  
  it('submits form data correctly', async () => {
    // Test form submission
  })
})
```

### 9.2 E2E Tests
```typescript
// e2e/contact-flow.spec.ts
test('complete contact flow', async ({ page }) => {
  await page.goto('/contact')
  // Test entire user journey
})
```

## 10. Deployment Configuration / デプロイメント設定

### 10.1 Vercel Configuration
```json
{
  "functions": {
    "app/api/contact/route.ts": {
      "maxDuration": 10
    }
  },
  "images": {
    "domains": ["images.ctfassets.net"]
  }
}
```

## 11. Version Compatibility Notes / バージョン互換性メモ

### 11.1 Critical Dependencies / 重要な依存関係
- **Next.js 14**: Requires Node.js 18.17.0 or later
- **React 18**: Concurrent features and Server Components
- **TypeScript 5.0+**: Satisfies operator and const type parameters

### 11.2 Version Constraints / バージョン制約
```json
{
  "engines": {
    "node": ">=18.17.0",
    "pnpm": ">=8.0.0"
  }
}
```

## 12. Revision History / 改訂履歴
- **[2024-12-XX]**: Initial design generation by spec-design command
  - Based on requirement.md for corporate website renewal
  - 企業ホームページリニューアルのrequirement.mdに基づく

--------------------------------------------------------------------------------