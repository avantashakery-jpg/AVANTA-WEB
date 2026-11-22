
<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AVANTA | آوانتا</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#f4f6f8;color:#182331;font-family:Tahoma,Arial,sans-serif}
header{background:#101d30;color:white;padding:22px 16px;text-align:center}
.logo{font-size:32px;font-weight:bold;letter-spacing:3px;color:#64d9c1}
header p{margin:10px 0 0;color:#d5e0eb;font-size:14px}
.container{max-width:850px;margin:22px auto;padding:0 14px}
h2{font-size:19px;margin:20px 0 12px}
.search,.panel,.ad{background:white;border-radius:13px;padding:16px;margin-bottom:15px;box-shadow:0 2px 12px #14243a0b}
input,select,textarea{width:100%;padding:13px;margin:7px 0;border:1px solid #d9e0e7;border-radius:8px;font:inherit;font-size:14px;background:white}
textarea{min-height:90px;resize:vertical}
button{border:0;border-radius:9px;padding:13px 18px;font:inherit;cursor:pointer}
.primary{background:#087f70;color:white;width:100%;font-weight:bold}
.secondary{background:#e8f3f1;color:#087f70}
.filters{display:flex;gap:8px;flex-wrap:wrap;margin:12px 0}
.filters button{background:#e7edf2;color:#26394c;padding:9px 13px;font-size:13px}
.filters button.active{background:#087f70;color:white}
.ad h3{margin:0 0 10px;font-size:17px}
.ad p{font-size:14px;line-height:1.8;color:#536273}
.meta{font-size:12px;color:#718092;margin-top:12px}
.empty{text-align:center;padding:25px;color:#718092}
footer{text-align:center;padding:25px;color:#82909e;font-size:12px}
.hidden{display:none}
</style>
</head>
<body>

<header>
  <div class="logo">AVANTA</div>
  <p>بازارگاه ارتباط انسان‌ها با کالاها، خدمات و فرصت‌ها</p>
</header>

<main class="container">

  <section class="search">
    <input id="searchInput" type="search"
      placeholder="چه چیزی می‌خواهید پیدا کنید؟">
    <div class="filters" id="filters">
      <button class="active" data-category="همه">همه آگهی‌ها</button>
      <button data-category="کالا">کالا</button>
      <button data-category="خدمات">خدمات</button>
      <button data-category="مهارت">مهارت</button>
    </div>
  </section>

  <section class="panel">
    <h2>ثبت آگهی جدید</h2>
    <form id="adForm">
      <input id="title" required maxlength="80"
        placeholder="عنوان آگهی؛ مثلاً خدمات طراحی ناخن">

      <select id="category" required>
        <option value="">انتخاب دسته‌بندی</option>
        <option value="کالا">فروش کالا</option>
        <option value="خدمات">ارائه خدمات</option>
        <option value="مهارت">معرفی مهارت</option>
      </select>

      <input id="price" placeholder="قیمت یا هزینه (اختیاری)">
      <input id="city" required placeholder="شهر یا محل فعالیت">
      <input id="contact" required placeholder="شماره تماس یا راه ارتباطی">
      <textarea id="description" required maxlength="1000"
        placeholder="توضیحات آگهی"></textarea>

      <button class="primary" type="submit">ثبت آگهی</button>
    </form>
  </section>

  <h2>آگهی‌های آوانتا</h2>
  <div id="adsList"></div>

</main>

<footer>
  AVANTA — برای انسان‌ها، برای زمین، برای آینده
</footer>

<script>
const STORAGE_KEY = "avanta_public_demo_ads";
let selectedCategory = "همه";
let ads = [];

try {
  ads = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
  if (!Array.isArray(ads)) ads = [];
} catch {
  ads = [];
}

const form = document.getElementById("adForm");
const list = document.getElementById("adsList");
const searchInput = document.getElementById("searchInput");

function saveAds() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(ads));
}

function safeText(value) {
  return String(value ?? "").replace(/[&<>"']/g, char => ({
    "&":"&amp;",
    "<":"&lt;",
    ">":"&gt;",
    '"':"&quot;",
    "'":"&#39;"
  }[char]));
}

function renderAds() {
  const query = searchInput.value.trim().toLowerCase();

  const filtered = ads.filter(ad => {
    const matchesCategory =
      selectedCategory === "همه" ||
      ad.category === selectedCategory;

    const text = (
      ad.title + " " + ad.description + " " + ad.city
    ).toLowerCase();

    return matchesCategory && text.includes(query);
  });

  if (!filtered.length) {
    list.innerHTML =
      '<div class="empty">هنوز آگهی‌ای برای نمایش وجود ندارد.</div>';
    return;
  }

  list.innerHTML = filtered.map(ad => `
    <article class="ad">
      <h3>${safeText(ad.title)}</h3>
      <div class="meta">
        ${safeText(ad.category)} · ${safeText(ad.city)}
      </div>
      <p>${safeText(ad.description)}</p>
      <div><strong>قیمت:</strong> ${safeText(ad.price || "توافقی")}</div>
      <div class="meta">
        راه ارتباطی: ${safeText(ad.contact)}
      </div>
    </article>
  `).join("");
}

form.addEventListener("submit", function(event) {
  event.preventDefault();

  const ad = {
    id: Date.now(),
    title: document.getElementById("title").value.trim(),
    category: document.getElementById("category").value,
    price: document.getElementById("price").value.trim(),
    city: document.getElementById("city").value.trim(),
    contact: document.getElementById("contact").value.trim(),
    description: document.getElementById("description").value.trim()
  };

  if (!ad.title || !ad.category || !ad.city ||
      !ad.contact || !ad.description) {
    alert("لطفاً اطلاعات ضروری را تکمیل کنید.");
    return;
  }

  ads.unshift(ad);
  saveAds();
  form.reset();
  renderAds();
  alert("آگهی در این نسخه آزمایشی ثبت شد.");
});

searchInput.addEventListener("input", renderAds);

document.querySelectorAll("#filters button").forEach(button => {
  button.addEventListener("click", () => {
    selectedCategory = button.dataset.category;

    document.querySelectorAll("#filters button")
      .forEach(item => item.classList.remove("active"));

    button.classList.add("active");
    renderAds();
  });
});

renderAds();
</script>
</body>
</html>
