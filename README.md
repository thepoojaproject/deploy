<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Deploy - Windows 98 Edition</title>
  <style>
    * { margin:0; padding:0; box-sizing:border-box; }
    body {
      height:100vh;
      font-family:'MS Sans Serif',Tahoma,Arial,sans-serif;
      background:#008080;
      color:#000;
      overflow:hidden;
      user-select:none;
    }
    .window {
      position:absolute;
      top:5%; left:5%; width:90%; height:88%;
      min-width:520px; min-height:400px;
      background:#c0c0c0;
      border:2px solid #dfdfdf;
      border-left-color:#000; border-top-color:#000;
      box-shadow:3px 3px 0 #000;
      display:flex; flex-direction:column;
      resize:both; overflow:hidden;
    }
    .title-bar {
      height:28px;
      background:linear-gradient(to right,#000080,#1084d0);
      color:white;
      display:flex; align-items:center;
      padding:0 6px; cursor:default;
    }
    .title {
      flex:1; font-weight:bold; font-size:12px;
      padding-left:22px;
      background:url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16"><rect x="2" y="2" width="12" height="12" fill="white"/><path d="M4 4 L12 12 M12 4 L4 12" stroke="black" stroke-width="2"/></svg>') 4px center/16px no-repeat;
    }
    .window-btn {
      width:22px; height:20px; margin-left:2px;
      background:#c0c0c0; border:1px solid #fff #808080 #808080 #fff;
      font-size:11px; line-height:18px; text-align:center;
      cursor:pointer;
    }
    .window-btn:active { border-color:#808080 #fff #fff #808080; }

    .menu-bar {
      height:24px; background:#c0c0c0;
      border-bottom:1px solid #808080; display:flex;
      font-size:12px;
    }
    .menu-item {
      padding:4px 12px; cursor:default;
    }
    .menu-item:hover { background:#000080; color:white; }

    .toolbar {
      height:34px; background:#c0c0c0;
      border-bottom:2px solid #808080; padding:4px 8px;
      display:flex; align-items:center; gap:12px;
    }
    select, button {
      font-size:12px; padding:2px 8px;
      border:1px solid #000 #fff #fff #000;
      background:#fff; box-shadow:inset 1px 1px #808080;
    }
    button:active {
      border-color:#808080 #fff #fff #808080;
      box-shadow:inset 1px 1px #000;
    }

    .main { flex:1; display:flex; background:#000; border:2px inset #fff; overflow:hidden; }
    .editor-wrap { flex:1; display:flex; flex-direction:column; border-right:2px inset #fff; position:relative; }
    .editor-gutter {
      position:absolute; left:0; top:0; bottom:0; width:40px;
      background:#000080; color:#0a0; font-family:Consolas,monospace;
      font-size:13px; text-align:right; padding-right:6px;
      overflow:hidden; pointer-events:none;
    }
    textarea {
      flex:1; background:#000080; color:#0f8;
      font-family:Consolas,'Courier New',monospace; font-size:14px;
      padding:6px 8px 6px 48px; border:none; resize:none;
      outline:none; line-height:1.45; caret-color:#0f0;
    }
    .output-wrap { flex:1; display:flex; flex-direction:column; }
    .tab-label {
      height:24px; background:#c0c0c0; border-bottom:1px solid #808080;
      padding:4px 10px; font-size:12px; font-weight:bold;
    }
    #output {
      flex:1; background:#000; color:#0f0;
      font-family:Consolas,monospace; font-size:14px;
      padding:10px; overflow:auto; white-space:pre-wrap;
    }
    #html-output { flex:1; width:100%; border:none; display:none; }

    .status-bar {
      height:24px; 
      background:#c0c0c0; 
      border-top:1px solid #fff;
      padding:0 8px; 
      font-size:11px; 
      display:flex; 
      align-items:center; 
      justify-content: space-between;
      gap:16px;
      white-space: nowrap;
    }
    .status-left { flex-shrink: 0; }
    .status-right {
      font-style: italic;
      color: #444;
      opacity: 0.9;
    }
    .status-marquee { 
      flex:1; 
      overflow:hidden; 
      white-space:nowrap; 
      text-align: center;
    }
    .marquee-inner { 
      display:inline-block; 
      animation:marquee 20s linear infinite; 
    }
    @keyframes marquee { 
      0% { transform:translateX(100%); } 
      100% { transform:translateX(-100%); } 
    }

    /* Dropdown */
    .dropdown-menu {
      position: absolute; top: 52px; left: 8px;
      background: #c0c0c0; border: 2px outset #ffffff;
      box-shadow: 3px 3px 5px rgba(0,0,0,0.6);
      width: 190px; padding: 4px 0;
      font-size: 12px; z-index: 999; display: none;
    }
    .menu-entry {
      padding: 4px 20px; cursor: default;
    }
    .menu-entry:hover {
      background: #000080; color: white;
    }
    .separator {
      height: 1px; background: #808080; margin: 4px 8px;
      border-top: 1px solid #ffffff;
    }
  </style>
</head>
<body>

<div class="window" id="win">
  <div class="title-bar" id="titleBar">
    <div class="title">Deploy - Windows 98 Edition</div>
    <div class="window-btn" onclick="alert('Minimized! (not really)')">_</div>
    <div class="window-btn" onclick="alert('Maximized!')">□</div>
    <div class="window-btn" onclick="forceClose()">×</div>
  </div>

  <div class="menu-bar">
    <div class="menu-item" onclick="toggleFileMenu(event)">File</div>
    <div class="menu-item">Edit</div>
    <div class="menu-item">Run</div>
    <div class="menu-item">Help</div>
  </div>

  <div id="fileMenu" class="dropdown-menu">
    <div class="menu-entry" onclick="window.open('https://github.com/askbhimmondal-stack', '_blank', 'noopener,noreferrer')">
      My GitHub Profile
    </div>
    <div class="menu-entry" onclick="alert('New file coming soon!')">New</div>
    <div class="menu-entry" onclick="alert('Open not implemented yet')">Open...</div>
    <div class="separator"></div>
    <div class="menu-entry" onclick="forceClose()">Exit</div>
  </div>

  <div class="toolbar">
    <label>Language:</label>
    <select id="lang">
      <option value="javascript">JavaScript</option>
      <option value="python">Python (Pyodide)</option>
      <option value="html">HTML + CSS + JS</option>
    </select>
    <button onclick="runCode()">Run F5</button>
    <button onclick="clearOutput()">Clear</button>
  </div>

  <div class="main">
    <div class="editor-wrap">
      <div class="editor-gutter" id="gutter"></div>
      <textarea id="code" spellcheck="false">// Welcome! ♡
// Press Ctrl + Enter or F5 to run

console.log("Hello from Windows 98!");</textarea>
    </div>

    <div class="output-wrap">
      <div class="tab-label">Output</div>
      <pre id="output"></pre>
      <iframe id="html-output" sandbox="allow-scripts"></iframe>
    </div>
  </div>

  <div class="status-bar">
    <div class="status-left" id="status">Ready</div>
    
    <div class="status-marquee">
      <div class="marquee-inner">
        Pyodide • JavaScript • HTML • Ctrl+Enter or F5 to run • GitHub: askbhimmondal-stack
      </div>
    </div>
    
    <div class="status-right">
      Made in ❤ for Pooja
    </div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/pyodide/v0.26.2/full/pyodide.js"></script>

<script>
// Force close (works in most modern browsers when page is standalone)
function forceClose() {
  window.open('', '_self').close();
  window.close();
  // Fallback message if close is blocked
  document.body.innerHTML = '<h1 style="color:white;text-align:center;margin-top:40vh;font-family:Arial">Goodbye! ♡ Pooja</h1>';
}

// ────────────────────────────────────────────────
const win = document.getElementById('win');
const titleBar = document.getElementById('titleBar');
const codeArea = document.getElementById('code');
const gutter = document.getElementById('gutter');
const output = document.getElementById('output');
const htmlFrame = document.getElementById('html-output');
const status = document.getElementById('status');
const langSel = document.getElementById('lang');
const fileMenu = document.getElementById('fileMenu');

let pyodide, pyReady = false;

// Draggable window
let isDragging = false, startX, startY, initLeft, initTop;
titleBar.onmousedown = e => {
  if (e.target.classList.contains('window-btn')) return;
  isDragging = true; startX = e.clientX; startY = e.clientY;
  initLeft = win.offsetLeft; initTop = win.offsetTop;
  titleBar.style.cursor = 'move';
};
document.onmousemove = e => {
  if (!isDragging) return;
  win.style.left = (initLeft + e.clientX - startX) + 'px';
  win.style.top  = (initTop + e.clientY - startY) + 'px';
};
document.onmouseup = () => { isDragging = false; titleBar.style.cursor = 'default'; };

// Line numbers
function updateGutter() {
  const lines = (codeArea.value.match(/\n/g) || []).length + 1;
  gutter.innerHTML = Array.from({length:lines}, (_,i) => i+1).join('\n');
}
codeArea.addEventListener('input', updateGutter);
codeArea.addEventListener('scroll', () => gutter.scrollTop = codeArea.scrollTop);
updateGutter();

// Language persistence
if (localStorage.getItem('lastLang')) langSel.value = localStorage.getItem('lastLang');

// Python init
async function initPyodide() {
  status.textContent = 'Loading Python...';
  pyodide = await loadPyodide();
  pyReady = true;
  status.textContent = 'Ready';
}
window.addEventListener('load', initPyodide);

// Run code
async function runCode() {
  const lang = langSel.value;
  localStorage.setItem('lastLang', lang);
  const code = codeArea.value.trim();

  output.textContent = '';
  htmlFrame.style.display = 'none';
  output.style.display = 'block';
  output.style.color = '#0f0';
  status.textContent = 'Running...';

  try {
    if (lang === 'javascript') {
      const logs = [];
      const fakeConsole = { log: (...a) => logs.push(a.map(x=>String(x)).join(' ')) };
      new Function('console', code)(fakeConsole);
      output.textContent = logs.join('\n') || '(no output)';
    }
    else if (lang === 'python') {
      if (!pyReady) throw new Error("Python still loading...");
      let out = "";
      pyodide.setStdout({batched: s => out += s});
      pyodide.setStderr({batched: s => out += s});
      await pyodide.runPythonAsync(code);
      output.textContent = out.trim() || '(no output)';
    }
    else if (lang === 'html') {
      output.style.display = 'none';
      htmlFrame.style.display = 'block';
      htmlFrame.srcdoc = code;
    }
    status.textContent = 'Finished • ' + new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
  } catch (e) {
    output.style.color = '#f44';
    output.textContent = e.message + '\n' + (e.stack||'').split('\n').slice(0,6).join('\n');
    status.textContent = 'Error';
  }
}

function clearOutput() {
  output.textContent = '';
  htmlFrame.srcdoc = '';
  htmlFrame.style.display = 'none';
  output.style.display = 'block';
  status.textContent = 'Cleared';
}

// Dropdown
function toggleFileMenu(e) {
  e.stopPropagation();
  const isOpen = fileMenu.style.display === 'block';
  fileMenu.style.display = isOpen ? 'none' : 'block';
  if (!isOpen) {
    setTimeout(() => {
      document.addEventListener('click', function handler(ev) {
        if (!fileMenu.contains(ev.target)) {
          fileMenu.style.display = 'none';
          document.removeEventListener('click', handler);
        }
      }, { once: true });
    }, 10);
  }
}

// Shortcuts
codeArea.addEventListener('keydown', e => {
  if ((e.ctrlKey && e.key === 'Enter') || e.key === 'F5') {
    e.preventDefault();
    runCode();
  }
});
</script>
</body>
</html>
