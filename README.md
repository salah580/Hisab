<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>تطبيق حساب الخشب — كامل</title>

  <!-- Chart.js CDN -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <style>
    :root{
      --bg:#f5f7fa; --card:#fff; --accent:#2e8b57; --text:#222;
      --muted:#6b6b6b;
    }
    [data-theme="dark"]{
      --bg:#0f1720; --card:#0b1520; --accent:#36d399; --text:#e6eef6; --muted:#9aa4af;
    }
    body { margin:0; font-family: "Segoe UI", Tahoma, system-ui; background:var(--bg); color:var(--text); }
    .app { min-height:100vh; padding-bottom:80px; box-sizing:border-box; }
    header { padding:12px 16px; display:flex; align-items:center; gap:12px; }
    header img.logo { width:44px; height:44px; border-radius:8px; }
    header h1 { font-size:18px; margin:0; }

    /* Pages */
    .page { display:none; padding:16px; }
    .page.active { display:block; }

    /* Cards */
    .card { background:var(--card); border-radius:10px; padding:12px; box-shadow:0 6px 18px rgba(0,0,0,0.06); margin-bottom:12px; }
    .flex { display:flex; gap:12px; align-items:center; }
    .row { display:flex; gap:8px; }

    /* Bottom nav */
    .bottom-nav { position:fixed; bottom:0; left:0; right:0; height:64px; background:var(--card); display:flex; border-top:1px solid rgba(0,0,0,0.06); justify-content:space-around; align-items:center; }
    .nav-btn{ background:none;border:none;padding:8px 6px;font-size:13px; color:var(--muted); display:flex; flex-direction:column; align-items:center; gap:4px; cursor:pointer; }
    .nav-btn.active { color:var(--accent); font-weight:600; }

    /* FAB */
    .fab { position:fixed; right:18px; bottom:82px; width:56px;height:56px;border-radius:50%; background:var(--accent); color:#fff; border:none; font-size:30px; display:flex; align-items:center; justify-content:center; cursor:pointer; box-shadow:0 8px 18px rgba(0,0,0,0.2); }

    /* Forms */
    label { display:block; font-size:13px; margin-bottom:6px; color:var(--muted); }
    input[type="text"], input[type="number"], select { width:100%; padding:8px 10px; border-radius:6px; border:1px solid #ddd; box-sizing:border-box; background:transparent; color:var(--text); }
    button.primary { background:var(--accent); color:#fff;border:none;padding:8px 12px;border-radius:8px; cursor:pointer; }

    table { width:100%; border-collapse:collapse; font-size:14px; }
    th, td { padding:8px 6px; text-align:right; border-bottom:1px solid #eee; }
    th { color:var(--muted); font-weight:600; font-size:13px; }

    .small { font-size:13px; color:var(--muted); }
    .badge { background:rgba(0,0,0,0.06); padding:6px 8px; border-radius:8px; font-size:13px; }

    /* splash */
    .splash { display:flex; align-items:center; justify-content:center; flex-direction:column; gap:12px; height:70vh; }
    .logo-big{ width:96px;height:96px;border-radius:18px;background:linear-gradient(135deg,#2e8b57, #36d399);display:flex;align-items:center;justify-content:center;color:white;font-weight:700;font-size:28px;box-shadow:0 10px 30px rgba(0,0,0,0.15); }

    /* responsive */
    @media(min-width:900px){
      .container { max-width:980px; margin:0 auto; padding:10px; }
      .bottom-nav { display:flex; justify-content:center; gap:8px; }
    }
  </style>
</head>
<body>

<div id="app" class="app" data-theme="light">

  <!-- Splash (initial) -->
  <div id="splash" class="page active">
    <div class="splash card container" style="text-align:center;">
      <div class="logo-big">موقعي</div>
      <div>
        <h2 id="splash-text">تطبيق حساب الخشب</h2>
        <p class="small" id="splash-sub">جاري التحضير...</p>
      </div>
      <div style="margin-top:8px;">
        <button class="primary" onclick="startApp()">ابدأ</button>
      </div>
    </div>
  </div>

  <!-- Main UI -->
  <div id="main" style="display:none;">
    <header class="card container" style="justify-content:space-between;">
      <div class="flex">
        <img class="logo" src="" alt="logo" onerror="this.style.display='none'">
        <div>
          <h1 id="app-title">تطبيق حساب الخشب</h1>
          <div class="small" id="user-info">Not logged</div>
        </div>
      </div>
      <div style="text-align:left;">
        <div class="small" id="server-info">Server: <span id="server-ip">local</span></div>
        <div class="small">Theme: <span id="theme-name">Light</span></div>
      </div>
    </header>

    <div class="container">

      <!-- Home -->
      <section id="home" class="page active">
        <div class="card">
          <div class="flex" style="justify-content:space-between;">
            <div>
              <h3 id="home-welcome">أهلاً بك</h3>
              <div class="small" id="home-sub">موجز العمليات الأخيرة</div>
            </div>
            <div>
              <div class="small">المجموعة: <span id="total-count">0</span></div>
              <div class="small">إجمالي الكمية: <span id="total-qty">0</span></div>
            </div>
          </div>
        </div>

        <div id="recent-list" class="card">
          <h4 id="recent-title">آخر 5 عمليات</h4>
          <div id="recent-items" class="small">لا توجد عمليات بعد.</div>
        </div>

        <div class="card">
          <h4 id="chart-title">نظرة سريعة</h4>
          <canvas id="summaryChart" height="120"></canvas>
        </div>
      </section>

      <!-- Stats -->
      <section id="stats" class="page">
        <div class="card">
          <h3 id="stats-title">الإحصاء</h3>
          <div class="row" style="align-items:flex-end;">
            <div style="flex:1;">
              <label id="lbl-type">نوع الخشب</label>
              <select id="select-type"></select>
            </div>
            <div style="width:120px;">
              <label id="lbl-qty">الكمية</label>
              <input id="input-qty" type="number" min="1" value="1" />
            </div>
            <div style="width:140px;">
              <label id="lbl-price">سعر الوحدة</label>
              <input id="input-price" type="number" min="0" step="0.01" />
            </div>
            <div style="width:160px;">
              <label id="lbl-total">السعر الإجمالي</label>
              <div class="badge" id="display-total">0</div>
            </div>
            <div>
              <button class="primary" id="add-op-btn" style="height:40px;">إضافة</button>
            </div>
          </div>
        </div>

        <div class="card">
          <h4 id="ops-title">قائمة العمليات</h4>
          <div style="display:flex; gap:8px; margin-bottom:8px;">
            <input id="filter-text" placeholder="بحث / نوع / موظف" style="flex:1; padding:8px;" />
            <input id="filter-date" type="date" />
            <button onclick="filterOps()" class="primary">بحث</button>
            <button onclick="resetFilter()">إعادة</button>
          </div>
          <div style="overflow:auto; max-height:320px;">
            <table id="ops-table">
              <thead>
                <tr><th>العملية</th><th>النوع</th><th>الكمية</th><th>إجمالي</th><th>الموظف</th><th>الوقت</th><th>إجراءات</th></tr>
              </thead>
              <tbody></tbody>
            </table>
          </div>
          <div style="margin-top:8px;">
            <button onclick="exportCSV()" class="primary">تصدير CSV</button>
          </div>
        </div>

      </section>

      <!-- Settings -->
      <section id="settings" class="page">
        <div class="card">
          <h3 id="settings-title">الإعدادات</h3>

          <div style="display:flex; gap:12px; flex-wrap:wrap;">
            <div style="flex:1; min-width:220px;">
              <label id="lbl-lang">اللغة</label>
              <select id="lang-select"></select>
            </div>

            <div style="flex:1; min-width:220px;">
              <label id="lbl-theme">الوضع</label>
              <select id="theme-select">
                <option value="light">Light / فاتح</option>
                <option value="dark">Dark / داكن</option>
              </select>
            </div>

            <div style="flex:1; min-width:220px;">
              <label id="lbl-server">عنوان سيرفر الشركة (IP or URL)</label>
              <input id="server-input" placeholder="http://192.168.1.10:5000" />
            </div>

            <div style="flex:1; min-width:220px;">
              <label id="lbl-autosync">المزامنة التلقائية عند الاتصال</label>
              <select id="autosync-select">
                <option value="yes">نعم</option>
                <option value="no">لا</option>
              </select>
            </div>
          </div>

          <div style="margin-top:12px;">
            <button class="primary" onclick="saveSettings()">حفظ الإعدادات</button>
            <button style="margin-left:8px;" onclick="syncPending()">مزامنة الآن</button>
          </div>
        </div>

        <div class="card">
          <h4>إدارة أنواع الخشب</h4>
          <div style="display:flex; gap:8px;">
            <input id="new-type" placeholder="اسم النوع" />
            <input id="new-price" placeholder="سعر الوحدة" type="number" step="0.01" />
            <button onclick="addType()" class="primary">إضافة نوع</button>
          </div>
          <div id="types-list" style="margin-top:10px;"></div>
        </div>

        <div class="card">
          <h4>المستخدمون</h4>
          <div style="display:flex; gap:8px;">
            <input id="user-name" placeholder="اسم المستخدم" />
            <input id="user-pass" placeholder="كلمة المرور" />
            <select id="user-role"><option value="user">User</option><option value="admin">Admin</option></select>
            <button onclick="addUser()" class="primary">إضافة مستخدم</button>
          </div>
          <div id="users-list" style="margin-top:10px;"></div>
        </div>
      </section>

      <!-- Admin -->
      <section id="admin" class="page">
        <div class="card">
          <h3>لوحة التحكم (Admin)</h3>
          <div class="small">عرض وإدارة كل البيانات، فلترة، حذف جماعي، وتصدير.</div>
          <div style="margin-top:8px;">
            <button onclick="clearAll()" class="primary">مسح كل البيانات</button>
            <button onclick="exportCSV()" style="margin-left:8px;">تصدير كل البيانات CSV</button>
          </div>
        </div>

        <div class="card">
          <h4>إحصاءات متقدمة</h4>
          <canvas id="adminChart" height="120"></canvas>
        </div>
      </section>

    </div>
  </div>

  <!-- Bottom nav -->
  <div class="bottom-nav">
    <button class="nav-btn active" data-target="home" onclick="navClick(event)"><span>🏠</span><span id="nav-home">الرئيسية</span></button>
    <button class="nav-btn" data-target="stats" onclick="navClick(event)"><span>📊</span><span id="nav-stats">الإحصاء</span></button>
    <button class="nav-btn" data-target="settings" onclick="navClick(event)"><span>⚙️</span><span id="nav-settings">الإعدادات</span></button>
    <button class="nav-btn" data-target="admin" onclick="navClick(event)"><span>🔒</span><span id="nav-admin">لوحة التحكم</span></button>
  </div>

  <!-- FAB (only for stats add) -->
  <button class="fab" title="Add" onclick="openStatsFromFab()">＋</button>

</div>

<script>
/* ---------------------------
  Storage keys & initial data
----------------------------*/
const STORAGE = {
  TYPES: 'wood_types_v1',
  OPS: 'wood_ops_v1',
  SETTINGS: 'wood_settings_v1',
  USERS: 'wood_users_v1',
  PENDING: 'wood_pending_v1'
};

let state = {
  types: [],
  ops: [],
  pending: [],
  settings: { lang:'ar', theme:'light', server:'', autosync: true },
  users: []
};

const translations = {
  ar: {
    start: "ابدأ",
    welcome: "أهلاً بك في تطبيق حساب الخشب",
    recentTitle: "آخر 5 عمليات",
    noOps: "لا توجد عمليات بعد.",
    add: "إضافة",
    saveSettings: "حفظ الإعدادات",
    syncNow: "مزامنة الآن",
    exportCSV: "تصدير CSV",
    clearAllConfirm: "هل تريد مسح كل البيانات؟ هذا الإجراء لا يمكن التراجع عنه.",
    loginPrompt: "الرجاء تسجيل الدخول للوصول إلى اللوحة",
    lblType:"نوع الخشب", lblQty:"الكمية", lblPrice:"سعر الوحدة", lblTotal:"السعر الإجمالي"
  },
  en: {
    start: "Start",
    welcome: "Welcome to Wood Counter App",
    recentTitle: "Recent 5 operations",
    noOps: "No operations yet.",
    add: "Add",
    saveSettings: "Save Settings",
    syncNow: "Sync Now",
    exportCSV: "Export CSV",
    clearAllConfirm: "Clear all data? This cannot be undone.",
    loginPrompt: "Please login to access admin",
    lblType:"Type", lblQty:"Quantity", lblPrice:"Unit Price", lblTotal:"Total Price"
  }
};

/* ---------------------------
  Utilities
----------------------------*/
function uid(){ return Date.now().toString(36) + Math.random().toString(36).slice(2,8); }

function saveToStorage(){
  localStorage.setItem(STORAGE.TYPES, JSON.stringify(state.types));
  localStorage.setItem(STORAGE.OPS, JSON.stringify(state.ops));
  localStorage.setItem(STORAGE.SETTINGS, JSON.stringify(state.settings));
  localStorage.setItem(STORAGE.USERS, JSON.stringify(state.users));
  localStorage.setItem(STORAGE.PENDING, JSON.stringify(state.pending));
}

function loadFromStorage(){
  state.types = JSON.parse(localStorage.getItem(STORAGE.TYPES) || '[]');
  state.ops = JSON.parse(localStorage.getItem(STORAGE.OPS) || '[]');
  state.settings = JSON.parse(localStorage.getItem(STORAGE.SETTINGS) || JSON.stringify(state.settings));
  state.users = JSON.parse(localStorage.getItem(STORAGE.USERS) || '[]');
  state.pending = JSON.parse(localStorage.getItem(STORAGE.PENDING) || '[]');
}

/* ---------------------------
  Initialization & Splash
----------------------------*/
function startApp(){
  document.getElementById('splash').style.display='none';
  document.getElementById('main').style.display='block';
  loadFromStorage();
  ensureDefaults();
  applySettingsToUI();
  renderAll();
  bindEvents();
  if(state.settings.autosync) tryAutoSync();
}

function ensureDefaults(){
  // default types if empty (sample)
  if(state.types.length===0){
    state.types.push({ id:uid(), name:'خشب بلوط', price:12.5 });
    state.types.push({ id:uid(), name:'خشب صنوبر', price:9.0 });
  }
  // default admin user
  if(state.users.length===0){
    state.users.push({ id:uid(), name:'admin', pass:'admin', role:'admin' });
    state.users.push({ id:uid(), name:'seller', pass:'seller', role:'user' });
  }
  saveToStorage();
}

/* ---------------------------
  UI bindings
----------------------------*/
function bindEvents(){
  document.getElementById('input-qty').addEventListener('input', updateTotalDisplay);
  document.getElementById('input-price').addEventListener('input', updateTotalDisplay);
  document.getElementById('select-type').addEventListener('change', onTypeChange);
  document.getElementById('add-op-btn').addEventListener('click', onAddOperation);
  document.getElementById('lang-select').addEventListener('change', onLangChange);
  document.getElementById('theme-select').addEventListener('change', onThemeChange);
  document.getElementById('server-input').addEventListener('input', ()=>document.getElementById('server-ip').innerText = document.getElementById('server-input').value || 'local');
  window.addEventListener('online', ()=>{ if(state.settings.autosync) syncPending(); });
}

/* ---------------------------
  Navigation
----------------------------*/
function navClick(e){
  const btn = e.currentTarget;
  document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  const target = btn.getAttribute('data-target');
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById(target).classList.add('active');
  // render charts if admin/stats
  if(target==='admin') renderAdminChart();
  if(target==='home') updateSummaryChart();
}

/* FAB shortcuts */
function openStatsFromFab(){ document.querySelector('.nav-btn[data-target="stats"]').click(); }

/* ---------------------------
  Types management
----------------------------*/
function populateTypes(){
  const sel = document.getElementById('select-type');
  sel.innerHTML = '';
  state.types.forEach(t=>{
    const opt = document.createElement('option');
    opt.value = t.id; opt.textContent = `${t.name} (${t.price})`;
    sel.appendChild(opt);
  });
  // types list in settings
  const list = document.getElementById('types-list');
  list.innerHTML = '';
  state.types.forEach(t=>{
    const div = document.createElement('div');
    div.className='small';
    div.style.display='flex'; div.style.justifyContent='space-between'; div.style.alignItems='center'; div.style.marginBottom='6px';
    div.innerHTML = `<div>${t.name} — ${t.price}</div>
      <div style="display:flex; gap:6px;">
        <button onclick='editType("${t.id}")'>تعديل</button>
        <button onclick='deleteType("${t.id}")'>حذف</button>
      </div>`;
    list.appendChild(div);
  });
}

function addType(){
  const name = document.getElementById('new-type').value.trim();
  const price = parseFloat(document.getElementById('new-price').value) || 0;
  if(!name){ alert('ادخل اسم النوع'); return; }
  state.types.push({ id:uid(), name, price });
  saveToStorage(); populateTypes(); document.getElementById('new-type').value=''; document.getElementById('new-price').value='';
}

function editType(id){
  const t = state.types.find(x=>x.id===id);
  const name = prompt('اسم النوع', t.name);
  if(name===null) return;
  const price = prompt('سعر الوحدة', t.price);
  if(price===null) return;
  t.name = name; t.price = parseFloat(price) || t.price;
  saveToStorage(); populateTypes();
}

function deleteType(id){
  if(!confirm('حذف هذا النوع؟')) return;
  state.types = state.types.filter(x=>x.id!==id);
  saveToStorage(); populateTypes();
}

/* ---------------------------
  Users management
----------------------------*/
function renderUsers(){
  const el = document.getElementById('users-list');
  el.innerHTML = '';
  state.users.forEach(u=>{
    const d = document.createElement('div'); d.className='small';
    d.style.display='flex'; d.style.justifyContent='space-between'; d.style.alignItems='center'; d.style.marginBottom='6px';
    d.innerHTML = `<div>${u.name} — ${u.role}</div><div style="display:flex;gap:6px;"><button onclick='deleteUser("${u.id}")'>حذف</button></div>`;
    el.appendChild(d);
  });
}
function addUser(){
  const name = document.getElementById('user-name').value.trim();
  const pass = document.getElementById('user-pass').value.trim();
  const role = document.getElementById('user-role').value;
  if(!name || !pass){ alert('أكمل الحقول'); return; }
  state.users.push({ id:uid(), name, pass, role });
  saveToStorage(); renderUsers();
  document.getElementById('user-name').value=''; document.getElementById('user-pass').value='';
}
function deleteUser(id){
  if(!confirm('حذف المستخدم؟')) return;
  state.users = state.users.filter(u=>u.id!==id);
  saveToStorage(); renderUsers();
}

/* ---------------------------
  Operations (إضافة عملية)
----------------------------*/
function onTypeChange(){
  const sel = document.getElementById('select-type');
  const t = state.types.find(x=>x.id===sel.value);
  document.getElementById('input-price').value = t ? t.price : '';
  updateTotalDisplay();
}
function updateTotalDisplay(){
  const qty = parseFloat(document.getElementById('input-qty').value) || 0;
  const price = parseFloat(document.getElementById('input-price').value) || 0;
  document.getElementById('display-total').textContent = (qty * price).toFixed(2);
}

function onAddOperation(){
  const sel = document.getElementById('select-type'); if(!sel.value){ alert('اختار نوع'); return; }
  const typeObj = state.types.find(x=>x.id===sel.value);
  const qty = parseFloat(document.getElementById('input-qty').value) || 0;
  const price = parseFloat(document.getElementById('input-price').value) || 0;
  const total = qty * price;
  const op = { id:uid(), typeId: typeObj.id, typeName:typeObj.name, qty, price, total, user: currentUser()? currentUser().name : 'guest', time: new Date().toISOString() };
  state.ops.push(op);
  // attempt to send to server; if fails, push to pending
  attemptSendOp(op).then(success=>{
    if(!success){ state.pending.push(op); saveToStorage(); alert('تم الحفظ محلياً وسيتم مزامنته لاحقاً'); }
  });
  saveToStorage(); renderAll();
}

/* ---------------------------
  Server sync
----------------------------*/
function getServerURL(){ const s = document.getElementById('server-input').value.trim(); return s || state.settings.server || ''; }

async function attemptSendOp(op){
  const server = getServerURL();
  if(!server) return false;
  try{
    const res = await fetch((server.endsWith('/')? server.slice(0,-1) : server) + '/add', {
      method:'POST', headers:{'Content-Type':'application/json'}, body: JSON.stringify(op)
    });
    if(!res.ok) throw new Error('http');
    const json = await res.json();
    // optionally update op with server ID
    return true;
  }catch(e){
    return false;
  }
}

async function syncPending(){
  if(state.pending.length===0){ alert('لا توجد عناصر للمزامنة'); return; }
  const server = getServerURL();
  if(!server){ alert('اضبط عنوان السيرفر في الإعدادات'); return; }
  const failed = [];
  for(const op of [...state.pending]){ // iterate copy
    const ok = await attemptSendOp(op);
    if(ok){
      // remove from pending
      state.pending = state.pending.filter(p=>p.id!==op.id);
    } else failed.push(op);
  }
  saveToStorage();
  if(failed.length===0) alert('تمت المزامنة بنجاح');
  else alert(`${failed.length} فشلت بالمزامنة`);
}

/* auto sync attempt on start */
function tryAutoSync(){ if(navigator.onLine){ syncPending(); } }

/* ---------------------------
  Rendering lists & tables
----------------------------*/
function renderRecent(){
  const el = document.getElementById('recent-items');
  const recent = state.ops.slice(-5).reverse();
  if(recent.length===0){ el.textContent = translations[state.settings.lang].noOps; return; }
  el.innerHTML = '';
  recent.forEach(r=>{
    const div = document.createElement('div'); div.className='small'; div.style.display='flex'; div.style.justifyContent='space-between';
    div.innerHTML = `<div>${r.typeName} — ${r.qty} وحدة</div><div>${(new Date(r.time)).toLocaleString()}</div>`;
    el.appendChild(div);
  });
}

function renderOpsTable(list){
  const tbody = document.querySelector('#ops-table tbody'); tbody.innerHTML='';
  (list||state.ops).slice().reverse().forEach(op=>{
    const tr = document.createElement('tr');
    tr.innerHTML = `<td>${op.id.slice(-6)}</td>
      <td>${op.typeName}</td>
      <td>${op.qty}</td>
      <td>${op.total.toFixed(2)}</td>
      <td>${op.user}</td>
      <td>${(new Date(op.time)).toLocaleString()}</td>
      <td>
        <button onclick='printOp("${op.id}")'>فاتورة</button>
        <button onclick='deleteOp("${op.id}")'>حذف</button>
      </td>`;
    tbody.appendChild(tr);
  });
}

function deleteOp(id){
  if(!confirm('حذف العملية؟')) return;
  state.ops = state.ops.filter(o=>o.id!==id);
  // also remove pending if exists
  state.pending = state.pending.filter(p=>p.id!==id);
  saveToStorage();
  renderAll();
}

/* ---------------------------
  Charts
----------------------------*/
let summaryChart=null, adminChart=null;
function updateSummaryChart(){
  const ctx = document.getElementById('summaryChart').getContext('2d');
  const labelMap = {};
  state.ops.forEach(o=>{ labelMap[o.typeName] = (labelMap[o.typeName]||0) + o.qty; });
  const labels = Object.keys(labelMap);
  const data = Object.values(labelMap);
  if(summaryChart) summaryChart.destroy();
  summaryChart = new Chart(ctx, { type:'bar', data:{ labels, datasets:[{ label:'الكمية', data }] }, options:{ responsive:true } });
}

function renderAdminChart(){
  const ctx = document.getElementById('adminChart').getContext('2d');
  const revenue = {};
  state.ops.forEach(o=> revenue[o.typeName] = (revenue[o.typeName]||0) + o.total );
  const labels = Object.keys(revenue), data = Object.values(revenue);
  if(adminChart) adminChart.destroy();
  adminChart = new Chart(ctx, { type:'pie', data:{ labels, datasets:[{ data }] }, options:{ responsive:true } });
}

/* ---------------------------
  Export CSV & Print
----------------------------*/
function exportCSV(){
  const rows = [['id','type','qty','price','total','user','time']];
  state.ops.forEach(o=> rows.push([o.id,o.typeName,o.qty,o.price,o.total,o.user,o.time]));
  const csv = rows.map(r=> r.map(c=> `"${String(c).replace(/"/g,'""')}"`).join(',')).join('\n');
  const blob = new Blob([csv], { type:'text/csv;charset=utf-8;' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'wood_ops.csv'; a.click();
  URL.revokeObjectURL(url);
}

function printOp(id){
  const o = state.ops.find(x=>x.id===id); if(!o) return alert('not found');
  const html = `<html><head><meta charset="utf-8"><title>فاتورة</title></head><body><h2>فاتورة</h2>
  <p>العملية: ${o.id}</p><p>النوع: ${o.typeName}</p><p>الكمية: ${o.qty}</p><p>السعر للوحدة: ${o.price}</p><p>الإجمالي: ${o.total}</p>
  <p>الموظف: ${o.user}</p><p>الوقت: ${(new Date(o.time)).toLocaleString()}</p></body></html>`;
  const w = window.open('','_blank'); w.document.write(html); w.document.close(); w.print();
}

/* ---------------------------
  Filters
----------------------------*/
function filterOps(){
  const text = document.getElementById('filter-text').value.trim().toLowerCase();
  const date = document.getElementById('filter-date').value;
  const filtered = state.ops.filter(o=>{
    const matchText = !text || o.typeName.toLowerCase().includes(text) || o.user.toLowerCase().includes(text);
    const matchDate = !date || new Date(o.time).toISOString().slice(0,10) === date;
    return matchText && matchDate;
  });
  renderOpsTable(filtered);
}
function resetFilter(){ document.getElementById('filter-text').value=''; document.getElementById('filter-date').value=''; renderOpsTable(); }

/* ---------------------------
  Settings
----------------------------*/
function applySettingsToUI(){
  document.getElementById('lang-select').value = state.settings.lang;
  document.getElementById('theme-select').value = state.settings.theme;
  document.getElementById('server-input').value = state.settings.server || '';
  document.getElementById('autosync-select').value = state.settings.autosync? 'yes':'no';
  document.getElementById('server-ip').innerText = state.settings.server || 'local';
  document.getElementById('theme-name').innerText = state.settings.theme;
  document.getElementById('app-title').textContent = state.settings.lang==='ar' ? 'تطبيق حساب الخشب' : 'Wood Counter App';
  // theme
  applyTheme(state.settings.theme);
  // language
  translateUI(state.settings.lang);
}

function saveSettings(){
  state.settings.lang = document.getElementById('lang-select').value;
  state.settings.theme = document.getElementById('theme-select').value;
  state.settings.server = document.getElementById('server-input').value.trim();
  state.settings.autosync = document.getElementById('autosync-select').value === 'yes';
  saveToStorage();
  applySettingsToUI();
  alert('تم الحفظ');
}

function applyTheme(t){
  document.getElementById('app').setAttribute('data-theme', t==='dark' ? 'dark' : 'light');
  document.getElementById('theme-name').innerText = t;
}

function onLangChange(){ translateUI(document.getElementById('lang-select').value); }
function onThemeChange(){ applyTheme(document.getElementById('theme-select').value); }

function translateUI(lang){
  document.getElementById('splash-text').textContent = translations[lang].welcome;
  document.getElementById('home-welcome').textContent = translations[lang].welcome;
  document.getElementById('recent-title').textContent = translations[lang].recentTitle;
  document.getElementById('lbl-type').textContent = translations[lang].lblType;
  document.getElementById('lbl-qty').textContent = translations[lang].lblQty;
  document.getElementById('lbl-price').textContent = translations[lang].lblPrice;
  document.getElementById('lbl-total').textContent = translations[lang].lblTotal;
  document.getElementById('add-op-btn').textContent = translations[lang].add;
  // nav labels
  document.getElementById('nav-home').textContent = (lang==='ar' ? 'الرئيسية' : 'Home');
  document.getElementById('nav-stats').textContent = (lang==='ar' ? 'الإحصاء' : 'Stats');
  document.getElementById('nav-settings').textContent = (lang==='ar' ? 'الإعدادات' : 'Settings');
  document.getElementById('nav-admin').textContent = (lang==='ar' ? 'لوحة التحكم' : 'Admin');
  // others...
}

/* ---------------------------
  Admin actions
----------------------------*/
function clearAll(){
  if(!confirm(translations[state.settings.lang].clearAllConfirm)) return;
  state.types = []; state.ops = []; state.pending = []; state.users = []; saveToStorage(); renderAll();
}

/* ---------------------------
  Helpers & render all
----------------------------*/
function renderAll(){
  populateTypes(); renderUsers(); renderRecent(); renderOpsTable(); updateTotals(); updateSummaryChart(); renderAdminChart();
  // set default selection
  if(document.getElementById('select-type').options.length>0) onTypeChange();
}

function updateTotals(){
  document.getElementById('total-count').textContent = state.ops.length;
  const totalQty = state.ops.reduce((s,o)=>s+o.qty,0);
  document.getElementById('total-qty').textContent = totalQty;
}

/* ---------------------------
  Pending & sync on close
----------------------------*/
window.addEventListener('beforeunload', ()=> { saveToStorage(); });

/* ---------------------------
  Simple authentication (client-side)
----------------------------*/
function currentUser(){ return JSON.parse(localStorage.getItem('current_user') || 'null'); }
function loginUser(name, pass){
  const u = state.users.find(x=> x.name===name && x.pass===pass);
  if(!u) return false;
  localStorage.setItem('current_user', JSON.stringify(u));
  document.getElementById('user-info').textContent = `${u.name} (${u.role})`;
  return true;
}
function logoutUser(){ localStorage.removeItem('current_user'); document.getElementById('user-info').textContent = 'Not logged'; }

/* show login prompt if admin section clicked and not admin */
document.querySelectorAll('.nav-btn[data-target="admin"]').forEach(b=>{
  b.addEventListener('click', ()=>{
    const u = currentUser();
    if(!u || u.role!=='admin'){
      const name = prompt('اسم المستخدم (admin)'); if(!name) return; const pass = prompt('كلمة المرور'); if(!pass) return;
      if(!loginUser(name,pass)){ alert('فشل تسجيل الدخول'); return; }
    }
  });
});

/* ---------------------------
  Init load (auto)
----------------------------*/
(function initLoad(){
  loadFromStorage();
  applySettingsToUI();
  // set list boxes
  document.getElementById('lang-select').innerHTML = `<option value="ar">العربية</option><option value="en">English</option>`;
  // show initial state but keep splash
  populateTypes(); renderUsers();
  // if user already logged
  const cu = currentUser();
  if(cu) document.getElementById('user-info').textContent = `${cu.name} (${cu.role})`;
})();

</script>
</body>
</html>      border-radius:50%; width:56px; height:56px;
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
    .card {
      background:#fff; margin:10px 0; padding:10px;
      border-radius:8px; box-shadow:0 2px 5px rgba(0,0,0,0.1);
    }
  </style>
</head>
<body>

  <!-- الصفحة الرئيسية -->
  <div id="home" class="page active">
    <h2>الصفحة الرئيسية</h2>
    <p>أهلا بك في تطبيق حساب الخشب.</p>
    <h3>📊 موجز الإحصاءات الأخيرة</h3>
    <div id="recent-stats"></div>
  </div>

  <!-- صفحة الإحصاء -->
  <div id="stats" class="page">
    <h2>الإحصاء</h2>
    <div id="wood-list"></div>
    <button class="fab" onclick="openModal()">+</button>
  </div>

  <!-- صفحة الإعدادات -->
  <div id="settings" class="page">
    <h2>الإعدادات</h2>
    <p>إعدادات التطبيق.</p>
  </div>

  <!-- لوحة التحكم -->
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
        renderRecent();
        closeModal();
      })
      .catch(err => alert("خطأ في الاتصال بالسيرفر"));
    }

    function renderList() {
      const container = document.getElementById('wood-list');
      container.innerHTML = "";
      woodList.forEach(w => {
        const item = document.createElement("div");
        item.className = "card";
        item.textContent = `${w.type} - ${w.quantity}`;
        container.appendChild(item);
      });
    }

    function renderRecent() {
      const container = document.getElementById('recent-stats');
      container.innerHTML = "";
      const recent = woodList.slice(-5).reverse(); // آخر 5 مدخلات
      if (recent.length === 0) {
        container.innerHTML = "<p>لا توجد إحصاءات بعد.</p>";
      } else {
        recent.forEach(w => {
          const item = document.createElement("div");
          item.className = "card";
          item.textContent = `${w.type} - ${w.quantity}`;
          container.appendChild(item);
        });
      }
    }
  </script>
</body>
</html>      border-radius:50%; width:56px; height:56px;
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
