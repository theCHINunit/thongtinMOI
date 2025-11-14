<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Numeric Password Gate — Drive Viewer</title>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--accent:#10b981;--muted:#94a3b8}
    html,body{height:100%;margin:0;font-family:Inter,ui-sans-serif,system-ui,Segoe UI,Roboto,'Helvetica Neue',Arial}
    body{
      display:grid;
      place-items:center;
      color:#e6eef8;
      background:url('Tosca Modern Creative Web Development Presentation.png') center/cover no-repeat fixed;
      backdrop-filter: blur(4px);
    }
    .card{
      width:360px;
      max-width:95%;
      background:rgba(15,23,36,0.85);
      padding:20px;
      border-radius:14px;
      box-shadow:0 8px 30px rgba(0,0,0,0.6);
    }
    h1{font-size:18px;margin:0 0 8px}
    p{margin:0 0 14px;color:var(--muted);font-size:13px}
    .display{height:48px;background:rgba(255,255,255,0.05);border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:20px;letter-spacing:6px}
    .keypad{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-top:12px}
    button.key{height:56px;border-radius:10px;border:0;background:#071226;color:#dff7ef;font-size:18px;cursor:pointer;box-shadow:inset 0 -3px 0 rgba(0,0,0,0.2)}
    .controls{display:flex;gap:10px;margin-top:10px}
    .controls button{flex:1;height:42px;border-radius:8px;border:0;background:#0b2a3a;color:#dff7ef;cursor:pointer}
    .hint{color:#9fb3c8;font-size:12px;margin-top:10px}
    .viewer{margin-top:16px;border-radius:8px;overflow:hidden;background:#071022}
    iframe{width:100%;height:480px;border:0}
    .success{padding:10px;border-radius:8px;background:linear-gradient(90deg,rgba(16,185,129,0.12),rgba(16,185,129,0.04));color:#9ef0c9;font-size:14px;margin-top:10px}
    .small{font-size:12px;color:var(--muted)}
    #hintImageWrap{margin-top:15px;padding:10px;background:#0b1220;border-radius:8px}
    #hintImage{max-width:100%;height:auto;border-radius:6px;display:block}
  </style>
</head>
<body>
  <div class="card">
    <h1>Nhập mật khẩu số</h1>
    <p>Nhập mã gồm số để mở file Drive.</p>

    <div class="display" id="display">●●●●</div>

    <div class="keypad" id="keypad">
      <button class="key">1</button>
      <button class="key">2</button>
      <button class="key">3</button>
      <button class="key">4</button>
      <button class="key">5</button>
      <button class="key">6</button>
      <button class="key">7</button>
      <button class="key">8</button>
      <button class="key">9</button>
      <button class="key">←</button>
      <button class="key">0</button>
      <button class="key">OK</button>
    </div>

    <div class="controls">
      <button id="clear">Xóa</button>
      <button id="showPlain">Hiện chữ</button>
      <button id="showHint">Gợi ý</button>
    </div>

    <div id="hintImageWrap" style="display:none;">
        <img id="hintImage" src="Dark Blue Night Sky Illustrative Quiz Time Game Instagram Post.jpg" alt="Gợi ý mật khẩu" />
    </div>

    <div class="hint small">Lưu ý: file Drive cần để chế độ “Ai có liên kết đều có thể xem”.</div>

    <div id="resultArea"></div>

    <div id="viewerWrap" class="viewer" style="display:none;">
      <iframe id="driveFrame" src="about:blank" allow="autoplay; encrypted-media" sandbox="allow-same-origin allow-scripts allow-popups allow-forms"></iframe>
    </div>
  </div>

  <script>
    const PASSWORD = '4078';
    const DRIVE_LINK = 'https://drive.google.com/drive/folders/13zhZA-1uW8o4NBNm2b_0Vp9cM87bkdpO?usp=drive_link';

    const display = document.getElementById('display');
    const keys = document.querySelectorAll('.key');
    const clearBtn = document.getElementById('clear');
    const showPlain = document.getElementById('showPlain');
    const showHintBtn = document.getElementById('showHint');
    const hintImageWrap = document.getElementById('hintImageWrap');
    const resultArea = document.getElementById('resultArea');
    const viewerWrap = document.getElementById('viewerWrap');
    const driveFrame = document.getElementById('driveFrame');

    let input = '';
    let showNumbers = false;
    let hintVisible = false;

    function updateDisplay(){
      if(input.length === 0){ display.textContent = '●●●●'; return }
      display.textContent = showNumbers ? input : '•'.repeat(input.length);
    }

    showHintBtn.addEventListener('click', ()=>{
        hintVisible = !hintVisible;
        if(hintVisible){
            hintImageWrap.style.display = 'block';
            showHintBtn.textContent = 'Ẩn gợi ý';
        } else {
            hintImageWrap.style.display = 'none';
            showHintBtn.textContent = 'Gợi ý';
        }
    });

    keys.forEach(k=>k.addEventListener('click', ()=>{
      const v = k.textContent.trim();
      if(v === 'OK') return checkPassword();
      if(v === '←'){ input = input.slice(0,-1); } 
      else { if(input.length < 12 && /[0-9]/.test(v)) input += v; }
      updateDisplay();
    }));

    clearBtn.addEventListener('click', ()=>{ input=''; updateDisplay(); resultArea.innerHTML=''; });
    showPlain.addEventListener('click', ()=>{ showNumbers = !showNumbers; showPlain.textContent = showNumbers ? 'Ẩn chữ' : 'Hiện chữ'; updateDisplay(); });

    function checkPassword(){
      if(input === PASSWORD){
        resultArea.innerHTML = '<div class="success">✅ Mật khẩu chính xác — đang mở thư mục...</div>';
        openDrive();
      } else {
        resultArea.innerHTML = '<div class="success" style="background:rgba(255,70,70,0.08);color:#ffc6c6">❌ Mật khẩu sai. Thử lại.</div>';
        input = ''; updateDisplay();
      }
    }

    function openDrive(){
      viewerWrap.style.display = 'none';
      const directLink = DRIVE_LINK;
      resultArea.innerHTML += `<div style="margin-top:8px;font-size:13px;color:#9fb3c8">Mở thư mục Drive trong tab mới: <a href="${directLink}" target="_blank" rel="noopener" style="color:inherit;text-decoration:underline">Mở thư mục</a></div>`;
      driveFrame.src = directLink;
      viewerWrap.style.display = 'block';
    }

    document.addEventListener('keydown', (e)=>{
      if(e.key >= '0' && e.key <= '9'){ if(input.length < 12) input += e.key; updateDisplay(); }
      if(e.key === 'Backspace'){ input = input.slice(0,-1); updateDisplay(); }
      if(e.key === 'Enter') checkPassword();
    });

    updateDisplay();
  </script>
</body>
</html>
