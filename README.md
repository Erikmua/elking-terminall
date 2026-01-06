<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Terminal by Elking</title>
    
    <meta name="theme-color" content="#00bfff">
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <link rel="manifest" href="data:application/json;base64,ewogICJpZCI6ICJlbGtpbmctdGVybWluYWwiLAogICJuYW1lIjogIlRlcm1pbmFsIGJ5IEVsa2luZyIsCiAgInNob3J0X25hbWUiOiAiRWxraW5nVGVybSIsCiAgInN0YXJ0X3VybCI6ICIuIiwKICAiZGlzcGxheSI6ICJzdGFuZGFsb25lIiwKICAiYmFja2dyb3VuZF9jb2xvciI6ICIjMDAwMDAwIiwKICAidGhlbWVfY29sb3IiOiAiIzAwYmZmZiIsCiAgImljb25zIjogWwogICAgewogICAgICAic3JjIjogImh0dHBzOi8vY2RuLWljb25zLXBuZy5mbGF0aWNvbi5jb20vNTEyLzI2MjEvMjYyMTEyOC5wbmciLAogICAgICAic2l6ZXMiOiAiNTEyeDUxMiIsCiAgICAgICJ0eXBlIjogImltYWdlL3BuZyIKICAgIH0KICBdCn0=">

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Fira+Code:wght@400;700&display=swap');

        :root {
            --bg-color: #000;
            --term-color: #00ff41;
            --prompt-color: #00bfff;
            --font-size: 14px;
        }

        body {
            margin: 0;
            background: var(--bg-color) url('https://images.unsplash.com/photo-1462331940025-496dfbfc7564?auto=format&fit=crop&w=1500') center/cover fixed;
            color: var(--term-color);
            font-family: 'Fira Code', monospace;
            height: 100vh;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        #terminal-wrapper {
            flex: 1;
            background: rgba(0, 0, 0, 0.85);
            padding: 15px;
            overflow-y: auto;
            font-size: var(--font-size);
        }

        .ascii-art {
            color: #00bfff;
            white-space: pre;
            font-size: 8px; /* Adattato per mobile */
            line-height: 1.2;
            margin-bottom: 20px;
        }

        .prompt-line { display: flex; flex-direction: column; margin-top: 10px; }
        .prompt-head { color: var(--prompt-color); font-weight: bold; }
        
        input {
            background: transparent;
            border: none;
            color: var(--term-color);
            font-family: 'Fira Code', monospace;
            font-size: var(--font-size);
            outline: none;
            width: 100%;
        }

        #extra-keys {
            background: #1a1a1a;
            display: flex;
            gap: 5px;
            padding: 10px;
            border-top: 1px solid #333;
        }

        .btn {
            background: #333;
            color: white;
            border: none;
            padding: 10px;
            flex: 1;
            border-radius: 4px;
            font-weight: bold;
        }

        .success { color: #0f0; }
        .warn { color: #ff0; }
        .err { color: #f00; }
    </style>
</head>
<body>

    <input type="file" id="bg-input" style="display:none" accept="image/*">

    <div id="terminal-wrapper" onclick="document.getElementById('cmd').focus()">
        <div id="output">
<pre class="ascii-art">
███████╗██╗     ██╗  ██╗██╗███╗   ██╗ ██████╗ 
██╔════╝██║     ██║ ██╔╝██║████╗  ██║██╔════╝ 
█████╗  ██║     █████╔╝ ██║██╔██╗ ██║██║  ███╗
██╔══╝  ██║     ██╔═██╗ ██║██║╚██╗██║██║   ██║
███████╗███████╗██║  ██╗██║██║ ╚████║╚██████╔╝
╚══════╝╚══════╝╚═╝  ╚═╝╚═╝╚═╝  ╚═══╝ ╚═════╝ 
</pre>
            <div class="success">SYSTEM ONLINE - TERMINAL BY ELKING</div>
            <div>Digita 'help' per i comandi.</div>
        </div>
        
        <div class="prompt-line">
            <span class="prompt-head">┌──(elking㉿space)</span>
            <div style="display:flex">
                <span class="prompt-head">└─$ &nbsp;</span>
                <input type="text" id="cmd" autofocus spellcheck="false">
            </div>
        </div>
    </div>

    <div id="extra-keys">
        <button class="btn" onclick="changeZoom(1)">ZOOM +</button>
        <button class="btn" onclick="changeZoom(-1)">ZOOM -</button>
        <button class="btn" onclick="runCmd('termux-setup-storage')">STORAGE</button>
        <button class="btn" onclick="runCmd('clear')">CLEAR</button>
    </div>

    <script>
        const output = document.getElementById('output');
        const cmdInput = document.getElementById('cmd');
        let fontSize = 14;
        let hasStorage = false;

        cmdInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                const val = cmdInput.value.trim();
                printHTML(`<div style="color:#00bfff; margin-top:10px">└─$ ${val}</div>`);
                runCmd(val);
                cmdInput.value = '';
                document.getElementById('terminal-wrapper').scrollTop = 999999;
            }
        });

        function runCmd(cmd) {
            const c = cmd.toLowerCase();
            if (c === 'help') {
                printHTML("Comandi: <span class='warn'>clear, neofetch, pkg install, chbg, termux-setup-storage</span>");
            } else if (c === 'clear') {
                output.innerHTML = '';
            } else if (c === 'termux-setup-storage') {
                if(confirm("Permettere l'accesso alla galleria?")) {
                    hasStorage = true;
                    printHTML("<span class='success'>Accesso alla memoria configurato!</span>");
                }
            } else if (c === 'chbg') {
                if(!hasStorage) printHTML("<span class='err'>Errore: lancia prima 'termux-setup-storage'</span>");
                else document.getElementById('bg-input').click();
            } else if (c === 'neofetch') {
                printHTML("<span style='color:cyan'>OS: Elking Space PWA<br>Kernel: Web-App-1.0<br>Theme: Deep Space</span>");
            } else if (c.startsWith('pkg install')) {
                printHTML("<div class='success'>Installazione in corso... [##########] 100%</div>");
            } else {
                printHTML(`<span class='err'>Comando '${c}' non trovato.</span>`);
            }
        }

        function printHTML(html) {
            output.innerHTML += `<div>${html}</div>`;
        }

        function changeZoom(dir) {
            fontSize += dir * 2;
            document.documentElement.style.setProperty('--font-size', fontSize + 'px');
        }

        document.getElementById('bg-input').onchange = (e) => {
            const reader = new FileReader();
            reader.onload = (ev) => document.body.style.backgroundImage = `url(${ev.target.result})`;
            reader.readAsDataURL(e.target.files[0]);
        };

        // Service Worker integrato per installazione offline
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('data:application/javascript;base64,c2VsZi5hZGRFdmVudExpc3RlbmVyKCdmZXRjaCcsIGV2ID0+IGV2LnJlc3BvbmRXaXRoKGZldGNoKGV2LnJlcXVlc3QpKSk7');
        }
    </script>
</body>
</html>
