<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CRAXY V16 // NEURAL DUMP</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/js-beautify/1.14.7/beautify-html.min.js"></script>
    <style>
        :root { --main: #fff; --bg: #000; --accent: #ff0033; }
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Monaco', monospace; }
        body { background: var(--bg); color: var(--main); padding: 10px; display: flex; justify-content: center; }
        
        .box { width: 100%; max-width: 600px; border: 1px solid #222; border-radius: 8px; background: #080808; padding: 15px; }
        h1 { font-size: 14px; margin-bottom: 15px; letter-spacing: 2px; color: var(--accent); }

        input, textarea { 
            width: 100%; background: #000; border: 1px solid #222; color: #0f0; 
            padding: 12px; border-radius: 6px; outline: none; margin-bottom: 10px; font-size: 11px;
        }
        
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }
        button { 
            padding: 12px; border-radius: 6px; border: none; cursor: pointer;
            font-weight: bold; font-size: 10px; text-transform: uppercase;
        }
        .btn-p { background: var(--main); color: #000; width: 100%; margin-bottom: 10px; }
        .btn-s { background: #151515; color: #fff; border: 1px solid #222; }

        #loader { display: none; text-align: center; font-size: 9px; color: var(--accent); margin: 10px 0; }
    </style>
</head>
<body>

<div class="box">
    <h1>CRAXY V16 // NEURAL DUMP</h1>
    
    <input type="text" id="target" placeholder="URL TARGET (Wajib)">
    
    <button class="btn-p" onclick="grabData()">AUTO SNATCH & REPAIR</button>
    
    <div id="loader">EXTRACTING NEURAL DATA...</div>

    <textarea id="output" style="height: 300px;" placeholder="Jika auto gagal, paste view-source disini..."></textarea>

    <div class="grid">
        <button class="btn-s" onclick="deepClean()">FORCE DEEP CLEAN</button>
        <button class="btn-s" onclick="copy()">COPY HTML</button>
    </div>
    
    <button class="btn-s" style="width:100%; margin-top:8px; background: #ff0033; color: #fff;" onclick="download()">DOWNLOAD FIXED FILE</button>
</div>

<script>
    const out = document.getElementById('output');
    const load = document.getElementById('loader');

    async function grabData() {
        const url = document.getElementById('target').value;
        if(!url) return alert("URL KOSONG!");
        
        load.style.display = 'block';
        try {
            const res = await fetch(`https://api.codetabs.com/v1/proxy?quest=${url}`);
            const html = await res.text();
            if(html) {
                out.value = html;
                deepClean();
            }
        } catch(e) {
            alert("Sistem diblokir server korban. Gunakan VIEW-SOURCE manual!");
        }
        load.style.display = 'none';
    }

    function deepClean() {
        let code = out.value;
        const url = document.getElementById('target').value;
        if(!code) return;

        try {
            const origin = new URL(url.startsWith('http') ? url : 'https://' + url).origin;

            // 1. Nuke SEMUA Script & Event Handler (Penyebab Whitescreen)
            code = code.replace(/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi, "");
            code = code.replace(/on\w+="[^"]*"/g, ""); 
            
            // 2. Nuke Meta CSP & Frame Breakers
            code = code.replace(/<meta http-equiv="Content-Security-Policy"[^>]*>/gi, "");

            // 3. Inject BASE URL & CSS Fix
            const injection = `\n<base href="${origin}/">\n<style>html, body { display: block !important; visibility: visible !important; opacity: 1 !important; }</style>\n`;
            
            if(code.includes('<head>')) {
                code = code.replace('<head>', '<head>' + injection);
            } else {
                code = injection + code;
            }

            // 4. Force Beautify
            out.value = html_beautify(code, { 
                indent_size: 4, 
                max_preserve_newlines: 0,
                preserve_newlines: false 
            });
            
            document.getElementById('loader').innerText = "CLEAN SUCCESS";
        } catch(e) {
            alert("URL salah atau kode rusak!");
        }
    }

    function copy() {
        out.select();
        document.execCommand('copy');
        alert("Copied!");
    }

    function download() {
        const blob = new Blob([out.value], {type: 'text/html'});
        const a = document.createElement('a');
        a.href = URL.createObjectURL(blob);
        a.download = "cleansed_web.html";
        a.click();
    }
</script>

</body>
</html>
