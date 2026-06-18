# react-big
Ustozingiz bergan fikr-mulohaza (feedback) sizga muammoni aniq ko'rsatib beribdi: Sizda g'oya va reja zo'r, lekin GitHub repozitoriyangiz bo'sh qolgan. Mentor loyihaning ishlashini baholay olishi va sizga kamida 75+ ball qo'yib berishi uchun barcha kodlarni to'liq yuklashingiz, papkalar tuzilishini ko'rsatishingiz va ko'proq commit'lar qilishingiz shart.

Hozir ushbu vaziyatdan chiqish va ballingizni 90+ gacha ko'tarish uchun 21 kunlik deaddline ichida nimalar qilish kerakligini qadamma-qadam ko'rib chiqamiz.

🛠 1-qadam: GitHub Repozitoriyasini To'g'rilash (Eng muhimi!)
Mentor aytganidek, loyihani bitta repoda, lekin alohida ikkita papkada saqlash eng to'g'ri yo'ldir. Repozitoriyangiz strukturasi quyidagicha ko'rinishi shart:

Plaintext
recipe-finder-app/ (Asosiy repo)
├── frontend/          # Vite + React loyihangiz kodi
│   ├── src/
│   ├── package.json
│   └── ...
├── backend/           # Flask + Python loyihangiz kodi
│   ├── app/
│   ├── requirements.txt
│   └── ...
└── README.md          # Arxitektura diagrammasi va yo'riqnoma
Ko'p commit qilish siri (Commit History):
Faqat yakuniy kodni birdiga yuklamang (yana 1 ta commit bo'lib qoladi). Har bir kichik bosqichni alohida commit qiling:

feat: add auth context and useLocalStorage hook

feat: implement protected routes in frontend

feat: add recipe search and pagination in flask backend

fix: resolve CORS issues between FE and BE

💻 2-qadam: Baholashda Ballni Oshiruvchi Asosiy Kodlar
Mentor siz yozgan mantiqni repoda ko'ra olishi uchun quyidagi muhim kod bo'laklarini o'z joyiga joylashtiring:

⚙️ Backend: backend/app/routes/recipes.py (Filtr, Qidiruv, Sahifalash)
Ustozingiz aynan shu mantiqni tekshiradi. SQLALchemy orqali qidiruv va sahifalash (pagination) quyidagicha bo'ladi:

Python
from flask import Blueprint, request, jsonify
from app.models import Recipe

recipes_bp = Blueprint('recipes', __name__)

@recipes_bp.route('/api/recipes', methods=['GET'])
def get_recipes():
    # Query parametrlarni olish
    search_query = request.args.get('q', '')
    category = request.args.get('category', 'All')
    page = request.args.get('p', 1, type=int)
    per_page = 6 # Har bir sahifadagi retseptlar soni

    # Baza so'rovini boshlash
    query = Recipe.query

    # 1. Qidiruv (Search)
    if search_query:
        query = query.filter(Recipe.title.ilike(f'%{search_query}%'))

    # 2. Filtr (Filter)
    if category != 'All':
        query = query.filter(Recipe.category == category)

    # 3. Sahifalash (Pagination)
    paginated_recipes = query.paginate(page=page, per_page=per_page, error_out=False)

    results = []
    for recipe in paginated_recipes.items:
        results.append({
            'id': recipe.id,
            'title': recipe.title,
            'category': recipe.category,
            'image': recipe.image
        })

    return jsonify({
        'recipes': results,
        'total_pages': paginated_recipes.pages,
        'current_page': paginated_recipes.page
    }), 200
🪝 Frontend: frontend/src/hooks/useFetch.js (AbortController bilan)
Optimizatsiya talabini bajarish uchun universal hook kodi repoda bo'lishi shart:

JavaScript
import { useState, useEffect } from 'react';

export default function useFetch(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) return;

    const controller = new AbortController();
    const signal = controller.signal;

    setLoading(true);
    setError(null);

    fetch(url, { ...options, signal })
      .then((res) => {
        if (!res.ok) throw new Error(`Xatolik: ${res.status}`);
        return res.json();
      })
      .then((jsonData) => {
        setData(jsonData);
        setLoading(false);
      })
      .catch((err) => {
        if (err.name === 'AbortError') {
          console.log('Eski so\'rov bekor qilindi.');
        } else {
          setError(err.message);
          setLoading(false);
        }
      });

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}
📈 3-qadam: Ballni 90+ ga ko'tarish uchun "Chek-list"
Qayta topshirishdan oldin hamma narsa joyidami, tekshirib oling:

[ ] Frontend papkasi yuklangan: Ichida package.json, src/pages, src/context to'liq ko'rinib turibdi.

[ ] Backend papkasi yuklangan: Ichida requirements.txt (flask, flask-cors, flask-sqlalchemy, pyjwt yozilgan) mavjud.

[ ] Commitlar soni: Kamida 10-15 taga yetkazilgan va har birida nima qilingani ingliz tilida yozilgan.

[ ] Jonli havolalar (Live Links): Frontend Vercel-ga, Backend Render/Railway-ga yuklanib, ishlayotgan havolalari README.md faylining eng tepasiga yozib qo'yilgan.

Sizda hali 21 kun bor — bu muddat loyihani ideal holatga keltirish uchun yetarli. Kodlarni joy-joyiga qo'yib, reponi yangilang va keyingi darsga ishonch bilan o'ting!
