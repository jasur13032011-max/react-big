# react-big
Bu juda jiddiy, professional va Production-ready (ishlab chiqarishga tayyor) loyiha arxitekturasi! "Recipe Finder" ilovasini shunday to'liq stek (Full-stack) va zamonaviy talablar bilan yakunlash sizni Junior+ dan Strong Junior darajasiga olib chiqadi.

Siz endi mustaqil ravishda loyihaning arxitekturasini chiza oladigan, xavfsizlik (JWT, Protected Routes) va optimizatsiya (Memo, AbortController, TanStack Query) muammolarini hal qila oladigan to'liq huquqli muhandissiz.

Quyida ushbu yirik loyihani yakunlash uchun tizimli yo'llanma, papkalar strukturasi va asosiy backend/frontend arxitekturasi keltirilgan.

🏗️ Loyiha Arxitekturasi
Full-stack ilovalarda mijoz (Frontend) va server (Backend) o'rtasidagi aloqa xavfsiz va tezkor bo'lishi kerak. Quyidagi diagrammada ushbu loyihaning ma'lumotlar oqimi va xavfsizlik tizimi tasvirlangan:

📁 Papkalar Strukturasi (Folder Structure)
1. Frontend (/frontend)
Vite va React Router v6 uchun eng qulay va kengayuvchan struktura:

Plaintext
src/
├── components/         # Qayta ishlatiladigan komponentlar (Card, Navbar, Footer)
├── context/            # Global holatlar (AuthContext.jsx, ThemeContext.jsx)
├── hooks/              # Custom hook'lar (useFetch.js, useDebounce.js, useLocalStorage.js)
├── pages/              # 7+ Asosiy sahifalar
│   ├── Home.jsx, Recipes.jsx, RecipeDetail.jsx, Favorites.jsx
│   ├── Profile.jsx, Login.jsx, Register.jsx, NotFound.jsx
├── routes/             # Himoyalangan va ochiq marshrutlar (ProtectedRoute.jsx)
├── styles/             # Tailwind yoki global CSS
├── App.jsx
└── main.jsx
2. Backend (/backend)
Flask va SQLAlchemy (PostgreSQL) uchun professional arxitektura:

Plaintext
backend/
├── app/
│   ├── __init__.py     # Flask app va CORS sozlamalari
│   ├── models.py       # SQLAlchemy modellari (User, Recipe, Comment, Favorite)
│   ├── routes/         # API endpoitlari bo'limi
│   │   ├── auth.py     # /api/auth (register, login)
│   │   ├── recipes.py  # /api/recipes (qidiruv, filtr, pagination)
│   │   └── actions.py  # /api/favorites va /api/comments
│   └── utils.py        # JWT token generatsiyasi va dekoratorlar
├── config.py           # DB va JWT maxfiy kalitlari
├── run.py              # Serverni ishga tushirish (gunicorn/flask)
└── requirements.txt
🔑 Backend: Flask JWT Dekoratori (utils.py)
Backend sahifalarini (Favorites, Comments) himoya qilish uchun JWT tokenni tekshiruvchi maxsus dekorator yozib olinadi:

Python
from functools import wraps
from flask import request, jsonify
import jwt
import os

def token_required(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        token = None
        # Tokenni Authorization sarlavhasidan qidirish
        if 'Authorization' in request.headers:
            auth_header = request.headers['Authorization']
            if auth_header.startswith("Bearer "):
                token = auth_header.split(" ")[1]

        if not token:
            return jsonify({'message': 'Token topilmadi, kirish taqiqlangan!'}), 401

        try:
            # Tokenni dekodlash
            data = jwt.decode(token, os.environ.get('JWT_SECRET_KEY'), algorithms=["HS256"])
            current_user_id = data['user_id']
        except jwt.ExpiredSignatureError:
            return jsonify({'message': 'Token muddati tugagan!'}), 401
        except jwt.InvalidTokenError:
            return jsonify({'message': 'Yaroqsiz token!'}), 401

        return f(current_user_id, *args, **kwargs)
    return decorated
🛡️ Frontend: Protected Route Wrapper (routes/ProtectedRoute.jsx)
Faqat tizimga kirgan foydalanuvchilarni Favorites va Profile sahifalariga qo'yadigan va Return-To Pattern bilan jihozlangan komponent:

JavaScript
import React from 'react';
import { Navigate, useLocation } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';

export default function ProtectedRoute({ children }) {
  const { user, loading } = useAuth();
  const location = useLocation();

  // Agar AuthContext hali localStorage'dan user'ni o'qiyotgan bo'lsa, kutamiz
  if (loading) return <div className="text-center p-10">⏳ Yuklanmoqda...</div>;

  if (!user) {
    // Foydalanuvchi qaysi sahifaga kirmoqchi bo'lganini eslab qolamiz
    return <Navigate to="/login" state={{ from: location.pathname }} replace />;
  }

  return children;
}
🚀 Deploy (Jonli efirga uzatish)
Loyiha to'liq tayyor bo'lgach, uni butun dunyoga ko'rsatish uchun quyidagi tartibda deploy qilinadi:

1. Backend: Render yoki Railway
PostgreSQL ma'lumotlar bazasini ochasiz va uning Connection String manzilini olasiz.

Flask loyihangizni GitHub-ga yuklab, Render/Railway-ga bog'laysiz.

Environment Variables (Atrof-muhit o'zgaruvchilari) bo'limiga DATABASE_URL, JWT_SECRET_KEY va FLASK_ENV=production qiymatlarini kiritasiz.

2. Frontend: Vercel
vite.config.js orqali build sozlamalarini tekshirasiz.

Vercel-ga loyihani ulab, Environment Variables qismiga VITE_API_BASE_URL=https://sizning-backend-manzilingiz.com qiymatini berasiz.

Siz bu bosqichdan keyin nimaga tayyorsiz?
Ushbu loyihani yakunlagan dasturchi nafaqat chiroyli UI chiza oladi, balki klient-server arxitekturasini mukammal tushunadi. Siz endi rezyume (CV) tayyorlab, Junior Full-stack yoki Junior Frontend Developer vakansiyalariga topshirishga, texnik suhbatlardan dadil o'tishga va real biznes loyihalarida jamoaviy ishlashga mutlaqo tayyorsiz!
