# PINTAR — Fullstack Starter Project

## Teknologi

- Flutter Mobile App
- Next.js Web Dashboard
- Node.js + Express Backend
- PostgreSQL Database
- JWT Authentication
- OpenAI API
- Whisper API

---

# Struktur Project

```bash
PINTAR/
│
├── mobile-app/
├── web-dashboard/
├── backend-api/
├── database/
└── docs/
```

---

# 1. FLUTTER MOBILE APP

## Membuat Project

```bash
flutter create pintar_mobile
cd pintar_mobile
```

---

# Install Package Flutter

```bash
flutter pub add provider
flutter pub add dio
flutter pub add shared_preferences
flutter pub add speech_to_text
flutter pub add flutter_tts
flutter pub add lottie
```

---

# Struktur Flutter

```bash
lib/
│
├── core/
├── screens/
├── services/
├── widgets/
├── models/
└── main.dart
```

---

# main.dart

```dart
import 'package:flutter/material.dart';
import 'screens/login_screen.dart';

void main() {
  runApp(const PintarApp());
}

class PintarApp extends StatelessWidget {
  const PintarApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'PINTAR',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const LoginScreen(),
    );
  }
}
```

---

# login_screen.dart

Buat file:

```bash
lib/screens/login_screen.dart
```

```dart
import 'package:flutter/material.dart';
import 'dashboard_screen.dart';

class LoginScreen extends StatelessWidget {
  const LoginScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        padding: const EdgeInsets.all(24),
        decoration: const BoxDecoration(
          gradient: LinearGradient(
            colors: [Color(0xFF1565FF), Color(0xFF5E9CFF)],
            begin: Alignment.topLeft,
            end: Alignment.bottomRight,
          ),
        ),
        child: Center(
          child: SingleChildScrollView(
            child: Column(
              children: [
                const Icon(Icons.language,
                    color: Colors.white, size: 100),
                const SizedBox(height: 20),
                const Text(
                  'PINTAR',
                  style: TextStyle(
                    color: Colors.white,
                    fontSize: 40,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                const SizedBox(height: 30),
                TextField(
                  decoration: InputDecoration(
                    filled: true,
                    fillColor: Colors.white,
                    hintText: 'Email',
                    border: OutlineInputBorder(
                      borderRadius: BorderRadius.circular(14),
                    ),
                  ),
                ),
                const SizedBox(height: 20),
                TextField(
                  obscureText: true,
                  decoration: InputDecoration(
                    filled: true,
                    fillColor: Colors.white,
                    hintText: 'Password',
                    border: OutlineInputBorder(
                      borderRadius: BorderRadius.circular(14),
                    ),
                  ),
                ),
                const SizedBox(height: 30),
                SizedBox(
                  width: double.infinity,
                  height: 55,
                  child: ElevatedButton(
                    onPressed: () {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (_) => const DashboardScreen(),
                        ),
                      );
                    },
                    child: const Text('LOGIN'),
                  ),
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

---

# dashboard_screen.dart

```dart
import 'package:flutter/material.dart';

class DashboardScreen extends StatelessWidget {
  const DashboardScreen({super.key});

  Widget card(String title, IconData icon) {
    return Container(
      padding: const EdgeInsets.all(20),
      margin: const EdgeInsets.only(bottom: 20),
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(20),
        boxShadow: const [
          BoxShadow(
            blurRadius: 10,
            color: Colors.black12,
          )
        ],
      ),
      child: Row(
        children: [
          Icon(icon, size: 40, color: Colors.blue),
          const SizedBox(width: 20),
          Text(
            title,
            style: const TextStyle(
              fontSize: 20,
              fontWeight: FontWeight.bold,
            ),
          )
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: const Color(0xFFF5F9FF),
      appBar: AppBar(
        title: const Text('PINTAR Dashboard'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: ListView(
          children: [
            card('AI Tutor', Icons.smart_toy),
            card('Pronunciation', Icons.mic),
            card('Leaderboard', Icons.leaderboard),
            card('Game Edukasi', Icons.games),
            card('Premium', Icons.workspace_premium),
          ],
        ),
      ),
    );
  }
}
```

---

# 2. BACKEND NODE.JS

## Membuat Backend

```bash
mkdir backend-api
cd backend-api
npm init -y
```

---

# Install Package

```bash
npm install express cors dotenv pg bcrypt jsonwebtoken openai
```

---

# Struktur Backend

```bash
backend-api/
│
├── src/
│   ├── routes/
│   ├── controllers/
│   ├── middleware/
│   ├── services/
│   └── server.js
```

---

# server.js

```javascript
const express = require('express');
const cors = require('cors');
require('dotenv').config();

const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.json({
    message: 'PINTAR API Running'
  });
});

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

