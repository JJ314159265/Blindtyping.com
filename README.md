<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Blind Typing Trainer - iPhone Optimized</title>
    <style>
        :root {
            --bg-color: #f4f7f6;
            --card-bg: #ffffff;
            --text-color: #333333;
            --primary: #007aff;
            --primary-hover: #005ec4;
            --border-color: #d1d1d6;
            --error-bg: #ffdddd;
            --error-text: #d8000c;
            --success-bg: #dff2bf;
            --success-text: #4f8a10;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 10px;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            box-sizing: border-box;
        }

        h1 {
            font-size: 1.5rem;
            margin: 10px 0;
            text-align: center;
        }

        h2, h3 {
            font-size: 1.2rem;
            margin: 8px 0;
        }

        .container {
            background: var(--card-bg);
            padding: 15px;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
            width: 100%;
            max-width: 500px; /* Optimized for iPhone max width */
            display: flex;
            flex-direction: column;
            gap: 15px;
            box-sizing: border-box;
        }

        .setup-screen, .app-screen {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .hidden {
            display: none !important;
        }

        label {
            font-weight: 600;
            font-size: 0.9rem;
        }

        select, textarea, button, input {
            font-size: 1rem;
            padding: 12px;
            border-radius: 8px;
            border: 1px solid var(--border-color);
            box-sizing: border-box;
            background-color: #fff;
            -webkit-appearance: none;
        }

        textarea {
            resize: none;
        }

        button {
            background-color: var(--primary);
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.2s;
            min-height: 44px; /* Apple minimum touch target size */
        }

        button:active {
            background-color: var(--primary-hover);
        }

        .secondary-btn {
            background-color: #8e8e93;
        }
        .secondary-btn:active {
            background-color: #636366;
        }

        .row-group {
            display: flex;
            gap: 10px;
        }

        .row-group > div {
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 4px;
        }

        /* Dynamic Layouts for Mode 1 */
        .workspace {
            display: flex;
            gap: 10px;
            flex: 1;
        }

        .workspace.layout-top { flex-direction: column; }
        .workspace.layout-bottom { flex-direction: column-reverse; }
        .workspace.layout-left { flex-direction: column; } /* Fallback to vertical stack on mobile screens for usability */
        .workspace.layout-right { flex-direction: column; } /* Fallback to vertical stack on mobile screens for usability */

        .target-box, .typing-box-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        /* Interactive Target Text Box */
        .target-display {
            background: #eef2f5;
            padding: 12px;
            border-radius: 8px;
            border: 1px solid var(--border-color);
            overflow-y: auto;
            white-space: pre-wrap;
            font-family: monospace;
            cursor: pointer;
            user-select: none;
            -webkit-user-select: none;
        }

        .target-char {
            display: inline-block;
            padding: 2px 0;
        }
        .target-char:active {
            background-color: #d0e1f9;
        }

        /* Completely blank text area style optimized for mobile keyboards */
        .blind-textarea {
            font-family: monospace;
            padding: 12px;
            box-sizing: border-box;
            color: transparent !important;
            background-color: #ffffff;
            caret-color: transparent; 
            outline: none;
            border: 1px solid var(--border-color);
            border-radius: 8px;
        }
        .blind-textarea::selection {
            background: transparent;
        }

        .controls-row {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        /* Results Screen */
        .results-box {
            background: #fafafa;
            padding: 12px;
            border-radius: 8px;
            border: 1px solid var(--border-color);
            font-family: monospace;
            white-space: pre-wrap;
            line-height: 1.5;
            word-break: break-all;
            font-size: 0.95rem;
            max-height: 250px;
            overflow-y: auto;
        }

        .diff-correct {
            background-color: var(--success-bg);
            color: var(--success-text);
        }

        .diff-error {
            background-color: var(--error-bg);
            color: var(--error-text);
            text-decoration: underline;
        }

        .diff-missing {
            background-color: #ffffe0;
            color: #b8860b;
        }

        /* Highlight for the single red pause asterisk */
        .pause-marker {
            background-color: #ff3b30;
            color: white;
            font-weight: bold;
            padding: 0 6px;
            border-radius: 4px;
            border: 1.5px solid #c92a20;
            display: inline-block;
            margin: 0 2px;
        }
    </style>
</head>
<body>

    <h1>Blind Typing Trainer</h1>

    <div class="container">
        <!-- SETUP SCREEN -->
        <div id="setupScreen" class="setup-screen">
            <h2>Configuration</h2>
            <div>
                <label for="modeSelect">Select Mode:</label>
                <select id="modeSelect" onchange="toggleModeOptions()">
                    <option value="1">Mode 1: Replicate Preloaded Text</option>
                    <option value="2">Mode 2: Free Typing Session</option>
                </select>
            </div>

            <div id="mode1Options">
                <div>
                    <label for="positionSelect">Target Text Position:</label>
                    <select id="positionSelect">
                        <option value="top">Top</option>
                        <option value="bottom">Bottom</option>
                    </select>
                </div>
                <div style="margin-top: 8px;">
                    <label for="customText">Target Text (Preloaded):</label>
                    <textarea id="customText" style="height: 70px;">The quick brown fox jumps over the lazy dog. Practice makes perfect.</textarea>
                </div>
                <div style="margin-top: 8px;">
                    <label for="targetFontSize">Target Font Size:</label>
                    <select id="targetFontSize">
                        <option value="0.9rem">Small</option>
                        <option value="1.05rem" selected>Medium</option>
                        <option value="1.25rem">Large</option>
                    </select>
                </div>
            </div>

            <!-- Dimensions Configuration -->
            <hr style="border: 0; border-top: 1px solid var(--border-color); margin: 5px 0;">
            <h3>Box Heights</h3>
            <div class="row-group">
                <div>
                    <label for="targetBoxHeight">Target Box Height:</label>
                    <input type="text" id="targetBoxHeight" value="120px">
                </div>
                <div>
                    <label for="typingBoxHeight">Typing Box Height:</label>
                    <input type="text" id="typingBoxHeight" value="120px">
                </div>
            </div>

            <button onclick="startTest()" style="margin-top: 5px;">Start Typing Test</button>
        </div>

        <!-- APP SCREEN -->
        <div id="appScreen" class="app-screen hidden">
            <div id="workspace" class="workspace layout-top">
                <div id="targetWrapper" class="target-box">
                    <label id="targetLabelTitle" style="font-size:0.8rem;">Tap character to place/remove red asterisk marker:</label>
                    <div id="targetDisplay" class="target-display"></div>
                </div>
                <div class="typing-box-container">
                    <label for="blindInput" style="font-size:0.8rem;">Type blindly below (completely blank area):</label>
                    <textarea id="blindInput" class="blind-textarea" placeholder="Type here blindly..." autocapitalize="off" autocorrect="off" autocomplete="off" spellcheck="false"></textarea>
                </div>
            </div>
            <div class="controls-row">
                <button type="button" id="clearMarkerBtn" class="secondary-btn hidden" onclick="clearMarker()">Remove Asterisk Marker</button>
                <button id="actionBtn" onclick="finishTest()">Done / End</button>
            </div>
        </div>

        <!-- RESULTS SCREEN -->
        <div id="resultsScreen" class="setup-screen hidden">
            <h2>Session Results</h2>
            <div id="resultsContent" class="results-box"></div>
            <button onclick="resetApp()">Try Again</button>
        </div>
    </div>

    <script>
        let currentMode = '1';
        let rawBaseText = '';
        let markerIndex = -1;

        function toggleModeOptions() {
            const mode = document.getElementById('modeSelect').value;
            const mode1Opts = document.getElementById('mode1Options');
            if (mode === '2') {
                mode1Opts.classList.add('hidden');
            } else {
                mode1Opts.classList.remove('hidden');
            }
        }

        function startTest() {
            currentMode = document.getElementById('modeSelect').value;
            document.getElementById('setupScreen').classList.add('hidden');
            document.getElementById('appScreen').classList.remove('hidden');

            const targetDisplayEl = document.getElementById('targetDisplay');
            const targetWrapperEl = document.getElementById('targetWrapper');
            const blindInputEl = document.getElementById('blindInput');

            const tHeight = document.getElementById('targetBoxHeight').value;
            const typeHeight = document.getElementById('typingBoxHeight').value;

            targetDisplayEl.style.height = tHeight;
            blindInputEl.style.height = typeHeight;

            const inputField = document.getElementById('blindInput');
            inputField.value = '';
            inputField.focus();

            if (currentMode === '1') {
                rawBaseText = document.getElementById('customText').value;
                markerIndex = -1; 
                document.getElementById('targetWrapper').classList.remove('hidden');
                document.getElementById('clearMarkerBtn').classList.remove('hidden');
                
                const selectedFontSize = document.getElementById('targetFontSize').value;
                targetDisplayEl.style.fontSize = selectedFontSize;

                renderTargetDisplay();

                const position = document.getElementById('positionSelect').value;
                const workspace = document.getElementById('workspace');
                workspace.className = `workspace layout-${position}`;
            } else {
                document.getElementById('targetWrapper').classList.add('hidden');
                document.getElementById('clearMarkerBtn').classList.add('hidden');
            }
        }

        function renderTargetDisplay() {
            const displayBox = document.getElementById('targetDisplay');
            displayBox.innerHTML = '';

            for (let i = 0; i <= rawBaseText.length; i++) {
                if (i === markerIndex) {
                    const badge = document.createElement('span');
                    badge.className = 'pause-marker';
                    badge.textContent = '*';
                    displayBox.appendChild(badge);
                }

                if (i < rawBaseText.length) {
                    const charSpan = document.createElement('span');
                    charSpan.className = 'target-char';
                    charSpan.textContent = rawBaseText[i];
                    const currentIndex = i;
                    
                    // Touch and click friendly event listener for iPhone
                    charSpan.addEventListener('click', (e) => {
                        e.preventDefault();
                        if (markerIndex === currentIndex) {
                            markerIndex = -1; 
                        } else {
                            markerIndex = currentIndex; 
                        }
                        renderTargetDisplay();
                        document.getElementById('blindInput').focus();
                    });
                    
                    displayBox.appendChild(charSpan);
                }
            }
        }

        function clearMarker() {
            markerIndex = -1;
            renderTargetDisplay();
            document.getElementById('blindInput').focus();
        }

        function finishTest() {
            const typedText = document.getElementById('blindInput').value;
            document.getElementById('appScreen').classList.add('hidden');
            document.getElementById('resultsScreen').classList.remove('hidden');

            const resultsContent = document.getElementById('resultsContent');
            resultsContent.innerHTML = '';

            if (currentMode === '1') {
                let fullTargetWithMarker = rawBaseText;
                if (markerIndex !== -1) {
                    fullTargetWithMarker = rawBaseText.slice(0, markerIndex) + '*' + rawBaseText.slice(markerIndex);
                }
                resultsContent.innerHTML = generateDiffHTML(fullTargetWithMarker, typedText);
            } else {
                resultsContent.textContent = typedText;
            }
        }

        function generateDiffHTML(target, typed) {
            let html = "<strong>Comparison (Target vs Typed):</strong><br><br>";
            let maxLength = Math.max(target.length, typed.length);

            for (let i = 0; i < maxLength; i++) {
                let tChar = target[i] || '';
                let uChar = typed[i] || '';

                if (tChar === '*') {
                    html += `<span class="pause-marker" title="Left off marker">*</span>`;
                } else if (uChar === '') {
                    html += `<span class="diff-missing" title="Missing">${tChar}</span>`;
                } else if (tChar === uChar) {
                    html += `<span class="diff-correct">${uChar}</span>`;
                } else {
                    html += `<span class="diff-error" title="Expected '${tChar}', got '${uChar}'"></span>`;
                }
            }
            
            html += "<br><br><small>Legend: <span class='diff-correct'>Match</span> | <span class='diff-error'>Error/Mismatch</span> | <span class='diff-missing'>Missing</span> | <span class='pause-marker'>* Left Off Marker</span></small>";
            return html;
        }

        function resetApp() {
            document.getElementById('resultsScreen').classList.add('hidden');
            document.getElementById('setupScreen').classList.remove('hidden');
        }
    </script>
</body>
</html>
