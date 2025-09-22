<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>تطبيق حساب الخشب</title>
  <style>
    body { margin:0; font-family: sans-serif; background:#f5f5f5; }
    .page { display: none; padding:20px; }
    .active { display: block; }
    .bottom-nav {
      position: fixed; bottom:0; left:0; right:0;
      display: flex; justify-content: space-around;
      background: #fff; border-top:1px solid #ccc;
    }
    .bottom-nav button {
      flex:1; padding:10px; border:none; background:none;
      font-size:16px; cursor: pointer;
    }
    .bottom-nav button.active { color: green; font-weight: bold; }
    .fab {
      position: fixed; bottom:70px; right:20px;
      background: green; color:#fff; border:none;
      border-radius:50%; width:56px; height:56px;
      font-size:30px; cursor:pointer;
    }
    .modal {
      display:none; position:fixed; top:0; left:0; right:0; bottom:0;
      background:rgba(0,0,0,0.5); justify-content:center; align-items:center;
    }
    .modal-content {
      background:#fff; padding:20px; border-radius:10px; width:300px;
    }
    .modal input { width:100%; margin:5px 0; padding:8px; }
    .modal button { margin-top:10px; padding:10px; width:100%; }
  </style>
</head>
<body>

  <!-- الصفحات -->
  <div id="home" class="page active">
    <h2>الصفحة الرئيسية</h2>
    <p>محتوى الصفحة الرئيسية.</p>
  </div>

  <div id="stats" class="page">
    <h2>الإحصاء</h2>
    <div id="wood-list"></div>
    <button class="fab" onclick="openModal()">+</button>
  </div>

  <div id="settings" class="page">
    <h2>الإعدادات</h2>
    <p>إعدادات التطبيق.</p>
  </div>

  <div id="dashboard" class="page">
    <h2>لوحة التحكم</h2>
    <p>محتوى لوحة التحكم.</p>
  </div>

  <!-- النافبار -->
  <div class="bottom-nav">
    <button onclick="showPage('home', this)" class="active">الرئيسية</button>
    <button onclick="showPage('stats', this)">الإحصاء</button>
    <button onclick="showPage('settings', this)">الإعدادات</button>
    <button onclick="showPage('dashboard', this)">لوحة التحكم</button>
  </div>

  <!-- مودال الإضافة -->
  <div id="modal" class="modal">
    <div class="modal-content">
      <h3>إضافة نوع خشب</h3>
      <input type="text" id="wood-type" placeholder="نوع الخشب">
      <input type="number" id="wood-qty" placeholder="الكمية">
      <button onclick="saveWood()">حفظ</button>
      <button onclick="closeModal()">إلغاء</button>
    </div>
  </div>

  <script>
    let woodList = [];

    function showPage(id, btn) {
      document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
      document.getElementById(id).classList.add('active');
      document.querySelectorAll('.bottom-nav button').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
    }

    function openModal() {
      document.getElementById('modal').style.display = 'flex';
    }
    function closeModal() {
      document.getElementById('modal').style.display = 'none';
    }

    function saveWood() {
      const type = document.getElementById('wood-type').value;
      const qty = document.getElementById('wood-qty').value;

      if (!type || !qty) { alert("يرجى ملء الحقول"); return; }

      // إرسال البيانات للسيرفر
      fetch("http://192.168.1.10:5000/add", {   // غير الـ IP حسب سيرفر الشركة
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ type, quantity: parseInt(qty) })
      })
      .then(res => res.json())
      .then(data => {
        woodList.push(data.data);
        renderList();
        closeModal();
      })
      .catch(err => alert("خطأ في الاتصال بالسيرفر"));
    }

    function renderList() {
      const container = document.getElementById('wood-list');
      container.innerHTML = "";
      woodList.forEach(w => {
        const item = document.createElement("div");
        item.textContent = `${w.type} - ${w.quantity}`;
        container.appendChild(item);
      });
    }
  </script>
</body>
</html>
