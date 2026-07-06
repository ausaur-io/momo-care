<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>毛毛醫療護理日誌</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    html, body { height: 100%; }
    body.modal-open { overflow: hidden; }
    .modal-scroll { overflow-y: auto; -webkit-overflow-scrolling: touch; }
  </style>
</head>
<body class="bg-gray-100 text-gray-800 pb-24">

  <header class="bg-teal-600 text-white p-4 shadow-md sticky top-0 z-50 flex justify-between items-center">
    <div class="flex items-center gap-2">
      <button onclick="goHome()" class="w-10 h-10 rounded-full bg-teal-700 flex items-center justify-center shadow">
        <i class="fa-solid fa-cat text-lg"></i>
      </button>
      <div>
        <h1 class="text-xl font-bold"><i class="fa-solid fa-cat mr-2"></i>毛毛護理日誌</h1>
        <p class="text-[10px] opacity-80">TechX Lab Firebase 測試版 v0.1</p>
      </div>
    </div>
    <div class="flex items-center space-x-2">
      <button onclick="openCalendarModal()" class="bg-teal-700 hover:bg-teal-800 text-white p-2 rounded-lg text-sm shadow font-bold flex items-center gap-1">
        <i class="fa-solid fa-calendar-days text-base"></i> 歷史日曆
      </button>
      <button onclick="manualSave()" class="bg-yellow-500 hover:bg-yellow-600 text-gray-900 font-bold text-xs px-2.5 py-2 rounded-lg shadow">
        <i class="fa-solid fa-floppy-disk"></i> 儲存
      </button>
    </div>
  </header>

  <main class="max-w-md mx-auto p-4 space-y-4">

    <section class="bg-white p-4 rounded-xl shadow">
      <div class="flex items-center space-x-4 mb-4">
        <div class="w-16 h-16 bg-teal-100 rounded-full flex items-center justify-center text-3xl shadow-inner">🐱</div>
        <div>
          <h2 class="text-lg font-bold">毛毛 (腎病護理中)</h2>
          <p class="text-xs text-gray-500">今日心情：<span id="moodDisplay" class="font-medium text-teal-600">未紀錄</span></p>
        </div>
      </div>

      <div class="grid grid-cols-3 gap-2 mb-4">
        <button onclick="setMood('😺 精靈')" class="p-2 border rounded-lg text-sm bg-gray-50 hover:bg-teal-50">😺 精靈</button>
        <button onclick="setMood('😿 攰')" class="p-2 border rounded-lg text-sm bg-gray-50 hover:bg-yellow-50">😿 攰</button>
        <button onclick="setMood('🤮 嘔吐')" class="p-2 border rounded-lg text-sm bg-gray-50 hover:bg-red-50">🤮 嘔吐</button>
      </div>

      <div class="grid grid-cols-2 gap-3 border-t pt-3">
        <div class="bg-blue-50 p-3 rounded-lg text-center">
          <span class="text-xs text-blue-600 font-bold block"><i class="fa-solid fa-restroom mr-1"></i>今日小便</span>
          <span class="text-2xl font-black text-blue-700" id="peeCount">0</span> 次
          <button onclick="changeCounter('pee', 1)" class="mt-2 block w-full bg-blue-500 text-white text-xs py-1 rounded font-bold">+</button>
        </div>
        <div class="bg-amber-50 p-3 rounded-lg text-center">
          <span class="text-xs text-amber-600 font-bold block"><i class="fa-solid fa-poop mr-1"></i>今日肚痾/排便</span>
          <span class="text-2xl font-black text-amber-700" id="poopCount">0</span> 次
          <button onclick="triggerPoopRecord()" class="mt-2 block w-full bg-amber-500 text-white text-xs py-1 rounded font-bold">紀錄 + 影相</button>
        </div>
      </div>
    </section>

    <section id="backlogSection" class="hidden bg-orange-50 border-2 border-orange-300 p-4 rounded-xl shadow">
      <h3 class="text-orange-800 font-bold text-sm mb-2"><i class="fa-solid fa-triangle-exclamation mr-1"></i> ⚠️ 早上有未食藥物！請記得補番：</h3>
      <div id="backlogList" class="space-y-2"></div>
    </section>

    <section class="bg-white p-4 rounded-xl shadow">
      <h3 class="text-md font-bold mb-3 border-b pb-2 flex justify-between items-center">
        <span><i class="fa-solid fa-clock text-teal-600 mr-2"></i>今日行程 Checklist</span>
        <button onclick="resetDay()" class="text-xs text-red-500 hover:underline">重設今日</button>
      </h3>
      <div class="space-y-4" id="timelineList"></div>
    </section>

    <section class="bg-white p-4 rounded-xl shadow">
      <h3 class="text-md font-bold mb-3 border-b pb-2"><i class="fa-solid fa-file-medical text-red-500 mr-2"></i>醫療報告庫 (最多可上傳5個檔案)</h3>

      <div class="bg-gray-50 p-3 rounded-lg border border-dashed border-gray-300 mb-4 space-y-3">
        <div>
          <label class="block text-xs font-bold text-gray-600 mb-1">1. 輸入報告備忘名稱：</label>
          <input type="text" id="reportName" placeholder="例如：7月6日 驗血報告" class="w-full border p-2 rounded text-sm bg-white">
        </div>
        <div>
          <label class="block text-xs font-bold text-gray-600 mb-1">2. 選擇檔案 (最多5個)：<span id="fileCountTxt" class="text-teal-600 font-bold">(0/5)</span></label>
          <div class="flex items-center gap-2">
            <input type="file" id="realFilePicker" multiple accept="image/*,application/pdf" onchange="checkFileCount(this)" class="w-full text-xs text-gray-500 file:mr-3 file:py-1.5 file:px-3 file:rounded file:border-0 file:text-xs file:font-semibold file:bg-teal-50 file:text-teal-700 hover:file:bg-teal-100">
            <button type="button" onclick="clearRealFilePicker()" class="shrink-0 w-9 h-9 rounded-full bg-red-500 text-white font-bold flex items-center justify-center">×</button>
          </div>
        </div>
        <button onclick="handleRealUpload()" class="w-full bg-teal-600 text-white text-sm py-2 rounded font-bold hover:bg-teal-700 transition">
          <i class="fa-solid fa-cloud-arrow-up mr-1"></i> 載入檔案至報告庫
        </button>
      </div>

      <ul id="reportList" class="space-y-2 text-sm divide-y divide-gray-100"></ul>
    </section>
  </main>

  <div id="previewModal" class="fixed inset-0 bg-black/90 z-50 flex flex-col items-center justify-center p-4 hidden modal-scroll" onclick="closePreviewModal()">
    <p class="text-white text-xs mb-2"><i class="fa-solid fa-circle-xmark mr-1"></i> 點擊任何地方關閉預覽</p>
    <div class="max-w-full max-h-[80vh] overflow-auto bg-white p-2 rounded-lg" onclick="event.stopPropagation()">
      <img id="previewImg" src="" alt="報告預覽" class="max-w-full h-auto mx-auto hidden">
      <div id="previewPdfText" class="p-8 text-center text-gray-800 hidden">
        <i class="fa-solid fa-file-pdf text-5xl text-red-500 mb-2"></i>
        <p class="font-bold">PDF 文件無法在安全環境中直接預覽</p>
      </div>
    </div>
    <h4 id="previewTitle" class="text-white font-bold mt-4 text-center"></h4>
  </div>

  <div id="calendarModal" class="fixed inset-0 bg-black/60 z-45 flex items-center justify-center p-4 hidden modal-scroll" onclick="closeCalendarModalByBg(event)">
    <div class="bg-white rounded-xl p-4 w-full max-w-sm space-y-4 max-h-[90vh] overflow-y-auto" onclick="event.stopPropagation()">
      <div class="flex justify-between items-center border-b pb-2">
        <h3 class="font-bold text-lg text-teal-700"><i class="fa-solid fa-calendar-week mr-2"></i>7月份 護理進度追蹤</h3>
        <button onclick="closeCalendarModal()" class="text-gray-400 text-xl"><i class="fa-solid fa-xmark"></i></button>
      </div>

      <div class="flex items-center justify-between">
        <button type="button" onclick="changeCalendarDay(-1)" class="px-3 py-1 rounded bg-gray-100 text-gray-700 text-sm font-bold">◀ 前一日</button>
        <div id="calendarDateTxt" class="text-sm font-bold text-teal-700">2026-07-06</div>
        <button type="button" onclick="changeCalendarDay(1)" class="px-3 py-1 rounded bg-gray-100 text-gray-700 text-sm font-bold">後一日 ▶</button>
      </div>

      <p class="text-[11px] text-gray-500">🟢 全部完成 | 🔴 有未完成 | 未到日期不顯示紅點</p>

      <div class="grid grid-cols-7 gap-1.5 text-center text-xs font-bold bg-gray-50 p-2 rounded-lg" id="calendarGrid">
        <div class="text-gray-400 py-1">日</div><div class="text-gray-400 py-1">一</div><div class="text-gray-400 py-1">二</div><div class="text-gray-400 py-1">三</div><div class="text-gray-400 py-1">四</div><div class="text-gray-400 py-1">五</div><div class="text-gray-400 py-1">六</div>
      </div>

      <div class="border-t pt-3 space-y-2">
        <h4 class="text-xs font-bold text-gray-700"><i class="fa-solid fa-database mr-1"></i> 當日漏藥：</h4>
        <div id="dayReportBox" class="text-xs bg-gray-50 border rounded p-3 space-y-2 max-h-48 overflow-y-auto"></div>
        <textarea id="rawDataBox" readonly class="w-full h-16 text-[9px] font-mono p-2 border rounded bg-gray-50 select-all"></textarea>
      </div>
    </div>
  </div>

  <div id="poopModal" class="fixed inset-0 bg-black/50 z-40 flex items-center justify-center p-4 hidden modal-scroll" onclick="closePoopModalByBg(event)">
    <div class="bg-white rounded-xl p-4 w-full max-w-sm space-y-4" onclick="event.stopPropagation()">
      <div class="flex justify-between items-center">
        <h3 class="font-bold text-lg">💩 排便/肚痾 詳細紀錄</h3>
        <button onclick="goHome()" class="w-10 h-10 rounded-full bg-teal-600 text-white flex items-center justify-center shadow">
          <i class="fa-solid fa-cat"></i>
        </button>
      </div>

      <div>
        <label class="block text-xs font-bold text-gray-600 mb-1">大便狀態：</label>
        <select id="poopType" class="w-full border p-2 rounded bg-gray-50">
          <option value="正常">正常便便</option>
          <option value="軟便">軟便</option>
          <option value="肚痾">🚨 肚痾</option>
        </select>
      </div>

      <div class="space-y-2">
        <div class="flex items-center justify-between">
          <label class="block text-xs font-bold text-gray-600">便便相片：</label>
          <button type="button" onclick="togglePoopCapture()" class="text-[11px] px-2 py-1 rounded bg-gray-100 text-gray-700 font-bold">
            切換：<span id="captureModeTxt">相機優先</span>
          </button>
        </div>
        <div class="flex items-center gap-2">
          <input type="file" id="poopFilePicker" accept="image/*" capture="environment" class="w-full text-xs text-gray-500 file:mr-3 file:py-1.5 file:px-3 file:rounded file:border-0 file:text-xs file:font-semibold file:bg-amber-50 file:text-amber-700 hover:file:bg-amber-100" onchange="previewPoopPhoto(this)">
          <button type="button" onclick="clearPoopPhoto()" class="w-9 h-9 rounded-full bg-red-500 text-white font-bold flex items-center justify-center">×</button>
        </div>
        <img id="poopPreviewImg" class="hidden w-full rounded border mt-2" alt="便便相片預覽">
      </div>

      <div class="flex justify-end space-x-2">
        <button onclick="closePoopModal()" class="px-4 py-2 text-sm text-gray-500 hover:bg-gray-100 rounded">取消</button>
        <button onclick="savePoopRecord()" class="px-4 py-2 text-sm bg-amber-500 text-white font-bold rounded">儲存紀錄</button>
      </div>
    </div>
  </div>

  <footer class="fixed bottom-0 left-0 right-0 bg-gray-900 text-white p-3 text-center text-xs shadow-lg">
    毛毛專屬護理 App MVP 測試版
  </footer>

  <!-- Firebase SDK (modular, via CDN) -->
  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.15.0/firebase-app.js";
    import { getDatabase, ref, set, update, onValue, push, remove } from "https://www.gstatic.com/firebasejs/12.15.0/firebase-database.js";

    window.firebaseReady = false;
    window.firebaseDb = null;

    const firebaseConfig = {
      apiKey: "PASTE_YOUR_API_KEY",
      authDomain: "PASTE_YOUR_AUTH_DOMAIN",
      databaseURL: "PASTE_YOUR_DATABASE_URL",
      projectId: "PASTE_YOUR_PROJECT_ID",
      storageBucket: "PASTE_YOUR_STORAGE_BUCKET",
      messagingSenderId: "PASTE_YOUR_MESSAGING_SENDER_ID",
      appId: "PASTE_YOUR_APP_ID"
    };

    try {
      const app = initializeApp(firebaseConfig);
      const db = getDatabase(app);
      window.firebaseDb = db;
      window.firebaseReady = true;
      console.log("Firebase initialized");
    } catch (e) {
      console.error("Firebase init failed:", e);
    }

    window.fbWrite = async function(path, data) {
      if (!window.firebaseReady) return false;
      try {
        await set(ref(window.firebaseDb, path), data);
        return true;
      } catch (e) {
        console.error("fbWrite failed", e);
        return false;
      }
    };

    window.fbUpdate = async function(path, data) {
      if (!window.firebaseReady) return false;
      try {
        await update(ref(window.firebaseDb, path), data);
        return true;
      } catch (e) {
        console.error("fbUpdate failed", e);
        return false;
      }
    };

    window.fbReadListen = function(path, callback) {
      if (!window.firebaseReady) return;
      onValue(ref(window.firebaseDb, path), (snap) => {
        callback(snap.val());
      });
    };
  </script>

  <script>
    var STORAGE_KEY = 'momo_app_state_v5_mix';
    var poopCaptureOn = true;
    var selectedDate = '2026-07-06';
    var poopPhotoData = '';
    var initialTasks = [
      { id: 't1', time: '06:00', name: '打皮下水 150 mL', type: 'water', period: 'morning', done: false, doneTime: '' },
      { id: 't2', time: '09:00', name: '降磷藥 + 保護胃 (飯前)', type: 'med', period: 'morning', done: false, doneTime: '' },
      { id: 't3', time: '09:30', name: '腎 Supplement (飯後)', type: 'med', period: 'morning', done: false, doneTime: '' },
      { id: 't4', time: '09:30', name: '止嘔藥 (飯後)', type: 'med', period: 'morning', done: false, doneTime: '' },
      { id: 't5', time: '09:30', name: '止柯藥 (飯後)', type: 'med', period: 'morning', done: false, doneTime: '' },
      { id: 't6', time: '09:30', name: '抗生素 (飯後)', type: 'med', period: 'morning', done: false, doneTime: '' },
      { id: 't7', time: '09:30', name: '化痰藥 (有需要時服)', type: 'med_opt', period: 'morning', done: false, doneTime: '' },
      { id: 't8', time: '09:30', name: '防敏藥 (有需要時服)', type: 'med_opt', period: 'morning', done: false, doneTime: '' },
      { id: 't9', time: '09:30', name: '開胃藥 (有需要時服)', type: 'med_opt', period: 'morning', done: false, doneTime: '' },
      { id: 't10', time: '09:30', name: '飲 Renal Liquid (第1次)', type: 'liquid', period: 'morning', done: false, doneTime: '' },
      { id: 't11', time: '22:00', name: '打皮下水 150 mL (第2次)', type: 'water', period: 'night', done: false, doneTime: '' },
      { id: 't12', time: '22:30', name: '降磷藥 + 保護胃 (第2次/飯前)', type: 'med', period: 'night', done: false, doneTime: '' },
      { id: 't13', time: '23:00', name: '腎 Supplement (第2次/飯後)', type: 'med', period: 'night', done: false, doneTime: '' },
      { id: 't14', time: '23:00', name: '飲 Renal Liquid (第2次)', type: 'liquid', period: 'night', done: false, doneTime: '' }
    ];

    function getDefaultState() {
      return {
        mood: '未紀錄',
        pee: 0,
        poop: 0,
        tasks: JSON.parse(JSON.stringify(initialTasks)),
        reports: [],
        historyLogs: {}
      };
    }

    function loadState() {
      try {
        var raw = localStorage.getItem(STORAGE_KEY);
        return raw ? JSON.parse(raw) : getDefaultState();
      } catch (e) {
        return getDefaultState();
      }
    }

    var appState = loadState();

    function safeSave() {
      try {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(appState));
        return true;
      } catch (e) {
        alert('本機儲存失敗。');
        return false;
      }
    }

    function syncAll() {
      safeSave();
      if (window.firebaseReady) {
        window.fbWrite('/momo/appState', appState);
      }
      renderApp();
    }

    function saveState() { syncAll(); }

    function setTodayHistory() {
      var today = "2026-07-06";
      var isAnyMedLeaked = appState.tasks.some(function(t) {
        return (t.type === 'med' || t.type === 'water') && !t.done;
      });
      appState.historyLogs[today] = { allDone: !isAnyMedLeaked };
    }

    function manualSave() {
      setTodayHistory();
      syncAll();
      alert('已儲存及同步。');
    }

    function setMood(mood) { appState.mood = mood; saveState(); }
    function changeCounter(field, amount) { appState[field] = Math.max(0, appState[field] + amount); saveState(); }

    function toggleTask(id) {
      var task = appState.tasks.find(function(t) { return t.id === id; });
      if (!task) return;
      if (!task.done) {
        var now = new Date();
        task.done = true;
        task.doneTime = now.getHours() + ':' + (now.getMinutes() < 10 ? '0' : '') + now.getMinutes();
      } else {
        if (confirm('確定要取消已餵食狀態嗎？')) {
          task.done = false;
          task.doneTime = '';
        }
      }
      setTodayHistory();
      saveState();
    }

    function checkFileCount(input) {
      if (input.files.length > 5) {
        alert('⚠️ 一次最多只能夠選擇 5 個檔案！');
        input.value = '';
        document.getElementById('fileCountTxt').innerText = "(0/5)";
      } else {
        document.getElementById('fileCountTxt').innerText = "(" + input.files.length + "/5)";
      }
    }

    function clearRealFilePicker() {
      document.getElementById('realFilePicker').value = '';
      document.getElementById('fileCountTxt').innerText = "(0/5)";
    }

    async function handleRealUpload() {
      var nameInput = document.getElementById('reportName');
      var fileInput = document.getElementById('realFilePicker');
      if (!nameInput.value.trim()) return alert('請先輸入報告備忘名稱');
      if (fileInput.files.length === 0) return alert('請選取檔案');

      var files = Array.from(fileInput.files);
      var now = new Date().toISOString().split('T')[0];
      for (var i = 0; i < files.length; i++) {
        var file = files[i];
        var reader = new FileReader();
        var dataUrl = await new Promise(function(resolve, reject) {
          reader.onload = function(e) { resolve(e.target.result); };
          reader.onerror = reject;
          reader.readAsDataURL(file);
        });

        appState.reports.unshift({
          id: 'r_' + Date.now() + '_' + i,
          name: nameInput.value + (files.length > 1 ? ' (' + (i + 1) + ')' : ''),
          date: now,
          fileData: dataUrl,
          fileType: file.type || ''
        });
      }

      nameInput.value = '';
      fileInput.value = '';
      document.getElementById('fileCountTxt').innerText = "(0/5)";
      setTodayHistory();
      saveState();
    }

    function togglePoopCapture() {
      poopCaptureOn = !poopCaptureOn;
      var input = document.getElementById('poopFilePicker');
      var txt = document.getElementById('captureModeTxt');
      if (poopCaptureOn) {
        input.setAttribute('capture', 'environment');
        txt.innerText = '相機優先';
      } else {
        input.removeAttribute('capture');
        txt.innerText = '可揀相簿';
      }
    }

    function clearPoopPhoto() {
      document.getElementById('poopFilePicker').value = '';
      poopPhotoData = '';
      var img = document.getElementById('poopPreviewImg');
      img.src = '';
      img.classList.add('hidden');
    }

    function previewPoopPhoto(input) {
      if (!input.files || !input.files[0]) return;
      var file = input.files[0];
      var img = document.getElementById('poopPreviewImg');
      var objectUrl = URL.createObjectURL(file);
      img.src = objectUrl;
      img.classList.remove('hidden');
      img.onload = function() { URL.revokeObjectURL(objectUrl); };
      poopPhotoData = '';
    }

    function savePoopRecord() {
      appState.poop += 1;
      var poopType = document.getElementById('poopType').value;
      var fileInput = document.getElementById('poopFilePicker');
      var file = fileInput.files && fileInput.files[0];

      if (file) {
        var reader = new FileReader();
        reader.onload = async function(e) {
          appState.reports.unshift({
            id: 'poop_' + Date.now(),
            name: '便便紀錄相片 - ' + poopType,
            date: new Date().toISOString().split('T')[0],
            fileData: e.target.result,
            fileType: file.type || 'image/*'
          });
          clearPoopPhoto();
          closePoopModal();
          setTodayHistory();
          saveState();
        };
        reader.readAsDataURL(file);
      } else {
        clearPoopPhoto();
        closePoopModal();
        setTodayHistory();
        saveState();
      }
    }

    function deleteReport(id) {
      appState.reports = appState.reports.filter(function(r) { return r.id !== id; });
      saveState();
    }

    function openImagePreview(id) {
      var rep = appState.reports.find(function(r) { return r.id === id; });
      if (!rep) return;
      document.getElementById('previewModal').classList.remove('hidden');
      document.getElementById('previewTitle').innerText = rep.name + " (" + rep.date + ")";
      var imgTag = document.getElementById('previewImg');
      var pdfTag = document.getElementById('previewPdfText');
      if (rep.fileData && rep.fileData.indexOf('data:image') === 0) {
        imgTag.src = rep.fileData;
        imgTag.classList.remove('hidden');
        pdfTag.classList.add('hidden');
      } else {
        imgTag.classList.add('hidden');
        pdfTag.classList.remove('hidden');
      }
    }

    function closePreviewModal() { document.getElementById('previewModal').classList.add('hidden'); }

    function changeCalendarDay(delta) {
      var d = new Date(selectedDate + 'T00:00:00');
      d.setDate(d.getDate() + delta);
      selectedDate = d.toISOString().split('T')[0];
      renderCalendarGrid();
      renderDayReport();
    }

    function renderCalendarGrid() {
      document.getElementById('calendarDateTxt').innerText = selectedDate;
      var grid = document.getElementById('calendarGrid');
      var oldDays = grid.querySelectorAll('.day-cell');
      oldDays.forEach(function(el) { el.remove(); });

      for (var s = 0; s < 3; s++) {
        var emp = document.createElement('div');
        emp.className = 'day-cell py-2';
        grid.appendChild(emp);
      }

      for (var d = 1; d <= 31; d++) {
        var dayStr = "2026-07-" + (d < 10 ? '0' : '') + d;
        var cell = document.createElement('div');
        cell.className = 'day-cell py-2 border rounded bg-white relative flex flex-col items-center justify-center cursor-pointer';
        cell.onclick = (function(dateStr) {
          return function() {
            selectedDate = dateStr;
            renderCalendarGrid();
            renderDayReport();
          };
        })(dayStr);

        var numSpan = document.createElement('span');
        numSpan.innerText = d;
        cell.appendChild(numSpan);

        var dot = document.createElement('span');
        var currentDay = new Date("2026-07-06T00:00:00");
        var thisDay = new Date(dayStr + 'T00:00:00');

        if (thisDay > currentDay) {
          dot.className = 'w-2 h-2 rounded-full mt-1 block opacity-0';
        } else if (appState.historyLogs[dayStr]) {
          dot.className = appState.historyLogs[dayStr].allDone
            ? 'w-2 h-2 rounded-full bg-green-500 mt-1 block'
            : 'w-2 h-2 rounded-full bg-red-500 mt-1 block';
        } else {
          dot.className = 'w-2 h-2 rounded-full bg-red-500 mt-1 block';
        }

        if (dayStr === selectedDate) {
          cell.classList.add('ring-2', 'ring-teal-500', 'bg-teal-50');
        }

        cell.appendChild(dot);
        grid.appendChild(cell);
      }
    }

    function renderDayReport() {
      var box = document.getElementById('dayReportBox');
      var missing = appState.tasks.filter(function(t) {
        return (t.type === 'med' || t.type === 'water') && !t.done;
      });

      var html = '<div class="font-bold">日期：' + selectedDate + '</div>';
      html += '<div class="font-bold">當日漏藥：</div>';
      if (missing.length === 0) {
        html += '<div class="text-green-600">全部完成</div>';
      } else {
        missing.forEach(function(t) {
          html += '<div class="bg-white border rounded p-2 mb-1">' + t.time + ' - ' + t.name + '</div>';
        });
      }
      box.innerHTML = html;
    }

    function openCalendarModal() {
      setTodayHistory();
      safeSave();
      document.body.classList.add('modal-open');
      document.getElementById('calendarModal').classList.remove('hidden');
      document.getElementById('rawDataBox').value = JSON.stringify(appState);
      renderCalendarGrid();
      renderDayReport();
    }

    function closeCalendarModal() {
      document.getElementById('calendarModal').classList.add('hidden');
      document.body.classList.remove('modal-open');
    }

    function closeCalendarModalByBg(event) {
      if (event.target.id === 'calendarModal') closeCalendarModal();
    }

    function triggerPoopRecord() {
      document.body.classList.add('modal-open');
      document.getElementById('poopModal').classList.remove('hidden');
    }

    function closePoopModal() {
      document.getElementById('poopModal').classList.add('hidden');
      document.body.classList.remove('modal-open');
    }

    function closePoopModalByBg(event) {
      if (event.target.id === 'poopModal') closePoopModal();
    }

    function goHome() {
      closePoopModal();
      closeCalendarModal();
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    function resetDay() {
      if (confirm('確定要重設今日數據嗎？')) {
        appState.mood = '未紀錄';
        appState.pee = 0;
        appState.poop = 0;
        appState.tasks = JSON.parse(JSON.stringify(initialTasks));
        saveState();
      }
    }

    function renderApp() {
      document.getElementById('moodDisplay').innerText = appState.mood;
      document.getElementById('peeCount').innerText = appState.pee;
      document.getElementById('poopCount').innerText = appState.poop;

      var backlogTasks = appState.tasks.filter(function(t) {
        return t.period === 'morning' && t.type === 'med' && !t.done;
      });

      var backlogSection = document.getElementById('backlogSection');
      if (backlogTasks.length > 0) {
        backlogSection.classList.remove('hidden');
        var bHtml = '';
        backlogTasks.forEach(function(t) {
          bHtml += '<div class="flex justify-between items-center bg-white p-2 rounded border border-orange-200 mb-1">' +
            '<span class="text-xs font-bold text-red-600">⚠️ [早上漏食] ' + t.name + '</span>' +
            '<button onclick="toggleTask(\'' + t.id + '\')" class="bg-orange-500 text-white text-xs px-2 py-1 rounded font-bold">今晚補食</button>' +
          '</div>';
        });
        document.getElementById('backlogList').innerHTML = bHtml;
      } else {
        backlogSection.classList.add('hidden');
      }

      var timelineHtml = '';
      appState.tasks.forEach(function(t) {
        var isDone = t.done;
        var badge = '';
        if (t.type === 'water') badge = '<span class="bg-blue-100 text-blue-700 px-1 text-[10px] rounded font-bold">皮下水</span>';
        if (t.type === 'med') badge = '<span class="bg-purple-100 text-purple-700 px-1 text-[10px] rounded font-bold">必服藥</span>';
        if (t.type === 'med_opt') badge = '<span class="bg-gray-100 text-gray-600 px-1 text-[10px] rounded font-bold">有需要</span>';
        if (t.type === 'liquid') badge = '<span class="bg-teal-100 text-teal-700 px-1 text-[10px] rounded font-bold">營養液</span>';

        timelineHtml += '<div class="flex items-start space-x-3 p-2.5 rounded-lg border ' + (isDone ? 'bg-gray-50 opacity-60' : 'bg-white') + '">' +
          '<button onclick="toggleTask(\'' + t.id + '\')" class="text-xl focus:outline-none">' +
            (isDone ? '<i class="fa-solid fa-circle-check text-teal-500"></i>' : '<i class="fa-regular fa-circle text-gray-300"></i>') +
          '</button>' +
          '<div class="flex-1">' +
            '<div class="flex items-center space-x-2">' +
              '<span class="text-xs font-bold ' + (isDone ? 'text-gray-400' : 'text-teal-600') + '">' + t.time + '</span>' +
              badge +
            '</div>' +
            '<p class="text-sm ' + (isDone ? 'line-through text-gray-400' : 'text-gray-800') + '">' + t.name + '</p>' +
            (isDone ? '<p class="text-[10px] text-gray-400">打卡時間: ' + t.doneTime + '</p>' : '') +
          '</div>' +
        '</div>';
      });
      document.getElementById('timelineList').innerHTML = timelineHtml;

      var reportHtml = '';
      appState.reports.forEach(function(r) {
        reportHtml += '<li class="py-2.5 flex justify-between items-center border-b border-gray-100 gap-2">' +
          '<div class="truncate pr-2 flex-1">' +
            '<p class="font-medium text-gray-800 text-sm"><i class="fa-solid fa-file-image mr-2 text-teal-500"></i>' + r.name + '</p>' +
            '<p class="text-[10px] text-gray-400 ml-6">' + r.date + '</p>' +
          '</div>' +
          '<div class="flex items-center gap-2">' +
            '<button onclick="openImagePreview(\'' + r.id + '\')" class="bg-teal-600 text-white text-xs px-2.5 py-1 rounded font-bold hover:bg-teal-700">查看檔案</button>' +
            '<button onclick="deleteReport(\'' + r.id + '\')" class="w-7 h-7 rounded-full bg-red-500 text-white font-bold flex items-center justify-center">×</button>' +
          '</div>' +
        '</li>';
      });
      document.getElementById('reportList').innerHTML = reportHtml;
    }

    renderApp();
  </script>
</body>
</html>
