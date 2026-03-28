# 🏙️ SnapCity — AI Civic Reporter

> **Snap it. Report it. Fix it.**
> Turn a 10-second photo into a fully filed civic complaint — automatically.

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://choosealicense.com/licenses/mit/)
[![React](https://img.shields.io/badge/React-18.x-61DAFB?logo=react)](https://reactjs.org/)
[![Gemini AI](https://img.shields.io/badge/Google-Gemini_AI-4285F4?logo=google)](https://ai.google.dev/)
[![Supabase](https://img.shields.io/badge/Supabase-Database-3ECF8E?logo=supabase)](https://supabase.com/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

---

## 📖 Introduction

Every day, citizens walk past broken roads, overflowing garbage bins, busted streetlights, and water leaks — and do nothing. Not because they don't care, but because reporting a civic issue is painful. Wrong departments, endless forms, zero follow-up.

**SnapCity changes all of that.**

Upload a photo. Our AI agent instantly identifies the problem, estimates its severity, generates a professional complaint, and routes it to the correct municipal department — all in under 10 seconds. Citizens finally have a voice, and city authorities finally have structured, actionable data.

Built for hackathons. Built for cities. Built for impact.

---

## ✨ Features

### 🧠 AI-Powered Core
- 📸 **Image Upload with Preview** — Drag-and-drop or tap to upload any civic issue photo
- 🔍 **Auto Issue Classification** — Google Gemini Vision detects potholes, garbage, broken streetlights, water leaks, fallen trees, and more
- ⚠️ **Severity Detection** — AI rates urgency as Low, Medium, High, or Critical
- 🏛️ **Smart Department Routing** — Automatically maps each issue to the correct municipal department
- 📝 **AI-Generated Formal Complaint** — Gemini writes a professional, structured complaint letter instantly
- 📊 **Confidence Score** — Know how certain the AI is, with a recommended action plan
- 📍 **Auto Location Detection** — GPS-based location tagging with address resolution

### 📋 Complaint Management
- 🎫 **Unique Complaint ID** — Every report gets a trackable reference number
- 🔄 **Status Tracking** — Real-time updates: Pending → Under Review → In Progress → Resolved
- 🗂️ **Complaint History** — Full personal dashboard of all past submissions
- 🔔 **Email Notifications** — Alerts when complaint status changes

### 📊 Analytics Dashboard
- 🗺️ **City Heatmap** — Visual hotspot map of complaint density
- 📈 **Issue Trend Charts** — Track which problems are rising or falling
- 🏆 **Department Scorecards** — Resolution rates and average response times
- 📅 **Monthly City Health Report** — AI-generated civic health summaries

### 🔐 Security & Trust
- 🔒 **Secure Authentication** — Protected user accounts with session management
- 👤 **Anonymous Mode** — Report issues without revealing identity
- 🛡️ **Spam Detection** — AI filters fake or duplicate complaints
- 📷 **Photo Authenticity Check** — Verifies images are genuine civic issues

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| **Frontend** | React 18 + Tailwind CSS | Responsive web interface |
| **AI Vision & LLM** | Google Gemini (AI Studio) | Image analysis & complaint generation |
| **Database** | Supabase (PostgreSQL) | Complaint records & user data |
| **Storage** | Supabase Storage | Photo uploads & file management |
| **Authentication** | Supabase Auth | Secure user login & sessions |
| **Maps** | Google Maps API | Location detection & heatmap |
| **Email** | SMTP / SendGrid | Department notifications |
| **Hosting** | Vercel / Firebase Hosting | Frontend deployment |

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                     CITIZEN (Browser)                    │
│              React Frontend — SnapCity UI                │
└────────────────────────┬────────────────────────────────┘
                         │ REST API
┌────────────────────────▼────────────────────────────────┐
│                     API LAYER                            │
│              Supabase Edge Functions                     │
└──────┬──────────────────┬───────────────────────────────┘
       │                  │
┌──────▼──────┐   ┌───────▼──────────────────────────────┐
│  Supabase   │   │         Google Gemini AI              │
│  Database   │   │  • Image Classification               │
│  + Storage  │   │  • Severity Detection                 │
│  + Auth     │   │  • Complaint Generation               │
└──────┬──────┘   │  • Department Routing                 │
       │          └───────────────────────────────────────┘
┌──────▼──────────────────────────────────────────────────┐
│                   NOTIFICATION LAYER                     │
│         Email (SendGrid) + Push Notifications            │
└─────────────────────────────────────────────────────────┘
```

**Data Flow:**
1. User uploads photo → stored in Supabase Storage
2. Photo URL sent to Gemini Vision API → issue detected
3. Gemini LLM generates formal complaint text
4. Complaint saved to Supabase Database with department tag
5. Email auto-sent to relevant municipal department
6. User dashboard updated with live tracking status

---

## ⚙️ Installation Steps

### Prerequisites
Make sure you have the following installed:
- Node.js v18 or higher
- npm or yarn
- Git
- A Google AI Studio account (for Gemini API key)
- A Supabase account (free tier works)

---

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/snapcity.git
cd snapcity
```

### 2. Install Dependencies

```bash
npm install
# or
yarn install
```

### 3. Set Up Environment Variables

Create a `.env` file in the root directory:

```env
# Google Gemini AI
VITE_GEMINI_API_KEY=your_gemini_api_key_here

# Supabase
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key_here

# Google Maps
VITE_GOOGLE_MAPS_API_KEY=your_google_maps_api_key_here

# Email (optional)
VITE_SENDGRID_API_KEY=your_sendgrid_api_key_here
VITE_FROM_EMAIL=noreply@snapcity.in
```

### 4. Set Up Supabase Database

Run these SQL commands in your Supabase SQL editor:

```sql
-- Complaints table
CREATE TABLE complaints (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  user_id UUID REFERENCES auth.users(id),
  photo_url TEXT NOT NULL,
  issue_type TEXT NOT NULL,
  severity TEXT CHECK (severity IN ('low', 'medium', 'high', 'critical')),
  department TEXT NOT NULL,
  complaint_text TEXT NOT NULL,
  location TEXT,
  latitude FLOAT,
  longitude FLOAT,
  status TEXT DEFAULT 'Pending' CHECK (status IN ('Pending', 'Under Review', 'In Progress', 'Resolved')),
  confidence_score FLOAT,
  ward TEXT,
  city TEXT
);

-- Enable Row Level Security
ALTER TABLE complaints ENABLE ROW LEVEL SECURITY;

-- Policy: users can read their own complaints
CREATE POLICY "Users can view own complaints"
ON complaints FOR SELECT
USING (auth.uid() = user_id);

-- Policy: users can insert their own complaints
CREATE POLICY "Users can create complaints"
ON complaints FOR INSERT
WITH CHECK (auth.uid() = user_id);
```

### 5. Set Up Supabase Storage

```bash
# In Supabase Dashboard → Storage → Create bucket
# Bucket name: civic-photos
# Public: false (keep private)
```

### 6. Run the Development Server

```bash
npm run dev
# or
yarn dev
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

---

## 📱 Usage Guide

### For Citizens

**Step 1 — Sign Up / Login**
Create an account or log in securely. Anonymous reporting is also available.

**Step 2 — Upload a Photo**
Click the upload area or drag and drop a photo of the civic issue. Supported formats: JPG, PNG, WEBP.

**Step 3 — Auto Location**
Allow location access for automatic GPS tagging, or manually enter your address.

**Step 4 — AI Analysis**
Click **"Analyze Issue"**. Within seconds, the AI will show:
- Issue type detected (e.g., Pothole)
- Severity level (e.g., High)
- Department to handle it (e.g., Road Maintenance)
- Confidence score (e.g., 94%)
- AI-generated formal complaint

**Step 5 — Review & Submit**
Review the generated complaint. Edit if needed. Click **"Submit Complaint"** to file it officially.

**Step 6 — Track Your Complaint**
Go to **My Dashboard** to see real-time status updates for all your reports.

---

### For Municipal Officers

- Log in with officer credentials
- View all complaints assigned to your department
- Update complaint status as work progresses
- Access analytics dashboard for your ward/city

---

## 📸 Screenshots

> Screenshots from the live application:

| Screen | Description |
|---|---|
| `screenshots/home.png` | Landing page with upload interface |
| `screenshots/analysis.png` | AI analysis results with severity card |
| `screenshots/complaint.png` | Generated complaint preview |
| `screenshots/dashboard.png` | Citizen complaint tracking dashboard |
| `screenshots/heatmap.png` | City problem heatmap view |
| `screenshots/officer-portal.png` | Municipal officer management portal |

---

## 🚀 Future Enhancements

### Phase 2 — Community Features
- 👥 **Community Upvoting** — Neighbors upvote complaints to boost priority
- 💬 **Comment Thread** — Citizens add updates or additional photos
- 🏆 **Civic Points & Badges** — Gamified rewards for active reporters

### Phase 3 — Advanced AI
- 🔮 **Predictive Intelligence** — Forecast problem areas before they worsen
- ✅ **AI Verification** — Automatically confirm fix quality using before/after photo comparison
- 📰 **Civic Journalist Mode** — Auto-generate public reports for unresolved issues

### Phase 4 — Scale & Integration
- 🗣️ **Vernacular Voice Reporting** — Tamil, Hindi, Telugu, Kannada voice input
- 📱 **Native Mobile App** — Flutter-based iOS and Android app
- 🔗 **Government API Integration** — Direct integration with municipal ERP systems
- 🌍 **Multi-City Support** — Scalable to any city in India or globally

---

## 🌐 Deployment

### Deploy Frontend to Vercel

```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel --prod
```

### Deploy Frontend to Firebase Hosting

```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login and initialize
firebase login
firebase init hosting

# Build and deploy
npm run build
firebase deploy
```

### Environment Variables on Production
Make sure to add all `.env` variables to your hosting platform's environment settings (Vercel Dashboard → Settings → Environment Variables).

---

## 🤝 Contributing

Contributions are what make open source amazing. Any contributions you make are **greatly appreciated**.

### How to Contribute

1. **Fork** the repository
2. **Create** your feature branch
   ```bash
   git checkout -b feature/AmazingFeature
   ```
3. **Commit** your changes
   ```bash
   git commit -m 'Add some AmazingFeature'
   ```
4. **Push** to the branch
   ```bash
   git push origin feature/AmazingFeature
   ```
5. **Open** a Pull Request

### Contribution Guidelines
- Follow the existing code style and formatting
- Write clear, descriptive commit messages
- Add comments for complex logic
- Test your changes before submitting a PR
- Update documentation if you change functionality
- Be respectful and constructive in all interactions

### Reporting Bugs
Open an issue with the label `bug` and include:
- A clear description of the problem
- Steps to reproduce
- Expected vs actual behavior
- Screenshots if applicable

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.

```
MIT License

Copyright (c) 2026 SnapCity Team

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
```

---

## 👥 Team

| Name | Role | GitHub |
|---|---|---|
| **Pradeepan M** | Team Lead — Architecture, AI, Full Stack & Deployment | [@pradeepanm](https://github.com/pradeepanpondy-source) |
| **Mohamed Jabri J S** | Developer — Frontend, Backend & Integration | [@mohamedjabri](https://github.com/mohamedjabrijs2005) |

---

## 🙏 Acknowledgements

- [Google AI Studio](https://ai.google.dev/) — Gemini Vision & Language API
- [Supabase](https://supabase.com/) — Open source Firebase alternative
- [Google Maps Platform](https://developers.google.com/maps) — Location & mapping services
- [React](https://reactjs.org/) — Frontend framework
- [Tailwind CSS](https://tailwindcss.com/) — Utility-first CSS framework

---

<div align="center">

**Built with ❤️ for citizens. Built with 🧠 by AI.**

⭐ Star this repo if SnapCity inspired you!

[Report Bug](https://github.com/yourusername/snapcity/issues) · [Request Feature](https://github.com/yourusername/snapcity/issues) · [View Demo](https://snapcity.vercel.app)

</div>
