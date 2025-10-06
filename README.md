# Photo<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>📸 Моя фотогалерея</title>
  <style>
    :root{
      --bg:#f5f7fb;
      --card:#ffffff;
      --accent:#3b82f6;
      --muted:#6b7280;
      --radius:12px;
      --shadow: 0 6px 18px rgba(15,23,42,0.08);
      font-family: Inter, "Segoe UI", Roboto, sans-serif;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      background:var(--bg);
      color:#111827;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      padding:28px;
      display:flex;
      flex-direction:column;
      min-height:100vh;
      align-items:center;
    }
    header{
      text-align:center;
      margin-bottom:18px;
    }
    h1{margin:0;font-size:22px}
    p.lead{margin:6px 0 0;color:var(--muted);font-size:13px}

    .container{
      width:100%;
      max-width:980px;
    }

    .uploader{
      background:var(--card);
      border-radius:var(--radius);
      padding:14px;
      box-shadow:var(--shadow);
      display:flex;
      gap:12px;
      align-items:center;
      justify-content:space-between;
    }

    .left{
      display:flex;
      gap:12px;
      align-items:center;
      flex:1;
      min-width:0;
    }

    .dropzone{
      flex:1;
      border:2px dashed #e6eefc;
      border-radius:10px;
      padding:10px;
      text-align:center;
      cursor:pointer;
      background:linear-gradient(180deg, rgba(59,130,246,0.03), transparent);
      color:var(--muted);
      font-size:13px;
      min-height:56px;
      display:flex;
      align-items:center;
      justify-content:center;
    }
    .dropzone.drag{
      border-color: var(--accent);
      background: linear-gradient(180deg, rgba(59,130,246,0.08), rgba(59,130,246,0.02));
      color: #0b4bd6;
    }

    input[type="file"]{ display:none }

    .actions{
      display:flex;
      gap:8px;
      align-items:center;
    }
    button{
      background:var(--accent);
      color:white;
      border:0;
      padding:10px 14px;
      border-radius:10px;
      cursor:pointer;
      font-size:14px;
      box-shadow: 0 6px 14px rgba(59,130,246,0.12);
    }
    button.ghost{
      background:transparent;
      color:var(--muted);
      box-shadow:none;
      border:1px solid #e6eefc;
    }

    .controls{
      margin-top:16px;
      display:flex;
      gap:10px;
      align-items:center;
      justify-content:flex-end;
    }

    .gallery{
      margin-top:20px;
      display:grid;
      grid-template-columns: repeat(auto-fill, minmax(160px,1fr));
      gap:14px;
    }

    .card{
      background:var(--card);
      border-radius:12px;
      overflow:hidden;
      box-shadow:var(--shadow);
      position:relative;
      display:flex;
      flex-direction:column;
    }
    .card img{ display:block; width:100%; height:auto; object-fit:cover; }

    .thumb-row{
      display:flex;
      gap:8px;
      padding:8px;
      align-items:center;
      justify-content:space-between;
    }
    .meta{
      font-size:12px;
      color:var(--muted);
      overflow:hidden;
      text-overflow:ellipsis;
      white-space:nowrap;
      max-width:120px;
    }

    .small-btn{
      background:rgba(0,0,0,0.6);
      color:white;
      border:0;
      padding:6px 8px;
      border-radius:8px;
      cursor:pointer;
      font-size:13px;
    }

    .small-btn.danger{ background: #ef4444 }

    footer{ margin-top:auto; padding-top:20px; color:var(--muted); font-size:13px; }

    /* modal */
    .modal{
      position:fixed;
      inset:0;
      display:none;
      align-items:center;
      justify-content:center;
      z-index:999;
      background:rgba(3,7,18,0.5);
    }
    .modal.show{ display:flex }
    .modal .win{
      max-width:92%;
      max-height:92%;
      border-radius:10px;
      overflow:hidden;
      background:var(--card);
      box-shadow: 0 20px 60px rgba(2,6,23,0.6);
      display:flex;
      flex-direction:column;
    }
    .modal img{ max-width:100%; max-height:80vh; display:block; margin:0 auto; }
    .modal .m-row{ padding:10px; display:flex; gap:10px; justify-content:flex-end; }

    @media (max-width:520px){
      .uploader{ flex-direction:column; align-items:stretch }
      .actions{ justify-content:space-between }
    }
  </style>
</head>
<body>
  <header>
    <h1>📸 Моя фотогалерея</h1>
    <p class="lead">Загружай фото — они сохраняются в твоём браузере. Можно открыть локально или выложить на GitHub Pages/Netlify.</p>
  </header>

  <div class="container">
    <div class="uploader" id="uploader">
      <div class="left">
        <label class="dropzone" id="dropzone" tabindex="0">
          Перетащи фото сюда или нажми, чтобы выбрать
        </label>
        <input id="fileInput" type="file" accept="image/*" multiple>
      </div>
      <div class="actions">
        <button id="clearAll" class="ghost">Очистить все</button>
        <button id="uploadBtn">Загрузить</button>
      </div>
    </div>

    <div class="controls" style="display:flex;justify-content:space-between;margin-top:12px;">
      <div style="color:var(--muted);font-size:13px;">
        Сохранено: <span id="count">0</span> фото
      </div>
      <div style="color:var(--muted);font-size:13px;">
        <button id="downloadAll" class="ghost" title="Скачать все (zip не поддержан)">Скачать все</button>
      </div>
    </div>

    <div class="gallery" id="gallery" aria-live="polite"></div>
  </div>

  <footer>Фото хранятся в localStorage — они доступны только на этом устройстве и в этом браузере.</footer>

  <!-- Modal для просмотра -->
  <div class="modal" id="modal">
    <div class="win" role="dialog" aria-modal="true">
      <img id="modalImg" alt="Preview">
      <div class="m-row">
        <button id="closeModal" class="small-btn">Закрыть</button>
        <a id="downloadImg" class="small-btn" download>Скачать</a>
      </div>
    </div>
  </div>

  <script>
    // Элементы
    const fileInput = document.getElementById('fileInput');
    const uploadBtn = document.getElementById('uploadBtn');
    const gallery = document.getElementById('gallery');
    const countSpan = document.getElementById('count');
    const clearAllBtn = document.getElementById('clearAll');
    const dropzone = document.getElementById('dropzone');
    const modal = document.getElementById('modal');
    const modalImg = document.getElementById('modalImg');
    const closeModal = document.getElementById('closeModal');
    const downloadImg = document.getElementById('downloadImg');
    const downloadAll = document.getElementById('downloadAll');

    // Загрузка из localStorage
    let photos = JSON.parse(localStorage.getItem('photos_v1')) || [];

    function updateCount(){ countSpan.textContent = photos.length }

    function savePhotos(){ localStorage.setItem('photos_v1', JSON.stringify(photos)); updateCount(); }

    // Инициализация
    (function init(){
      photos.forEach(p => addCard(p));
      updateCount();
    })();

    // Добавление фото (FileList или массив dataURL)
    function handleFiles(files){
      const fileArr = Array.from(files || []);
      if(fileArr.length === 0) return;
      let loaded = 0;
      fileArr.forEach(file => {
        if(!file.type.startsWith('image/')) { loaded++; if(loaded===fileArr.length) savePhotos(); return; }
        const reader = new FileReader();
        reader.onload = (e) => {
          const url = e.target.result;
          photos.push({id:Date.now()+Math.random(), url, name: file.name, size: file.size});
          addCard(photos[photos.length-1]);
          loaded++;
          if(loaded===fileArr.length) savePhotos();
        };
        reader.readAsDataURL(file);
      });
    }

    uploadBtn.addEventListener('click', () => {
      fileInput.click();
    });

    fileInput.addEventListener('change', (e) => {
      handleFiles(e.target.files);
      fileInput.value = '';
    });

    // Drag & Drop
    ['dragenter','dragover'].forEach(ev => {
      dropzone.addEventListener(ev, (e) => {
        e.preventDefault(); e.stopPropagation();
        dropzone.classList.add('drag');
      });
    });
    ['dragleave','drop'].forEach(ev => {
      dropzone.addEventListener(ev, (e) => {
        e.preventDefault(); e.stopPropagation();
        dropzone.classList.remove('drag');
      });
    });
    dropzone.addEventListener('drop', (e) => {
      const dt = e.dataTransfer;
      if(dt && dt.files) handleFiles(dt.files);
    });
    dropzone.addEventListener('click', ()=> fileInput.click());
    dropzone.addEventListener('keypress', (e)=> { if(e.key==='Enter') fileInput.click(); });

    // Добавить карточку в галерею
    function addCard(photo){
      const card = document.createElement('div');
      card.className = 'card';
      card.dataset.id = photo.id;

      const img = document.createElement('img');
      img.src = photo.url;
      img.alt = photo.name || 'Фото';

      const row = document.createElement('div');
      row.className = 'thumb-row';

      const meta = document.createElement('div');
      meta.className = 'meta';
      const date = new Date(photo.id).toLocaleString();
      meta.textContent = `${photo.name || 'Фото'} • ${date}`;

      const btns = document.createElement('div');
      btns.style.display = 'flex';
      btns.style.gap = '6px';

      const viewBtn = document.createElement('button');
      viewBtn.className = 'small-btn';
      viewBtn.title = 'Просмотреть';
      viewBtn.textContent = 'Открыть';
      viewBtn.addEventListener('click', () => openModal(photo));

      const dlBtn = document.createElement('a');
      dlBtn.className = 'small-btn';
      dlBtn.textContent = 'Скачать';
      dlBtn.href = photo.url;
      dlBtn.download = photo.name || 'photo.png';

      const delBtn = document.createElement('button');
      delBtn.className = 'small-btn danger';
      delBtn.textContent = 'Удалить';
      delBtn.addEventListener('click', () => {
        const idx = photos.findIndex(p => p.id === photo.id);
        if(idx !== -1){
          photos.splice(idx,1);
          savePhotos();
          card.remove();
        }
      });

      btns.appendChild(viewBtn);
      btns.appendChild(dlBtn);
      btns.appendChild(delBtn);

      row.appendChild(meta);
      row.appendChild(btns);

      card.appendChild(img);
      card.appendChild(row);
      gallery.prepend(card);
      updateCount();
    }

    // Очистить всё
    clearAllBtn.addEventListener('click', () => {
      if(!confirm('Удалить все сохранённые фото?')) return;
      photos = [];
      savePhotos();
      gallery.innerHTML = '';
      updateCount();
    });

    // Модал просмотр
    function openModal(photo){
      modalImg.src = photo.url;
      downloadImg.href = photo.url;
      downloadImg.download = photo.name || 'photo.png';
      modal.classList.add('show');
    }
    closeModal.addEventListener('click', ()=> modal.classList.remove('show'));
    modal.addEventListener('click', (e)=> { if(e.target === modal) modal.classList.remove('show'); });

    // Скачать все — простая реализация: открывает каждую картинку в новой вкладке (мобильно не идеально)
    downloadAll.addEventListener('click', () => {
      if(photos.length===0){ alert('Нет фото для скачивания'); return; }
      const proceed = confirm('Откроются все изображения в новых вкладках для скачивания. Продолжить?');
      if(!proceed) return;
      photos.forEach(p => {
        const a = document.createElement('a');
        a.href = p.url;
        a.download = p.name || 'photo.png';
        document.body.appendChild(a);
        a.click();
        a.remove();
      });
    });

    // Обновление счётчика при старте
    updateCount();
  </script>
</body>
</html>