# PostgreSQL Connection

## db.js

```javascript
const { Pool } = require('pg');

const pool = new Pool({
  user: 'postgres',
  host: 'localhost',
  database: 'pintar_db',
  password: 'YOUR_PASSWORD',
  port: 5432,
});

module.exports = pool;
```

---

# 3. DATABASE SQL

## Buat Database

```sql
CREATE DATABASE pintar_db;
```

---

# Table Users

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100) UNIQUE,
  password TEXT,
  role VARCHAR(20) DEFAULT 'user',
  is_approved BOOLEAN DEFAULT false,
  xp INTEGER DEFAULT 0,
  level INTEGER DEFAULT 1,
  streak INTEGER DEFAULT 0,
  premium BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW()
);
```

---

# Table Lessons

```sql
CREATE TABLE lessons (
  id SERIAL PRIMARY KEY,
  language VARCHAR(50),
  title VARCHAR(255),
  level VARCHAR(50),
  content TEXT
);
```

---

# 4. JWT LOGIN SYSTEM

## authController.js

```javascript
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');

const login = async (req, res) => {
  const { email, password } = req.body;

  if (email === 'admin@gmail.com' && password === '123456') {
    const token = jwt.sign(
      { email },
      'SECRET_KEY',
      { expiresIn: '7d' }
    );

    return res.json({
      token,
      message: 'Login berhasil'
    });
  }

  return res.status(401).json({
    message: 'Login gagal'
  });
};

module.exports = {
  login,
};
```

---

# 5. OPENAI AI CHAT

## Install OpenAI

```bash
npm install openai
```

---

# aiService.js

```javascript
const OpenAI = require('openai');

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
});

async function askAI(message) {
  const response = await openai.chat.completions.create({
    model: 'gpt-4.1-mini',
    messages: [
      {
        role: 'user',
        content: message,
      },
    ],
  });

  return response.choices[0].message.content;
}

module.exports = askAI;
```

---

# 6. ENV FILE

## .env

```env
PORT=5000
OPENAI_API_KEY=YOUR_OPENAI_KEY
JWT_SECRET=SECRET_KEY
```

---

# 7. BUILD APK

## Jalankan Flutter

```bash
flutter run
```

---

# Build Release APK

```bash
flutter build apk --release
```

---

# Lokasi APK

```bash
build/app/outputs/flutter-apk/app-release.apk
```

---

# 8. FITUR SELANJUTNYA YANG BISA DITAMBAHKAN

- Voice recognition
- Whisper pronunciation scoring
- AI speaking simulator
- Daily streak
- XP & badge
- Leaderboard online
- Premium subscription
- Admin dashboard
- Firebase notification
- Dark mode animation
- AI mentor
- Multiplayer speaking room

---

# 9. DEPLOYMENT

## Frontend

- Vercel
- Netlify

## Backend

- Railway
- Render

## Database

- Supabase
- Neon PostgreSQL

---

# 10. TARGET FINAL

PINTAR menjadi platform belajar bahasa modern berbasis AI dengan:

- AI Tutor
- Speaking Simulator
- Pronunciation Checker
- Game Edukasi
- Daily Learning System
- Premium Learning Platform

