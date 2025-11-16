<!doctype html>
<html lang="id">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Animasi Grafik Fungsi Trigonometri</title>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--accent:#7c3aed;--muted:#98a0b3;--glass:rgba(255,255,255,0.03)}
    *{box-sizing:border-box}
    body{margin:0;font-family:Inter,ui-sans-serif,system-ui,Segoe UI,Roboto,Helvetica,Arial;'Segoe UI',sans-serif;background:linear-gradient(180deg,#071021 0%, #072034 50%, #0b1220 100%);color:#e6eef8;padding:24px}
    .wrap{max-width:1100px;margin:0 auto}
    header{display:flex;align-items:center;gap:16px;margin-bottom:18px}
    h1{font-size:20px;margin:0}
    .layout{display:grid;grid-template-columns:420px 1fr;gap:18px}
    .card{background:var(--card);border-radius:12px;padding:14px;box-shadow:0 6px 24px rgba(2,6,23,.6)}
    canvas{display:block;background:var(--glass);width:100%;border-radius:8px}
    label{display:block;color:var(--muted);font-size:13px;margin-bottom:6px}
    .row{display:flex;gap:8px;align-items:center}
    .controls{margin-top:12px;display:flex;flex-direction:column;gap:10px}
    input[type=range]{width:100%}
    select,input[type=number]{background:#071523;border:1px solid rgba(255,255,255,0.04);color:#e6eef8;padding:6px;border-radius:6px}
    .btn{background:linear-gradient(90deg,var(--accent),#3b82f6);border:none;padding:8px 12px;border-radius:8px;color:white;cursor:pointer}
    .small{font-size:13px;color:var(--muted)}
    .legend{display:flex;gap:8px;align-items:center;margin-top:8px}
    .dot{width:12px;height:12px;border-radius:50%}
    .muted{color:var(--muted)}
    footer{margin-top:14px;color:var(--muted);font-size:13px}
    @media (max-width:980px){.layout{grid-template-columns:1fr;}}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <h1>Animasi Grafik Fungsi Trigonometri (sin / cos / tan)</h1>
      <div class="small">Interaktif — ubah amplitudo, frekuensi, fase, & pilih fungsi</div>
    </header>

    <div class="layout">
      <!-- Left: Visualisasi lingkaran + controls -->
      <div class="card">
        <div style="display:flex;gap:10px;align-items:center;">
          <div style="flex:1">
            <label class="small">Visualisasi Lingkaran Satuan (untuk sin/cos)</label>
            <canvas id="circle" width="380" height="380"></canvas>
          </div>
          <div style="width:120px;">
            <label class="small">Sudut (°)</label>
            <input id="angle" type="range" min="0" max="360" value="0">
            <div class="small" style="margin-top:8px;text-align:center">θ = <span id="angleVal">0</span>°</div>
            <div style="height:8px"></div>
            <button id="play" class="btn" style="width:100%">Mulai</button>
            <div style="height:8px"></div>
            <button id="reset" class="btn" style="background:#334155;width:100%">Reset</button>
          </div>
        </div>

        <div style="margin-top:10px">
          <div class="small">Nilai</div>
          <div style="display:flex;gap:12px;margin-top:6px">
            <div class="card" style="padding:8px;flex:1;background:#071827">
              <div class="small">f(θ)</div>
              <div id="value" style="font-weight:700;font-size:18px">0.000</div>
            </div>
            <div class="card" style="padding:8px;flex:1;background:#071827">
              <div class="small">cos(θ)</div>
              <div id="cosv" style="font-weight:700;font-size:18px">1.000</div>
            </div>
          </div>
        </div>

      </div>

      <!-- Right: Graph -->
      <div class="card">
        <label class="small">Grafik Fungsi</label>
        <canvas id="graph" width="700" height="380"></canvas>

        <div class="controls">
          <div style="display:flex;gap:8px;align-items:center">
            <label style="min-width:80px">Fungsi</label>
            <select id="func">
              <option value="sin">sin(θ)</option>
              <option value="cos">cos(θ)</option>
              <option value="tan">tan(θ)</option>
            </select>
          </div>

          <div style="display:flex;gap:8px">
            <div style="flex:1">
              <label class="small">Amplitudo</label>
              <input id="amp" type="range" min="0.1" max="3" step="0.1" value="1">
            </div>
            <div style="width:110px">
              <label class="small">Frekuensi</label>
              <input id="freq" type="number" step="0.1" value="1" style="width:100%">
            </div>
            <div style="width:110px">
              <label class="small">Fase (°)</label>
              <input id="phase" type="number" step="1" value="0" style="width:100%">
            </div>
          </div>

          <div style="display:flex;gap:8px;align-items:center">
            <div style="flex:1">
              <label class="small">Kecepatan Animasi</label>
              <input id="speed" type="range" min="0" max="20" value="6">
            </div>
            <div style="width:120px">
              <label class="small">Jangkauan (°)</label>
              <input id="range" type="number" value="360" style="width:100%">
            </div>
          </div>

          <div style="display:flex;gap:8px;align-items:center;margin-top:6px">
            <button id="clear" class="btn" style="background:#334155">Bersihkan</button>
            <div class="legend" style="margin-left:6px">
              <div class="dot" style="background:var(--accent)"></div><div class="small">Kurva</div>
              <div class="dot" style="background:#ef4444"></div><div class="small">Titik sekarang</div>
            </div>
          </div>
        </div>

        <footer>Tekan &quot;Mulai&quot; untuk animasi — gunakan slider untuk mengubah parameter.</footer>
      </div>
    </div>
  </div>

  <script>
    // Canvas & contexts
    const circle = document.getElementById('circle'); const cctx = circle.getContext('2d');
    const graph = document.getElementById('graph'); const gctx = graph.getContext('2d');

    // Controls
    const angleRange = document.getElementById('angle'); const angleVal = document.getElementById('angleVal');
    const playBtn = document.getElementById('play'); const resetBtn = document.getElementById('reset');
    const funcSel = document.getElementById('func'); const amp = document.getElementById('amp');
    const freq = document.getElementById('freq'); const phase = document.getElementById('phase');
    const speed = document.getElementById('speed'); const rangeInput = document.getElementById('range');
    const clearBtn = document.getElementById('clear'); const valueBox = document.getElementById('value'); const cosBox = document.getElementById('cosv');

    // State
    let theta = 0; let playing = false; let raf = null; let plotted = [];

    // Sizes
    const CX = circle.width/2, CY = circle.height/2, R = Math.min(circle.width, circle.height)/2 - 30;
    const GLEFT = 40, GRIGHT = graph.width-20, GTOP = 20, GBOTTOM = graph.height-30, GWIDTH = GRIGHT - GLEFT;

    function clearCircle(){ cctx.clearRect(0,0,circle.width,circle.height); }
    function clearGraph(){ gctx.clearRect(0,0,graph.width,graph.height); }

    function drawCircle(){
      clearCircle();
      // grid
      cctx.strokeStyle = 'rgba(255,255,255,0.03)';
      for(let i=0;i<circle.width;i+=20){ cctx.beginPath(); cctx.moveTo(i,0); cctx.lineTo(i,circle.height); cctx.stroke(); }
      for(let j=0;j<circle.height;j+=20){ cctx.beginPath(); cctx.moveTo(0,j); cctx.lineTo(circle.width,j); cctx.stroke(); }

      // circle
      cctx.beginPath(); cctx.arc(CX,CY,R,0,Math.PI*2); cctx.strokeStyle='rgba(124,58,237,0.9)'; cctx.lineWidth=2; cctx.stroke();
      // axes
      cctx.beginPath(); cctx.moveTo(CX-R-10,CY); cctx.lineTo(CX+R+10,CY); cctx.moveTo(CX,CY-R-10); cctx.lineTo(CX,CY+R+10); cctx.strokeStyle='rgba(255,255,255,0.06)'; cctx.lineWidth=1; cctx.stroke();

      // current point
      const rad = degToRad(theta+parseFloat(phase.value||0));
      const x = CX + R*Math.cos(rad); const y = CY - R*Math.sin(rad);
      cctx.beginPath(); cctx.moveTo(CX,CY); cctx.lineTo(x,y); cctx.strokeStyle='rgba(239,68,68,0.9)'; cctx.lineWidth=2; cctx.stroke();

      cctx.beginPath(); cctx.arc(x,y,7,0,Math.PI*2); cctx.fillStyle='rgba(239,68,68,0.95)'; cctx.fill();
      // projection
      cctx.setLineDash([6,4]); cctx.beginPath(); cctx.moveTo(x,y); cctx.lineTo(x,CY); cctx.strokeStyle='rgba(59,130,246,0.8)'; cctx.stroke(); cctx.setLineDash([]);

      // labels
      cctx.fillStyle='rgba(255,255,255,0.7)'; cctx.font='13px sans-serif'; cctx.fillText('O',CX-10,CY+18); cctx.fillText('1',CX+R-6,CY+18); cctx.fillText('-1',CX-R-18,CY+18);
    }

    function degToRad(d){ return d*Math.PI/180; }

    function computeFunc(d){ const a=parseFloat(amp.value), f=parseFloat(freq.value), ph=parseFloat(phase.value);
      const arg = degToRad(d*f + ph);
      const fn = funcSel.value;
      if(fn==='sin') return a*Math.sin(arg);
      if(fn==='cos') return a*Math.cos(arg);
      if(fn==='tan') return a*Math.tan(arg);
      return 0;
    }

    function drawGraphAxes(){
      clearGraph();
      // background grid
      gctx.fillStyle='rgba(255,255,255,0.01)'; gctx.fillRect(0,0,graph.width,graph.height);
      gctx.strokeStyle='rgba(255,255,255,0.04)';
      for(let x=GLEFT;x<=GRIGHT;x+=20){ gctx.beginPath(); gctx.moveTo(x,0); gctx.lineTo(x,graph.height); gctx.stroke(); }
      for(let y=0;y<=graph.height;y+=20){ gctx.beginPath(); gctx.moveTo(0,y); gctx.lineTo(graph.width,y); gctx.stroke(); }

      // axes
      gctx.beginPath(); gctx.moveTo(GLEFT,graph.height/2); gctx.lineTo(GRIGHT,graph.height/2); gctx.moveTo(GLEFT,GTOP); gctx.lineTo(GLEFT,GBOTTOM); gctx.strokeStyle='rgba(255,255,255,0.2)'; gctx.lineWidth=1; gctx.stroke();
      // labels
      gctx.fillStyle='rgba(255,255,255,0.7)'; gctx.font='13px sans-serif'; gctx.fillText('θ (°)',GRIGHT-30,graph.height/2+18); gctx.fillText('f(θ)',10,GTOP+10);

      // ticks
      const maxRange = parseFloat(rangeInput.value||360);
      for(let t=0;t<=maxRange;t+=45){ const x = GLEFT + (t/maxRange)*GWIDTH; gctx.beginPath(); gctx.moveTo(x,graph.height/2-6); gctx.lineTo(x,graph.height/2+6); gctx.stroke(); gctx.fillText(t.toString(),x-10,graph.height/2+22); }
    }

    function redrawFull(upTo=null){
      drawGraphAxes();
      const maxRange = parseFloat(rangeInput.value||360);
      const step = 1;
      gctx.beginPath();
      let started=false;
      for(let t=0;t<= (upTo===null?maxRange:upTo); t+=step){
        const yv = computeFunc(t);
        // clamp huge values for tan
        const clampY = Math.max(Math.min(yv,10),-10);
        const x = GLEFT + (t/maxRange)*GWIDTH;
        const y = graph.height/2 - clampY*( (graph.height/2 - 30) / 3 );
        if(!started){ gctx.moveTo(x,y); started=true; } else { gctx.lineTo(x,y); }
      }
      gctx.strokeStyle='rgba(124,58,237,0.95)'; gctx.lineWidth=2; gctx.stroke();

      // draw current point
      const curX = GLEFT + (theta/parseFloat(rangeInput.value||360))*GWIDTH;
      const curYVal = computeFunc(theta);
      const curY = graph.height/2 - Math.max(Math.min(curYVal,10),-10)*((graph.height/2 - 30)/3);
      gctx.beginPath(); gctx.arc(curX,curY,6,0,Math.PI*2); gctx.fillStyle='rgba(239,68,68,0.95)'; gctx.fill();

      // dashed vertical helper
      gctx.setLineDash([6,4]); gctx.beginPath(); gctx.moveTo(curX,curY); gctx.lineTo(curX,graph.height/2); gctx.strokeStyle='rgba(59,130,246,0.8)'; gctx.stroke(); gctx.setLineDash([]);
    }

    function step(){
      const sp = parseFloat(speed.value);
      theta += sp/10; if(theta>parseFloat(rangeInput.value)) theta = 0;
      angleRange.value = Math.round(theta);
      angleVal.textContent = Math.round(theta);
      const fval = computeFunc(theta);
      valueBox.textContent = (isFinite(fval)?fval.toFixed(3):'∞');
      cosBox.textContent = Math.cos(degToRad(theta)).toFixed(3);
      drawCircle(); redrawFull(theta);
      if(playing) raf = requestAnimationFrame(step);
    }

    // Event wiring
    angleRange.addEventListener('input',()=>{ theta = parseFloat(angleRange.value); angleVal.textContent = Math.round(theta); const fval = computeFunc(theta); valueBox.textContent = (isFinite(fval)?fval.toFixed(3):'∞'); drawCircle(); redrawFull(theta); });
    funcSel.addEventListener('change',()=>{ redrawFull(theta); drawCircle(); }); amp.addEventListener('input',()=>{ redrawFull(theta); }); freq.addEventListener('input',()=>{ redrawFull(theta); }); phase.addEventListener('input',()=>{ redrawFull(theta); drawCircle(); }); rangeInput.addEventListener('input',()=>{ redrawFull(theta); }); clearBtn.addEventListener('click',()=>{ redrawFull(0); angleRange.value = 0; theta=0; angleVal.textContent='0'; valueBox.textContent='0.000'; cosBox.textContent='1.000'; });

    playBtn.addEventListener('click',()=>{
      playing = !playing; if(playing){ playBtn.textContent='Jeda'; raf = requestAnimationFrame(step); } else { playBtn.textContent='Mulai'; cancelAnimationFrame(raf); }
    });
    resetBtn.addEventListener('click',()=>{ playing=false; cancelAnimationFrame(raf); playBtn.textContent='Mulai'; theta=0; angleRange.value=0; angleVal.textContent='0'; valueBox.textContent='0.000'; cosBox.textContent='1.000'; redrawFull(0); drawCircle(); });

    // Init draw
    drawCircle(); redrawFull(0);
  </script>
</body>
</html>
