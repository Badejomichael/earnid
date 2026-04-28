# EarnID

### Verifiable Income Identity for Africa's Digital Workforce

[![Built on Solana](https://img.shields.io/badge/Built%20on-Solana-9945FF?style=flat&logo=solana)](https://solana.com)
[![Powered by Raenest](https://img.shields.io/badge/Powered%20by-Raenest-00C853?style=flat)](https://raenest.com)
[![Superteam Nigeria](https://img.shields.io/badge/Superteam-Nigeria-C8F135?style=flat)](https://superteam.fun/nigeria)
[![Next.js](https://img.shields.io/badge/Next.js-16-black?style=flat&logo=next.js)](https://nextjs.org)

---

## The Problem

Africa has over 35 million freelancers. They earn real money, do real work, and have real income histories. But when they apply for a visa, a loan, a bank account, or a high-value contract, they hit the same wall every time.

**Their income is invisible.**

Bank statements get rejected. PDF payslips are easily forged. Payoneer screenshots are not trusted. There is no standardized, verifiable way for a Nigerian freelancer to prove their financial credibility to the outside world.

This is not a payments problem. It is a proof problem.

---

## The Solution

EarnID is the first on-chain income verification protocol built for Africa's digital workforce.

Freelancers connect their income sources, and EarnID aggregates their earning history into a tamper-proof credential minted as an NFT on the Solana blockchain. The credential is shareable as a single link or QR code. Anyone, anywhere in the world, can verify it in seconds without creating an account or logging in.

One link. Instant trust. No forgeries.

---

## Live Demo

**App:** [https://earnid.netlify.app](https://earnid.netlify.app)

**Sample Credential:** [https://earnid.netlify.app/verify/demo](https://earnid.netlify.app/verify/demo)

---

## How It Works

**Step 1: Add your earnings**
Freelancers log income from any source manually or by uploading a CSV file. Upwork, Fiverr, Toptal, Deel, direct clients — everything counts.

**Step 2: Generate your consistency score**
A proprietary algorithm analyses three factors: total volume (40%), earning regularity across months (40%), and source diversity (20%). The result is a single 0 to 100 trust score that any institution can immediately understand.

**Step 3: Mint your credential on Solana**
The aggregated income data is minted as a tamper-proof NFT on the Solana blockchain. The credential is immutable, permanent, and tied to the user's wallet address.

**Step 4: Share and get verified**
The user shares a link or QR code. Visa officers, banks, clients, and institutions open the link and see the full verified credential with on-chain proof. No login required on their end.

---

## Features

### Core Product
- **On-chain credential minting** on Solana devnet with real transaction hashes and mint addresses
- **Consistency score algorithm** that computes a trust score from volume, regularity, and income diversity
- **Public verification page** that anyone can open to verify a credential without an account
- **Verification tracking** that logs every time a credential is viewed, with timestamps
- **PDF credential export** that generates a beautifully styled downloadable credential card
- **CSV import** for bulk uploading earnings history from any platform
- **QR code generation** linked to the live verification URL
- **Public or private toggle** to control credential visibility at any time

### Wallet and Web3
- **Solana wallet connection** via Phantom and Solflare with desktop extension detection and mobile deep linking
- **Wallet address saved to profile** and persists across devices when the user is logged in

### User Experience
- **Full authentication flow** with two-step registration, email login, password reset, and secure account deletion
- **Dashboard** with earnings overview, monthly bar chart, quick actions, and a mint prompt
- **Earnings management** with add, delete, search, filter by source, and sort by date or amount
- **Settings page** with profile editing, password change, and danger zone account deletion
- **Dashboard verify page** showing shareable link, QR code, and full verification history grouped by date
- **Fully responsive** across desktop, tablet, and mobile

---

## The Consistency Score

```
Score = (Volume Score × 0.40) + (Regularity Score × 0.40) + (Diversity Score × 0.20)

Volume Score    = min((total_earned / $10,000) × 100, 100)
Regularity Score = min((active_months / 12) × 100, 100)
Diversity Score  = min(unique_sources × 20, 100)
```

The score is designed to reward freelancers who earn consistently across multiple sources over time, not just those who had one big month.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 14, TypeScript, Tailwind CSS, Framer Motion |
| Backend | Supabase (PostgreSQL, Auth, RLS, Storage) |
| Blockchain | Solana Web3.js, @solana/wallet-adapter |
| Wallet | Phantom, Solflare via wallet adapter |
| PDF Generation | html2canvas + jsPDF |
| Deployment | Vercel / Netlify |

---

## Database Schema

```sql
profiles        -- user identity, profession, wallet address
earnings        -- income entries with source, amount, date
credentials     -- minted credential data, score, mint address, tx hash
verifications   -- verification log with timestamp for each credential view
```

All tables are protected with Supabase Row Level Security. Public credentials and verifications are readable by anyone. Private credentials return nothing.

---

## Why Solana

Solana is the only blockchain that makes this economically viable for African freelancers. Near-zero transaction fees, sub-second finality, and a growing ecosystem of Nigerian and African builders made it the natural home for EarnID.

The Raenest integration connects EarnID directly to the financial rails that Nigerian freelancers already use to receive cross-border payments, creating a complete loop from earning to proving to spending.

---

## Business Model

**Phase 1 (Current):** Free credential minting for individual freelancers. Revenue from premium features like detailed analytics and multiple credential versions.

**Phase 2:** B2B verification API sold to financial institutions, visa agencies, and global hiring platforms. They pay per verification query.

**Phase 3:** Partnership with Raenest and similar fintech platforms to embed EarnID as the trust layer for cross-border payments and financial products.

The total addressable market is every African freelancer who has ever been asked to prove their income to a foreign institution. That is tens of millions of people today and growing fast.

---

## Getting Started

### Prerequisites
- Node.js 18+
- A Supabase project

### Installation

```bash
git clone https://github.com/Badejomichael/earnid.git
cd earnid
npm install
```

### Environment Variables

Create a `.env.local` file in the root directory:

```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_role_key
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

### Database Setup

Run the SQL schema in your Supabase SQL editor:

```sql
create extension if not exists "uuid-ossp";

create table profiles (
  id uuid references auth.users on delete cascade primary key,
  wallet_address text unique,
  full_name text,
  profession text,
  country text default 'Nigeria',
  created_at timestamp with time zone default timezone('utc', now())
);

create table earnings (
  id uuid default uuid_generate_v4() primary key,
  user_id uuid references profiles(id) on delete cascade,
  source text not null,
  amount_usd numeric(12,2) not null,
  currency text default 'USD',
  earned_date date not null,
  description text,
  created_at timestamp with time zone default timezone('utc', now())
);

create table credentials (
  id uuid default uuid_generate_v4() primary key,
  user_id uuid references profiles(id) on delete cascade,
  total_earned numeric(12,2),
  consistency_score integer check (consistency_score between 0 and 100),
  active_since date,
  monthly_average numeric(12,2),
  top_sources text[],
  mint_address text,
  tx_hash text,
  is_public boolean default true,
  created_at timestamp with time zone default timezone('utc', now()),
  updated_at timestamp with time zone default timezone('utc', now())
);

create table verifications (
  id uuid default uuid_generate_v4() primary key,
  credential_id uuid references credentials(id) on delete cascade,
  viewer_ip text,
  viewer_country text,
  viewed_at timestamp with time zone default timezone('utc', now())
);
```

### Run Locally

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

---

## Project Structure

```
src/
├── app/
│   ├── page.tsx                    # Landing page
│   ├── login/page.tsx              # Authentication
│   ├── register/page.tsx           # Registration
│   ├── verify/[id]/page.tsx        # Public credential verification
│   ├── dashboard/
│   │   ├── page.tsx                # Overview
│   │   ├── earnings/page.tsx       # Earnings management
│   │   ├── credentials/page.tsx    # Credential minting
│   │   ├── verify/page.tsx         # Share and track verifications
│   │   └── settings/page.tsx       # Account settings
│   └── api/
│       ├── download-credential/    # PDF generation endpoint
│       └── delete-account/         # Secure account deletion
├── components/
│   ├── SolanaProvider.tsx          # Wallet adapter setup
│   ├── ConnectWalletButton.tsx     # Wallet connection UI
│   ├── QRCodeDisplay.tsx           # Real QR code generation
│   ├── WalletModal.tsx             # Wallet selection modal
│   └── WalletSuccessToast.tsx      # Wallet Connection success toast    
│   └── ui/
│       └── button.tsx/             # Buttons UI
└── lib/
    └── supabase/                   # Supabase client and server helpers
```

---

## Team

Built by **Badejomichael** (Superteam Nigeria) for the Colosseum Frontier Hackathon 2026.

---

## Track

This project is submitted to the **Raenest x Superteam Nigeria Track** as part of the Colosseum Frontier Hackathon.

EarnID is built for Nigerian-based builders shipping real products on Solana. It solves a real pain point for Africa's growing digital workforce and is directly aligned with Raenest's mission of helping Africans earn, receive, and move money globally with ease.

