<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Save Name & Instagram — Quick</title>
  <style>
    :root{--bg:#0f172a;--card:#0b1220;--accent:#06b6d4;--muted:#9aa5b1}
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,'Helvetica Neue',Arial;color:#e6eef6;background:linear-gradient(180deg,#071124 0%, #081428 60%);}
    .wrap{min-height:100%;display:flex;align-items:center;justify-content:center;padding:24px}
    .card{width:100%;max-width:560px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.008));border-radius:14px;padding:22px;box-shadow:0 8px 30px rgba(2,6,23,0.6);border:1px solid rgba(255,255,255,0.03)}
    h1{margin:0 0 8px;font-size:20px}
    p.lead{margin:0 0 18px;color:var(--muted)}
    label{display:block;font-size:13px;margin-top:12px;color:var(--muted)}
    input[type=text]{width:100%;padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit;font-size:15px}
    .btn{display:inline-block;padding:10px 14px;border-radius:10px;border:0;background:var(--accent);color:#042026;font-weight:600;cursor:pointer;margin-top:14px}
    .muted{color:var(--muted);font-size:13px;margin-top:10px}
    .saved-list{margin-top:14px;padding:10px;border-radius:8px;background:rgba(255,255,255,0.02);font-size:14px}
    footer{margin-top:14px;color:var(--muted);font-size:12px}
    .tap-hint{position:fixed;left:12px;bottom:12px;background:rgba(255,255,255,0.03);padding:10px 12px;border-radius:999px;font-size:13px}
  </style>
</head>
<body>
  <audio id="bgm" src="1080_1104__Aadavari Matalaku Arthale Verule Telugu HD Movie _ 2024 _ Venkatesh, Trisha.mp3" loop preload="auto" playsinline></audio>

  <div class="wrap">
    <div class="card">
      <h1>Save your Name & Instagram</h1>
      <p class="lead">Enter your name and Instagram id. The site will let you download the data to your computer (CSV). Tap anywhere on the screen once to start background music.</p>

      <form id="form">
        <label for="name">Full name</label>
        <input id="name" name="name" type="text" placeholder="e.g. Mourya" required />

        <label for="insta">Instagram username</label>
        <input id="insta" name="insta" type="text" placeholder="e.g. mourya123" required />

        <button type="submit" class="btn">Save to my computer</button>
      </form>

      <div class="muted">Notes: Make sure the mp3 file is in the same folder as this HTML file.</div>

      <div id="list" class="saved-list" aria-live="polite">No saved downloads yet.</div>
      <footer>Open this file in any modern browser. On mobile, the music will start after your first tap due to browser policies.</footer>
    </div>
  </div>

  <div class="tap-hint">Tap anywhere once to start music</div>

  <script>
    (function(){
      const bgm = document.getElementById('bgm');
      let musicStarted = false;

      // Play music only once after first user interaction, then keep looping
      document.addEventListener('click', async () => {
        if (!musicStarted) {
          try {
            await bgm.play();
            musicStarted = true;
            document.querySelector('.tap-hint').textContent = 'Music is playing...';
          } catch (err) {
            console.warn('Audio play failed:', err);
          }
        }
      }, {passive:true});

      // Save function creates a CSV and triggers download
      const form = document.getElementById('form');
      const list = document.getElementById('list');
      form.addEventListener('submit', (ev) => {
        ev.preventDefault();
        const name = document.getElementById('name').value.trim();
        const insta = document.getElementById('insta').value.trim();
        if (!name || !insta) return alert('Please fill both fields');

        const timestamp = new Date().toISOString();
        const csv = `Name,Instagram,Timestamp\n"${escapeCsv(name)}","${escapeCsv(insta)}","${timestamp}"\n`;
        const blob = new Blob([csv], {type: 'text/csv;charset=utf-8;'});
        const filename = `names_instagram_${(new Date()).toISOString().replace(/[:.]/g,'-')}.csv`;
        triggerDownload(blob, filename);

        addSavedEntry({name, insta, timestamp, filename});
        form.reset();
      });

      function escapeCsv(s){ return (''+s).replace(/"/g,'""'); }
      function escapeHtml(s){ return (''+s).replace(/[&<>"']/g, c=> ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":"&#39;"})[c]); }

      function triggerDownload(blob, filename) {
        const a = document.createElement('a');
        const url = URL.createObjectURL(blob);
        a.href = url; a.download = filename; document.body.appendChild(a); a.click();
        setTimeout(()=>{ URL.revokeObjectURL(url); a.remove(); }, 5000);
      }

      function addSavedEntry(obj) {
        list.innerHTML = `Saved: ${escapeHtml(obj.name)} — @${escapeHtml(obj.insta)} <span style=\"color:var(--muted);font-size:12px\">(${new Date(obj.timestamp).toLocaleString()})</span>`;
      }

    })();
  </script>
</body>
</html>
