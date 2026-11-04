  
<!DOCTYPE html>  
<html lang="tr">  
<head>  
<meta charset="UTF-8">  
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">  
<meta name="apple-mobile-web-app-capable" content="yes">  
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">  
<meta name="apple-mobile-web-app-title" content="Cüzdanım">  
<meta name="theme-color" content="#0f172a">  
<title>Cüzdanım - Kişisel Bütçe</title>  
<script src="https://cdn.tailwindcss.com"></script>  
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>  
<style>  
body{background:#0f172a;color:#f8fafc;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Arial,sans-serif;-webkit-tap-highlight-color:transparent}  
.tab-content{display:none}.tab-content.active{display:block}  
.modal{position:fixed;inset:0;background:#000b;z-index:60;display:none;align-items:flex-end;justify-content:center;padding:0}  
.modal.show{display:flex}  
.modal-panel{background:#1e293b;border:1px solid #334155;width:100%;max-width:480px;border-radius:24px 24px 0 0;padding:20px;max-height:90vh;overflow:auto}  
.field{width:100%;background:#0f172a;border:1px solid #334155;border-radius:12px;padding:11px;font-size:14px;color:#f8fafc;margin-top:5px}  
.btn{border-radius:12px;padding:11px 14px;font-size:13px;font-weight:650}  
.card{background:#1e293b;border:1px solid #334155;border-radius:18px;padding:15px}  
.muted{color:#94a3b8}  
@media(min-width:640px){.modal{align-items:center;padding:16px}.modal-panel{border-radius:20px}}  
</style>  
</head>  
<body class="pb-24">  
<header class="sticky top-0 z-40 bg-slate-800/95 border-b border-slate-700 px-4 py-3 flex justify-between items-center">  
 <div class="flex items-center gap-2"><div class="w-8 h-8 rounded-xl bg-gradient-to-tr from-emerald-500 to-blue-600 flex items-center justify-center font-bold">C</div><b>Cüzdanım</b></div>  
 <button class="btn bg-slate-700 border border-slate-600" onclick="openModal('export-modal')">Yedek / Veri</button>  
</header>  
  
<main class="max-w-md mx-auto p-4 space-y-4">  
<section id="tab-home" class="tab-content active space-y-4">  
 <div class="card bg-gradient-to-br from-slate-800 to-slate-900">  
  <p class="text-xs muted uppercase tracking-wider">Kullanılabilir Para (bu ay)</p>  
  <h2 id="available" class="text-3xl font-extrabold text-emerald-400 mt-1">0 TL</h2>  
  <div class="grid grid-cols-2 gap-4 mt-4 pt-4 border-t border-slate-700">  
   <div><p class="text-xs muted">Aylık Net Gelir</p><b id="salary-label">0 TL</b></div>  
   <div><p class="text-xs muted">Toplam Gider</p><b id="expense-label" class="text-rose-400">0 TL</b></div>  
  </div>  
 </div>  
 <div class="grid grid-cols-2 gap-3">  
  <div class="card"><p class="text-xs muted">Kalan Kategori Bütçesi</p><b id="budget-left" class="text-lg text-blue-400">0 TL</b></div>  
  <div class="card"><p class="text-xs muted">Nakit Birikim</p><b id="savings-label" class="text-lg text-emerald-400">0 TL</b></div>  
 </div>  
 <div class="card space-y-3">  
  <h3 class="text-sm font-semibold">Borçlar ve Yükümlülükler</h3>  
  <div class="flex justify-between text-sm"><span class="muted">Kredi Kartı Borcu</span><b id="debt-label" class="text-rose-400">0 TL</b></div>  
  <div class="flex justify-between text-sm"><span class="muted">Aylık Kredi Taksiti</span><b id="loan-label">0 TL</b></div>  
  <div class="flex justify-between text-sm"><span class="muted">Kira ve Faturalar</span><b id="bills-label">0 TL</b></div>  
 </div>  
 <div class="card space-y-2">  
  <div class="flex justify-between text-xs"><span class="muted">Tasarruf Hedefi (100.000 TL)</span><b id="goal-percent" class="text-emerald-400">%0</b></div>  
  <div class="h-2.5 rounded-full bg-slate-700 overflow-hidden"><div id="goal-bar" class="h-full bg-emerald-500 rounded-full" style="width:0%"></div></div>  
 </div>  
 <button class="btn bg-emerald-600 w-full" onclick="openTxModal()">+ Hızlı İşlem Ekle</button>  
</section>  
  
<section id="tab-transactions" class="tab-content space-y-4">  
 <div class="flex justify-between items-center"><h2 class="font-bold text-lg">İşlem Geçmişi</h2><button class="btn bg-emerald-600" onclick="openTxModal()">+ Yeni İşlem</button></div>  
 <input id="tx-search" class="field" placeholder="İşlem ara..." oninput="renderTransactions()">  
 <div id="tx-list" class="space-y-2"></div>  
</section>  
  
<section id="tab-budget" class="tab-content space-y-4">  
 <div class="flex justify-between items-center"><h2 class="font-bold text-lg">Aylık Kategori Bütçeleri</h2><button class="btn bg-slate-700" onclick="openCategoryModal()">+ Kategori</button></div>  
 <div id="budget-list" class="space-y-3"></div>  
</section>  
  
<section id="tab-debts" class="tab-content space-y-4">  
 <h2 class="font-bold text-lg">Kredi ve Kart Yönetimi</h2>  
 <div class="card space-y-3">  
  <div class="flex justify-between"><b class="text-sm">Kredi Kartı Detayları</b><button class="text-xs text-emerald-400" onclick="openCCModal()">Düzenle</button></div>  
  <div class="grid grid-cols-2 gap-2">  
   <div class="bg-slate-900 p-3 rounded-xl"><p class="text-xs muted">Mevcut Borç</p><b id="cc-current" class="text-rose-400">0 TL</b></div>  
   <div class="bg-slate-900 p-3 rounded-xl"><p class="text-xs muted">Planlanan Harcama</p><b id="cc-planned" class="text-blue-400">0 TL</b></div>  
  </div>  
 </div>  
 <div class="card space-y-3">  
  <div class="flex justify-between"><b class="text-sm">Aktif Kredi Takibi</b><button class="text-xs text-emerald-400" onclick="openLoanModal()">Düzenle</button></div>  
  <div class="flex justify-between text-sm"><span class="muted">Aylık taksit</span><b id="loan-monthly">0 TL</b></div>  
  <div class="flex justify-between text-sm"><span class="muted">Ödenen / toplam taksit</span><b id="loan-progress">0 / 0 Ay</b></div>  
  <div class="flex justify-between text-sm"><span class="muted">Kalan tutar</span><b id="loan-remaining" class="text-rose-400">0 TL</b></div>  
 </div>  
</section>  
  
<section id="tab-reports" class="tab-content space-y-4">  
 <h2 class="font-bold text-lg">Raporlar ve Ayarlar</h2>  
 <div class="card"><h3 class="text-xs muted mb-3 uppercase">Kategori Bazlı Harcama Dağılımı</h3><div class="h-64"><canvas id="expenseChart"></canvas></div></div>  
 <div class="card space-y-3"><b class="text-sm">Temel Finansal Değerler</b><button class="btn bg-slate-700 w-full" onclick="openSettingsModal()">Maaş, kira ve birikimi düzenle</button></div>  
</section>  
</main>  
  
<nav class="fixed bottom-0 left-0 right-0 bg-slate-800/95 border-t border-slate-700 z-50 max-w-md mx-auto">  
 <div class="grid grid-cols-5 h-16 text-center text-[10px] text-slate-400">  
  <button id="nav-home" onclick="switchTab('home')" class="text-emerald-400">⌂<br>Ana Sayfa</button>  
  <button id="nav-transactions" onclick="switchTab('transactions')">▤<br>İşlemler</button>  
  <button id="nav-budget" onclick="switchTab('budget')">◉<br>Bütçe</button>  
  <button id="nav-debts" onclick="switchTab('debts')">▣<br>Borçlar</button>  
  <button id="nav-reports" onclick="switchTab('reports')">▥<br>Raporlar</button>  
 </div>  
</nav>  
  
<div id="tx-modal" class="modal"><div class="modal-panel space-y-3">  
 <div class="flex justify-between"><b>Yeni İşlem</b><button onclick="closeModal('tx-modal')">✕</button></div>  
 <form id="tx-form" onsubmit="saveTransaction(event)" class="space-y-2">  
  <label class="text-xs muted">İşlem türü<select id="tx-type" class="field"><option value="expense">Gider</option><option value="income">Gelir</option></select></label>  
  <label class="text-xs muted">Tutar (TL)<input id="tx-amount" class="field" type="number" min="0.01" step="0.01" required></label>  
  <label class="text-xs muted">Kategori<select id="tx-category" class="field"></select></label>  
  <label class="text-xs muted">Ödeme yöntemi<select id="tx-payment" class="field"><option>Nakit</option><option>Banka Kartı</option><option>Kredi Kartı</option></select></label>  
  <label class="text-xs muted">Tarih<input id="tx-date" class="field" type="date" required></label>  
  <label class="text-xs muted">Açıklama<input id="tx-desc" class="field" placeholder="Örn: Market alışverişi"></label>  
  <button class="btn bg-emerald-600 w-full mt-2">Kaydet</button>  
 </form>  
</div></div>  
  
<div id="category-modal" class="modal"><div class="modal-panel space-y-3">  
 <div class="flex justify-between"><b id="category-modal-title">Kategori ekle</b><button onclick="closeModal('category-modal')">✕</button></div>  
 <form onsubmit="saveCategory(event)" class="space-y-2">  
  <input id="cat-name" class="field" placeholder="Kategori adı" required>  
  <input id="cat-budget" class="field" type="number" min="0" step="0.01" placeholder="Aylık bütçe (TL)" required>  
  <button class="btn bg-emerald-600 w-full">Kaydet</button>  
 </form>  
</div></div>  
  
<div id="cc-modal" class="modal"><div class="modal-panel space-y-3">  
 <div class="flex justify-between"><b>Kredi kartı bilgileri</b><button onclick="closeModal('cc-modal')">✕</button></div>  
 <form onsubmit="saveCC(event)" class="space-y-2">  
  <label class="text-xs muted">Mevcut toplam borç<input id="cc-debt-input" class="field" type="number" min="0" step="0.01" required></label>  
  <label class="text-xs muted">Planlanan ek harcama<input id="cc-planned-input" class="field" type="number" min="0" step="0.01" required></label>  
  <button class="btn bg-emerald-600 w-full">Kaydet</button>  
 </form>  
</div></div>  
  
<div id="loan-modal" class="modal"><div class="modal-panel space-y-3">  
 <div class="flex justify-between"><b>Kredi bilgileri</b><button onclick="closeModal('loan-modal')">✕</button></div>  
 <form onsubmit="saveLoan(event)" class="space-y-2">  
  <label class="text-xs muted">Aylık taksit (TL)<input id="loan-amount-input" class="field" type="number" min="0" step="0.01" required></label>  
  <label class="text-xs muted">Toplam taksit sayısı<input id="loan-total-input" class="field" type="number" min="0" step="1" required></label>  
  <label class="text-xs muted">Ödenen taksit sayısı<input id="loan-paid-input" class="field" type="number" min="0" step="1" required></label>  
  <button class="btn bg-emerald-600 w-full">Kaydet</button>  
 </form>  
</div></div>  
  
<div id="settings-modal" class="modal"><div class="modal-panel space-y-3">  
 <div class="flex justify-between"><b>Finansal parametreler</b><button onclick="closeModal('settings-modal')">✕</button></div>  
 <form onsubmit="saveSettings(event)" class="space-y-2">  
  <label class="text-xs muted">Aylık net maaş<input id="set-salary" class="field" type="number" min="0" step="0.01" required></label>  
  <label class="text-xs muted">Aylık kredi taksiti<input id="set-loan" class="field" type="number" min="0" step="0.01" required></label>  
  <label class="text-xs muted">Kira ve faturalar toplamı<input id="set-bills" class="field" type="number" min="0" step="0.01" required></label>  
  <label class="text-xs muted">Nakit birikim<input id="set-savings" class="field" type="number" min="0" step="0.01" required></label>  
  <button class="btn bg-emerald-600 w-full">Güncelle</button>  
 </form>  
</div></div>  
  
<div id="export-modal" class="modal"><div class="modal-panel space-y-3">  
 <div class="flex justify-between"><b>Veri yedekleme</b><button onclick="closeModal('export-modal')">✕</button></div>  
 <button class="btn bg-slate-700 w-full" onclick="exportJSON()">JSON olarak yedekle</button>  
 <label class="text-xs muted">Yedekten geri yükle<input class="field" type="file" accept=".json,application/json" onchange="importJSON(event)"></label>  
 <p class="text-xs muted">Veriler bu tarayıcının yerel hafızasında tutulur. Tarayıcı verilerini silmeden önce yedek al.</p>  
</div></div>  
  
<script>  
const DEFAULT={  
 settings:{salary:94000,loan:45000,bills:15000,ccDebt:91140.42,ccPlannedSpend:35000,savings:0},  
 categories:[  
 {name:"Market",budget:10000},{name:"Dışarıda yemek",budget:5000},  
 {name:"Yakıt",budget:4000},{name:"Ulaşım",budget:2000},  
 {name:"Faturalar",budget:5000},{name:"Kira",budget:10000},  
 {name:"Kişisel harcamalar",budget:4000},{name:"Alışveriş",budget:6000},  
 {name:"Eğlence",budget:3000},{name:"Sağlık",budget:2000},{name:"Diğer",budget:3000}],  
 transactions:[],loanInfo:{totalCount:24,paidCount:8,monthlyAmount:45000}  
};  
  
function loadState(){  
 try{  
  const x=JSON.parse(localStorage.getItem("cuzdanim_state"));  
  if(!x)return structuredClone(DEFAULT);  
  return {...structuredClone(DEFAULT),...x,  
   settings:{...DEFAULT.settings,...x.settings},  
   loanInfo:{...DEFAULT.loanInfo,...x.loanInfo},  
   categories:Array.isArray(x.categories)?x.categories:DEFAULT.categories,  
   transactions:Array.isArray(x.transactions)?x.transactions:[]};  
 }catch(e){return structuredClone(DEFAULT)}  
}  
let state=loadState(),chart=null,editingCategory=null;  
const $=id=>document.getElementById(id);  
const money=n=>new Intl.NumberFormat("tr-TR",{style:"currency",currency:"TRY"}).format(Number(n)||0);  
const num=id=>Number($(id).value)||0;  
function monthTransactions(){  
 const m=new Date().toISOString().slice(0,7);  
 return state.transactions.filter(t=>(t.date||"").slice(0,7)===m);  
}  
function persist(){  
 localStorage.setItem("cuzdanim_state",JSON.stringify(state));  
 renderAll();  
}  
function openModal(id){$(id).classList.add("show")}  
function closeModal(id){$(id).classList.remove("show")}  
function switchTab(id){  
 document.querySelectorAll(".tab-content").forEach(x=>x.classList.remove("active"));  
 $("tab-"+id).classList.add("active");  
 ["home","transactions","budget","debts","reports"].forEach(k=>$("nav-"+k).classList.toggle("text-emerald-400",k===id));  
 window.scrollTo(0,0);  
 if(id==="reports")renderChart();  
}  
function renderAll(){  
 const s=state.settings,tx=monthTransactions();  
 const expenses=tx.filter(t=>t.type==="expense").reduce((a,t)=>a+t.amount,0);  
 const income=tx.filter(t=>t.type==="income").reduce((a,t)=>a+t.amount,0);  
 const totalExpense=expenses+s.loan+s.bills;  
 const available=s.salary+income-totalExpense;  
 $("salary-label").textContent=money(s.salary+income);  
 $("expense-label").textContent=money(totalExpense);  
 $("available").textContent=money(available);  
 $("available").className="text-3xl font-extrabold mt-1 "+(available>=0?"text-emerald-400":"text-rose-400");  
 const spent=state.categories.reduce((sum,c)=>sum+tx.filter(t=>t.type==="expense"&&t.category===c.name).reduce((a,t)=>a+t.amount,0),0);  
 const budgets=state.categories.reduce((a,c)=>a+c.budget,0);  
 $("budget-left").textContent=money(budgets-spent);  
 $("savings-label").textContent=money(s.savings);  
 $("debt-label").textContent=money(s.ccDebt);  
 $("loan-label").textContent=money(s.loan);  
 $("bills-label").textContent=money(s.bills);  
 const pct=Math.min(100,Math.max(0,s.savings/100000*100));  
 $("goal-percent").textContent="%"+Math.round(pct);  
 $("goal-bar").style.width=pct+"%";  
 $("cc-current").textContent=money(s.ccDebt);  
 $("cc-planned").textContent=money(s.ccPlannedSpend);  
 const l=state.loanInfo;  
 $("loan-monthly").textContent=money(l.monthlyAmount);  
 $("loan-progress").textContent=`${l.paidCount} / ${l.totalCount} Ay`;  
 $("loan-remaining").textContent=money(Math.max(0,l.totalCount-l.paidCount)*l.monthlyAmount);  
 renderTransactions();renderBudgets();populateCategories();  
}  
function renderTransactions(){  
 const q=($("tx-search")?.value||"").toLocaleLowerCase("tr"),list=$("tx-list");  
 const items=[...state.transactions].filter(t=>(t.desc||"").toLocaleLowerCase("tr").includes(q)||(t.category||"").toLocaleLowerCase("tr").includes(q)).sort((a,b)=>(b.date||"").localeCompare(a.date||""));  
 list.innerHTML=items.length?"":"<p class='text-center text-xs muted py-6'>Kayıtlı işlem bulunamadı.</p>";  
 items.forEach(t=>{  
  const div=document.createElement("div");div.className="card flex justify-between items-center gap-2";  
  const left=document.createElement("div");  
  left.innerHTML="<p class='text-sm font-semibold'></p><p class='text-[10px] muted mt-1'></p>";  
  left.children[0].textContent=t.desc||t.category;  
  left.children[1].textContent=`${t.category} • ${t.payment} • ${t.date}`;  
  const right=document.createElement("div");right.className="flex items-center gap-3";  
  const amount=document.createElement("b");amount.className="text-sm "+(t.type==="income"?"text-emerald-400":"text-slate-100");  
  amount.textContent=(t.type==="income"?"+":"−")+money(t.amount);  
  const del=document.createElement("button");del.className="text-rose-400 text-xs";del.textContent="Sil";  
  del.onclick=()=>{if(confirm("Bu işlem silinsin mi?")){state.transactions=state.transactions.filter(x=>x.id!==t.id);persist()}};  
  right.append(amount,del);div.append(left,right);list.append(div);  
 });  
}  
function renderBudgets(){  
 const list=$("budget-list");list.innerHTML="";  
 state.categories.forEach((c,i)=>{  
  const spent=monthTransactions().filter(t=>t.type==="expense"&&t.category===c.name).reduce((a,t)=>a+t.amount,0);  
  const pct=c.budget>0?Math.min(100,spent/c.budget*100):0,over=spent>c.budget;  
  const box=document.createElement("div");box.className="card space-y-2";  
  const head=document.createElement("div");head.className="flex justify-between items-center gap-2";  
  const name=document.createElement("b");name.className="text-sm";name.textContent=c.name;  
  const amount=document.createElement("span");amount.className="text-xs "+(over?"text-rose-400":"muted");amount.textContent=`${money(spent)} / ${money(c.budget)}`;  
  head.append(name,amount);  
  const bar=document.createElement("div");bar.className="h-2 rounded-full bg-slate-900 overflow-hidden";  
  const fill=document.createElement("div");fill.className="h-full rounded-full "+(over?"bg-rose-500":"bg-emerald-500");fill.style.width=pct+"%";bar.append(fill);  
  const actions=document.createElement("div");actions.className="flex justify-end gap-3";  
  const edit=document.createElement("button");edit.className="text-xs text-blue-400";edit.textContent="Düzenle";edit.onclick=()=>openCategoryModal(i);  
  const del=document.createElement("button");del.className="text-xs text-rose-400";del.textContent="Sil";  
  del.onclick=()=>{if(confirm(c.name+" kategorisi silinsin mi?")){state.categories.splice(i,1);persist()}};  
  actions.append(edit,del);box.append(head,bar,actions);list.append(box);  
 });  
}  
function populateCategories(){  
 const sel=$("tx-category");sel.innerHTML="";  
 state.categories.forEach(c=>{const o=document.createElement("option");o.value=c.name;o.textContent=c.name;sel.append(o)});  
}  
function openTxModal(){  
 if(!state.categories.length)return alert("Önce bir kategori eklemelisin.");  
 $("tx-date").value=new Date().toISOString().slice(0,10);  
 populateCategories();openModal("tx-modal");  
}  
function saveTransaction(e){  
 e.preventDefault();const amount=num("tx-amount");  
 if(amount<=0)return alert("Tutar sıfırdan büyük olmalı.");  
 state.transactions.push({  
  id:crypto.randomUUID?crypto.randomUUID():String(Date.now()),  
  type:$("tx-type").value,amount,category:$("tx-category").value,  
  payment:$("tx-payment").value,date:$("tx-date").value,desc:$("tx-desc").value.trim()  
 });  
 persist();closeModal("tx-modal");$("tx-form").reset();  
}  
function openCategoryModal(i=null){  
 editingCategory=i;  
 $("category-modal-title").textContent=i===null?"Kategori ekle":"Kategori düzenle";  
 $("cat-name").value=i===null?"":state.categories[i].name;  
 $("cat-budget").value=i===null?"":state.categories[i].budget;  
 openModal("category-modal");  
}  
function saveCategory(e){  
 e.preventDefault();  
 const name=$("cat-name").value.trim(),budget=num("cat-budget");  
 if(!name)return;  
 if(editingCategory===null){  
  if(state.categories.some(c=>c.name.toLocaleLowerCase("tr")===name.toLocaleLowerCase("tr")))return alert("Bu kategori zaten var.");  
  state.categories.push({name,budget});  
 }else{  
  const old=state.categories[editingCategory].name;  
  if(state.categories.some((c,i)=>i!==editingCategory&&c.name.toLocaleLowerCase("tr")===name.toLocaleLowerCase("tr")))return alert("Bu kategori zaten var.");  
  state.categories[editingCategory]={name,budget};  
  state.transactions.forEach(t=>{if(t.category===old)t.category=name});  
 }  
 persist();closeModal("category-modal");  
}  
function openCCModal(){  
 $("cc-debt-input").value=state.settings.ccDebt;  
 $("cc-planned-input").value=state.settings.ccPlannedSpend;  
 openModal("cc-modal");  
}  
function saveCC(e){  
 e.preventDefault();state.settings.ccDebt=num("cc-debt-input");  
 state.settings.ccPlannedSpend=num("cc-planned-input");  
 persist();closeModal("cc-modal");  
}  
function openLoanModal(){  
 const l=state.loanInfo;  
 $("loan-amount-input").value=l.monthlyAmount;  
 $("loan-total-input").value=l.totalCount;  
 $("loan-paid-input").value=l.paidCount;  
 openModal("loan-modal");  
}  
function saveLoan(e){  
 e.preventDefault();  
 const total=Math.floor(num("loan-total-input")),paid=Math.floor(num("loan-paid-input"));  
 if(paid>total)return alert("Ödenen taksit, toplam taksitten fazla olamaz.");  
 state.loanInfo={monthlyAmount:num("loan-amount-input"),totalCount:total,paidCount:paid};  
 state.settings.loan=state.loanInfo.monthlyAmount;  
 persist();closeModal("loan-modal");  
}  
function openSettingsModal(){  
 const s=state.settings;  
 $("set-salary").value=s.salary;$("set-loan").value=s.loan;  
 $("set-bills").value=s.bills;$("set-savings").value=s.savings;  
 openModal("settings-modal");  
}  
function saveSettings(e){  
 e.preventDefault();  
 Object.assign(state.settings,{salary:num("set-salary"),loan:num("set-loan"),bills:num("set-bills"),savings:num("set-savings")});  
 state.loanInfo.monthlyAmount=state.settings.loan;  
 persist();closeModal("settings-modal");  
}  
function exportJSON(){  
 const blob=new Blob([JSON.stringify(state,null,2)],{type:"application/json"});  
 const url=URL.createObjectURL(blob),a=document.createElement("a");  
 a.href=url;a.download="cuzdanim-yedek-"+new Date().toISOString().slice(0,10)+".json";  
 a.click();URL.revokeObjectURL(url);  
}  
function importJSON(e){  
 const f=e.target.files[0];if(!f)return;  
 const r=new FileReader();  
 r.onload=()=>{  
  try{  
   const x=JSON.parse(r.result);  
   if(!x.settings||!Array.isArray(x.categories)||!Array.isArray(x.transactions))throw Error();  
   state={...structuredClone(DEFAULT),...x,settings:{...DEFAULT.settings,...x.settings},loanInfo:{...DEFAULT.loanInfo,...x.loanInfo}};  
   persist();closeModal("export-modal");alert("Yedek başarıyla yüklendi.");  
  }catch(err){alert("Yedek dosyası geçersiz veya bozuk.")}  
 };  
 r.readAsText(f);  
}  
function renderChart(){  
 const canvas=$("expenseChart");if(!canvas||!window.Chart)return;  
 const totals={};  
 monthTransactions().filter(t=>t.type==="expense").forEach(t=>totals[t.category]=(totals[t.category]||0)+t.amount);  
 const labels=Object.keys(totals).filter(k=>totals[k]>0);  
 if(chart)chart.destroy();  
 if(!labels.length){const ctx=canvas.getContext("2d");ctx.clearRect(0,0,canvas.width,canvas.height);return}  
 chart=new Chart(canvas,{  
  type:"doughnut",  
  data:{labels,datasets:[{data:labels.map(k=>totals[k]),backgroundColor:["#10b981","#3b82f6","#f59e0b","#ef4444","#8b5cf6","#ec4899","#14b8a6","#f97316","#6366f1","#84cc16","#64748b"]}]},  
  options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{position:"bottom",labels:{color:"#94a3b8",boxWidth:12,font:{size:10}}}}}  
 });  
}  
renderAll();  
</script>  
</body>  
</html>  
