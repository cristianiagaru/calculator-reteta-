<!DOCTYPE html>
<html lang="ro">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Matrix Optimizer Furaje v8.4 - Strict Weight Steps</title>
    <style>
        :root {
            --primary: #0f172a;
            --accent: #2563eb;
            --bg: #0b0f19;
            --card-bg: #131c2e;
            --text: #f8fafc;
            --text-muted: #94a3b8;
            --border: #1e293b;
            --success: #10b981;
            --danger: #ef4444;
            --warning: #f59e0b;
        }
        body {
            font-family: 'Segoe UI', system-ui, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
        }
        .app-container {
            max-width: 1700px;
            margin: 0 auto;
        }
        header {
            text-align: center;
            margin-bottom: 25px;
            padding: 20px;
            background: linear-gradient(135deg, #1e3a8a, #0f172a);
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        }
        header h1 { margin: 0 0 5px 0; font-size: 1.8rem; }
        header p { margin: 0; color: var(--text-muted); font-size: 13px; }
        
        .main-layout {
            display: grid;
            grid-template-columns: 1.15fr 0.85fr;
            gap: 25px;
        }
        @media (max-width: 1300px) {
            .main-layout { grid-template-columns: 1fr; }
        }
        .card {
            background: var(--card-bg);
            padding: 24px;
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            border: 1px solid var(--border);
            margin-bottom: 25px;
        }
        .card-title {
            font-size: 1.2rem;
            font-weight: bold;
            color: white;
            margin-bottom: 15px;
            border-bottom: 1px solid var(--border);
            padding-bottom: 10px;
        }
        .form-group { margin-bottom: 12px; }
        .form-group label {
            display: block;
            font-weight: 600;
            margin-bottom: 5px;
            font-size: 12px;
            color: var(--text-muted);
        }
        input, button {
            width: 100%;
            padding: 10px;
            border: 1px solid var(--border);
            background-color: #0b0f19;
            color: white;
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 14px;
        }
        input:focus { border-color: var(--accent); outline: none; }
        
        .grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
        .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; }
        .grid-2 { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; }
        
        button { background: var(--accent); color: white; border: none; font-weight: bold; cursor: pointer; }
        button:hover { background: #1d4ed8; }
        button.secondary { background: #334155; color: var(--text); }
        button.optim { background: linear-gradient(135deg, #10b981, #059669); font-size: 1.1rem; padding: 14px; margin-top: 15px; }
        button.optim:hover { background: #047857; }
        button.danger-btn { background: var(--danger); width: auto; padding: 3px 8px; font-size: 11px; margin: 0; }
        
        .checkbox-container {
            display: flex;
            align-items: center;
            gap: 6px;
            background: #0b0f19;
            padding: 8px;
            border-radius: 6px;
            border: 1px dashed var(--border);
        }
        .checkbox-container input { width: auto; cursor: pointer; }

        /* --- CORECHȚIE STRICTĂ BUG VIZUAL TABEL --- */
        .table-wrapper { 
            overflow-x: auto; 
            margin-top: 10px; 
            border-radius: 8px; 
            border: 1px solid var(--border); 
            background-color: #ffffff; 
        }
        table { 
            width: 100%; 
            border-collapse: collapse; 
            font-size: 12px; 
            text-align: left; 
            background-color: #ffffff;
        }
        th { 
            background: #1e293b; 
            color: #ffffff !important; 
            font-weight: bold; 
            padding: 10px 8px;
            white-space: nowrap;
        }
        td { 
            padding: 10px 8px; 
            border-bottom: 1px solid #e2e8f0; 
            color: #0f172a !important; 
            white-space: nowrap; 
        }
        tr {
            background-color: #ffffff;
        }
        tr:hover { 
            background-color: #f8fafc; 
        }

        .control-ingredient-box {
            display: grid;
            grid-template-columns: 2.5fr 1.5fr 1.5fr;
            gap: 15px;
            align-items: center;
            background: #0b0f19;
            padding: 10px 15px;
            border-radius: 6px;
            margin-bottom: 8px;
            border: 1px solid var(--border);
        }

        .metric-cards-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
            gap: 12px;
            margin-top: 15px;
        }
        .metric-card {
            background: #0b0f19;
            padding: 12px;
            border-radius: 8px;
            text-align: center;
            border: 1px solid var(--border);
        }
        .metric-card.highlight { border-color: var(--accent); background: rgba(37,99,235,0.05); }
        .metric-title { font-size: 11px; color: var(--text-muted); text-transform: uppercase; font-weight: bold; }
        .metric-value { font-weight: bold; font-size: 1.3rem; color: white; margin-top: 4px; }
        
        .price-large-box {
            background: rgba(16,185,129,0.1);
            border: 1px solid var(--success);
            border-radius: 8px;
            padding: 18px;
            margin-top: 15px;
            text-align: center;
        }

        .error-diagnostic-box {
            background: rgba(239, 68, 68, 0.1);
            border: 1px solid var(--danger);
            border-radius: 8px;
            padding: 15px;
            margin-top: 15px;
            display: none;
        }
        .error-diagnostic-title { color: #fca5a5; font-weight: bold; font-size: 14px; margin-bottom: 5px; }
        .error-diagnostic-list { margin: 0; padding-left: 20px; font-size: 13px; color: #fecdd3; }

        .section-divider {
            font-size: 11px;
            font-weight: bold;
            color: #f59e0b;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin: 15px 0 8px 0;
            border-bottom: 1px dashed var(--border);
            padding-bottom: 3px;
        }
    </style>
</head>
<body>

<div class="app-container">
    <header>
        <h1>🌾 Matrix Optimizer Furaje Enterprise v8.4</h1>
        <p>Pas Cântărire Inteligent: Cereale/Șroturi la 100g fix | Micro-ingrediente la 10g fix</p>
    </header>

    <div class="main-layout">
        <div>
            <div class="card">
                <div class="card-title">🎯 Setare Valori Nutriționale Target (per 1 KG)</div>
                
                <div class="section-divider">Macro-Nutrienți Solicitati (Barieră Strictă)</div>
                <div class="grid-4">
                    <div class="form-group"><label>Proteină Brută (%)</label><input type="number" step="0.1" id="target-prot" value="16.5"></div>
                    <div class="form-group"><label>Energie ME (kcal/kg)</label><input type="number" step="10" id="target-me" value="2850"></div>
                    <div class="form-group"><label>Grăsimi (%)</label><input type="number" step="0.1" id="target-grasimi" value="3.2"></div>
                    <div class="form-group"><label>Calciu (%)</label><input type="number" step="0.01" id="target-calciu" value="1.00"></div>
                </div>

                <div class="section-divider">Minerale Controlabile Pure (Respectate Obligatoriu)</div>
                <div class="grid-2">
                    <div class="form-group"><label>Sodiu (Na %)</label><input type="number" step="0.01" id="target-na" value="0.16"></div>
                    <div class="form-group"><label>Clor (Cl %)</label><input type="number" step="0.01" id="target-cl" value="0.22"></div>
                </div>

                <div class="section-divider">Fibră Brută Personalizată</div>
                <div class="grid-2">
                    <div class="form-group"><label>Valoare Fibră (%)</label><input type="number" step="0.1" id="target-fibra" value="4.5"></div>
                    <div class="checkbox-container">
                        <input type="checkbox" id="target-opt-fibra-minima" checked onchange="document.getElementById('target-fibra').disabled = this.checked;">
                        <label for="target-opt-fibra-minima" style="margin:0; font-weight:bold; color:#f59e0b; font-size:12px;">📉 Forțează fibră cât mai redusă</label>
                    </div>
                </div>

                <div class="section-divider">Aminoacizi Esențiali Target</div>
                <div class="grid-2">
                    <div class="form-group"><label>Min Lizină (%)</label><input type="number" step="0.01" id="target-lizina" value="0.85"></div>
                    <div class="form-group"><label>Min Metionină (%)</label><input type="number" step="0.01" id="target-metionina" value="0.35"></div>
                </div>
            </div>

            <div class="card">
                <div class="card-title">🔒 Alocare Cantități Directe / Blocaje (per 100 kg Lot)</div>
                <div id="ingredients-controls-container"></div>
                <button class="optim" onclick="ruleazaSistemMatricealV84()">⚡ Execută Optimizare Matriceală v8.4</button>
            </div>

            <div class="error-diagnostic-box" id="diagnostic-panel">
                <div class="error-diagnostic-title">❌ Rețetă Nerealizabilă! Cauze detectate în structura curentă:</div>
                <ul class="error-diagnostic-list" id="diagnostic-list"></ul>
            </div>

            <div class="card" id="results-card" style="display:none;">
                <div class="card-title">📊 Raport Tehnic Detaliat Rețetă Rezultată</div>
                <div class="price-large-box">
                    <div style="font-size: 11px; color: var(--success); font-weight: bold; text-transform: uppercase;">Cost Unitar Amestec Optim:</div>
                    <div style="font-size: 2rem; font-weight: bold; color: white;" id="total-pret-final">0.000 lei / kg</div>
                </div>

                <h4 style="color:white; margin-top:20px; margin-bottom:8px;">⚖️ Rețeta Finală Garantată (Masa Totală: <span id="span-masa-totala" style="color:var(--success);">100.00 kg</span>):</h4>
                <div class="table-wrapper">
                    <table>
                        <thead>
                            <tr>
                                <th>Denumire Ingredient</th>
                                <th>Cantitate de Cântărit (KG)</th>
                                <th>Subdiviziune Cântar (Pas)</th>
                                <th>Cost Ingredient în Lot</th>
                            </tr>
                        </thead>
                        <tbody id="recipe-output-tbody"></tbody>
                    </table>
                </div>

                <h4 style="color:white; margin-top:25px; margin-bottom:5px;">📊 Analiză Parametri Nutriționali Obținuți în Amestec:</h4>
                <div class="metric-cards-container" id="results-nutrients-cards"></div>
            </div>
        </div>

        <div>
            <div class="card">
                <div class="card-title">➕ Configurare Valori Elemente Database</div>
                <form id="ing-form" onsubmit="salveazaIngredient(event)">
                    <div class="form-group"><label>Nume Ingredient:</label><input type="text" id="db-nume" required></div>
                    <div class="form-group"><label>Preț per KG (lei):</label><input type="number" step="0.001" id="db-pret" value="0" required></div>
                    <div class="section-divider">Analiză Organică (%)</div>
                    <div class="grid-3">
                        <div class="form-group"><label>Proteină (%)</label><input type="number" step="0.001" id="db-proteina" value="0"></div>
                        <div class="form-group"><label>Grăsimi (%)</label><input type="number" step="0.001" id="db-grasimi" value="0"></div>
                        <div class="form-group"><label>Fibre (%)</label><input type="number" step="0.001" id="db-fibre" value="0"></div>
                    </div>
                    <div class="grid-2">
                        <div class="form-group"><label>Calciu (%)</label><input type="number" step="0.001" id="db-calciu" value="0"></div>
                        <div class="form-group"><label>ME (kcal/kg)</label><input type="number" step="1" id="db-me" value="0"></div>
                    </div>
                    <div class="section-divider">Aminoacizi (%)</div>
                    <div class="grid-3">
                        <div class="form-group"><label>Lizină (%)</label><input type="number" step="0.001" id="db-lizina" value="0"></div>
                        <div class="form-group"><label>Metionină (%)</label><input type="number" step="0.001" id="db-metionina" value="0"></div>
                        <div class="form-group"><label>Cistină (%)</label><input type="number" step="0.001" id="db-cisteina" value="0"></div>
                    </div>
                    <div class="section-divider">Minerale & Electroliți (%)</div>
                    <div class="grid-3">
                        <div class="form-group"><label>Na (Sodiu %)</label><input type="number" step="0.001" id="db-na" value="0"></div>
                        <div class="form-group"><label>Cl (Clor %)</label><input type="number" step="0.001" id="db-cl" value="0"></div>
                        <div class="form-group"><label>K (Potasiu %)</label><input type="number" step="0.001" id="db-k" value="0"></div>
                    </div>
                    <button type="submit" style="margin-top:15px;">Adaugă / Editează în DB</button>
                    <button type="button" class="secondary" onclick="reseteazaLaBazaDeDateInitiala()" style="margin-top: 5px;">🔄 Resetare Curată DB</button>
                </form>
            </div>

            <div class="card">
                <div class="card-title">📂 Baza de Date Activă în Memorie</div>
                <div class="table-wrapper">
                    <table>
                        <thead>
                            <tr>
                                <th>Ingredient</th><th>Preț</th><th>Prot%</th><th>Gras%</th><th>Fib%</th><th>Ca%</th><th>ME</th><th>Liz%</th><th>Met%</th><th>Na%</th><th>Cl%</th><th>K%</th><th>X</th>
                            </tr>
                        </thead>
                        <tbody id="ingredients-table-body"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    var dateTaleInitiali = [
        { nume: "Porumb", pret: 1.40, proteina: 8.5, grasimi: 3.5, fibre: 2.5, calciu: 0, me: 3350, lizina: 0.25, metionina: 0.175, cisteina: 0.20, k: 0.33, na: 0, cl: 0 },
        { nume: "Grau", pret: 1.25, proteina: 12.0, grasimi: 2.5, fibre: 3.0, calciu: 0, me: 3100, lizina: 0.35, metionina: 0.175, cisteina: 0.25, k: 0.40, na: 0, cl: 0 },
        { nume: "Srot soia", pret: 3.50, proteina: 47.0, grasimi: 1.5, fibre: 6.0, calciu: 0, me: 2500, lizina: 2.70, metionina: 0.60, cisteina: 0.60, k: 1.90, na: 0, cl: 0 },
        { nume: "Srot floare", pret: 1.60, proteina: 28.0, grasimi: 1.5, fibre: 28.0, calciu: 0, me: 1900, lizina: 1.30, metionina: 0.25, cisteina: 0.50, k: 1.10, na: 0, cl: 0 },
        { nume: "Mazare", pret: 2.50, proteina: 23.0, grasimi: 1.5, fibre: 6.0, calciu: 0, me: 0, lizina: 7.00, metionina: 0.90, cisteina: 0, k: 1.00, na: 0, cl: 0 },
        { nume: "Drojdie de bere", pret: 9.00, proteina: 40.0, grasimi: 3.2, fibre: 8.0, calciu: 0, me: 0, lizina: 2.20, metionina: 0.35, cisteina: 0.40, k: 1.80, na: 0, cl: 0 },
        { nume: "Sare", pret: 4.00, proteina: 0, grasimi: 0, fibre: 0, calciu: 0, me: 0, lizina: 0, metionina: 0, cisteina: 0, k: 0, na: 39.30, cl: 60.70 },
        { nume: "Bicarbonat", pret: 6.50, proteina: 0, grasimi: 0, fibre: 0, calciu: 0, me: 0, lizina: 0, metionina: 0, cisteina: 0, k: 0, na: 27.40, cl: 0 },
        { nume: "Premix uniteh", pret: 10.00, proteina: 0, grasimi: 0, fibre: 0, calciu: 0, me: 0, lizina: 0, metionina: 0, cisteina: 0, k: 0, na: 0, cl: 0 },
        { nume: "Lizina sintetica", pret: 22.00, proteina: 0, grasimi: 0, fibre: 0, calciu: 0, me: 0, lizina: 100.0, metionina: 0, cisteina: 0, k: 0, na: 0, cl: 0 },
        { nume: "Metionina sintetica", pret: 24.00, proteina: 0, grasimi: 0, fibre: 0, calciu: 0, me: 0, lizina: 0, metionina: 100.0, cisteina: 0, k: 0, na: 0, cl: 0 },
        { nume: "Ulei", pret: 8.00, proteina: 0, grasimi: 100.0, fibre: 0, calciu: 0, me: 9000, lizina: 0, metionina: 0, cisteina: 0, k: 0, na: 0, cl: 0 },
        { nume: "Calciu", pret: 3.00, proteina: 0, grasimi: 0, fibre: 0, calciu: 46.0, me: 0, lizina: 0, metionina: 0, cisteina: 0, k: 0, na: 0, cl: 0 }
    ];

    var dbIngrediente = dateTaleInitiali;
    try {
        var localData = localStorage.getItem("furaje_v84_db");
        if(localData) dbIngrediente = JSON.parse(localData);
    } catch(e) {}

    function init() {
        renderTabelDB();
        renderControaleMasaFixa();
        document.getElementById("target-fibra").disabled = document.getElementById("target-opt-fibra-minima").checked;
    }

    function reseteazaLaBazaDeDateInitiala() {
        if(confirm("Se revine la baza de date curata.")) {
            dbIngrediente = JSON.parse(JSON.stringify(dateTaleInitiali));
            localStorage.setItem("furaje_v84_db", JSON.stringify(dbIngrediente));
            init();
        }
    }

    function salveazaIngredient(e) {
        e.preventDefault();
        var nume = document.getElementById("db-nume").value.trim();
        var item = {
            nume: nume,
            pret: parseFloat(document.getElementById("db-pret").value) || 0,
            proteina: parseFloat(document.getElementById("db-proteina").value) || 0,
            grasimi: parseFloat(document.getElementById("db-grasimi").value) || 0,
            fibre: parseFloat(document.getElementById("db-fibre").value) || 0,
            calciu: parseFloat(document.getElementById("db-calciu").value) || 0,
            me: parseFloat(document.getElementById("db-me").value) || 0,
            lizina: parseFloat(document.getElementById("db-lizina").value) || 0,
            metionina: parseFloat(document.getElementById("db-metionina").value) || 0,
            cisteina: parseFloat(document.getElementById("db-cisteina").value) || 0,
            na: parseFloat(document.getElementById("db-na").value) || 0,
            cl: parseFloat(document.getElementById("db-cl").value) || 0,
            k: parseFloat(document.getElementById("db-k").value) || 0
        };
        var idx = dbIngrediente.findIndex(i => i.nume.toLowerCase() === nume.toLowerCase());
        if(idx > -1) dbIngrediente[idx] = item;
        else dbIngrediente.push(item);
        localStorage.setItem("furaje_v84_db", JSON.stringify(dbIngrediente));
        document.getElementById("ing-form").reset();
        init();
    }

    function stergeIngredient(nume) {
        dbIngrediente = dbIngrediente.filter(i => i.nume !== nume);
        localStorage.setItem("furaje_v84_db", JSON.stringify(dbIngrediente));
        init();
    }

    function renderTabelDB() {
        var tbody = document.getElementById("ingredients-table-body");
        tbody.innerHTML = "";
        dbIngrediente.forEach(function(ing) {
            var tr = document.createElement("tr");
            tr.innerHTML = `<td><b>${ing.nume}</b></td><td>${ing.pret.toFixed(2)}</td><td>${ing.proteina}%</td><td>${ing.grasimi}%</td><td>${ing.fibre}%</td><td>${ing.calciu}%</td><td>${ing.me}</td><td>${ing.lizina}%</td><td>${ing.metionina}%</td><td>${ing.na}%</td><td>${ing.cl}%</td><td>${ing.k}%</td><td><button type='button' class='danger-btn' onclick='stergeIngredient("${ing.nume}")'>X</button></td>`;
            tbody.appendChild(tr);
        });
    }

    function renderControaleMasaFixa() {
        var container = document.getElementById("ingredients-controls-container");
        container.innerHTML = "";
        dbIngrediente.forEach(function(ing, index) {
            var div = document.createElement("div");
            div.className = "control-ingredient-box";
            
            var esteMicro = verificaDacaEsteMicro(ing.nume);
            var pasText = esteMicro ? "Pas 10g (ex: 0.15)" : "Pas 100g (ex: 25.4)";

            div.innerHTML = `
                <div><b>${ing.nume}</b> <span style="color:var(--text-muted); font-size:11px;">(${pasText})</span></div>
                <div><input type="number" step="0.01" class='ing-exact-val' data-nume='${ing.nume}' placeholder="Liber sau Fix (kg)"></div>
                <div class="checkbox-container">
                    <input type="checkbox" class='ing-block-chk' data-nume='${ing.nume}' id='block-${index}' onchange="toggleBlockInput(this, ${index})">
                    <label for='block-${index}' style="color:var(--danger); font-size:11px; font-weight:bold; margin:0;">Blochează (0 kg)</label>
                </div>
            `;
            container.appendChild(div);
        });
    }

    function verificaDacaEsteMicro(nume) {
        var n = nume.toLowerCase();
        return (n.includes("sare") || n.includes("bicarbonat") || n.includes("sintetica") || n.includes("premix") || n.includes("calciu") || n.includes("ulei"));
    }

    function toggleBlockInput(chk, idx) {
        var inputs = document.querySelectorAll(".ing-exact-val");
        if(chk.checked) { inputs[idx].value = "0"; inputs[idx].disabled = true; } 
        else { inputs[idx].value = ""; inputs[idx].disabled = false; }
    }

    // ===================================================
    // MOTOR MATRICEAL ENTERPRISE V8.4 (ASIMETRIC LOGIC)
    // ===================================================
    function ruleazaSistemMatricealV84() {
        var targetProt = parseFloat(document.getElementById("target-prot").value) || 0;
        var targetMe = parseFloat(document.getElementById("target-me").value) || 0;
        var targetGrasimi = parseFloat(document.getElementById("target-grasimi").value) || 0;
        var targetCalciu = parseFloat(document.getElementById("target-calciu").value) || 0;
        var targetNa = parseFloat(document.getElementById("target-na").value) || 0;
        var targetCl = parseFloat(document.getElementById("target-cl").value) || 0;
        
        var targetFibra = parseFloat(document.getElementById("target-fibra").value) || 0;
        var optFibraMinima = document.getElementById("target-opt-fibra-minima").checked;

        var minLiz = parseFloat(document.getElementById("target-lizina").value) || 0;
        var minMet = parseFloat(document.getElementById("target-metionina").value) || 0;

        document.getElementById("diagnostic-panel").style.display = "none";
        document.getElementById("results-card").style.display = "none";

        var inputsMasa = document.querySelectorAll(".ing-exact-val");
        var chksBlocare = document.querySelectorAll(".ing-block-chk");
        
        var totalMasaFixataUtilizator = 0;
        var structuraMasaFixa = {};
        var ingredienteActiveMatrice = [];

        dbIngrediente.forEach(function(ing) {
            var inputVal = ""; var isBlocked = false;
            inputsMasa.forEach(inp => { if(inp.getAttribute("data-nume") === ing.nume) inputVal = inp.value; });
            chksBlocare.forEach(chk => { if(chk.getAttribute("data-nume") === ing.nume) isBlocked = chk.checked; });

            if(isBlocked || inputVal === "0") {
                structuraMasaFixa[ing.nume] = 0;
            } else if(inputVal !== "") {
                var kgf = parseFloat(inputVal) || 0;
                structuraMasaFixa[ing.nume] = kgf;
                totalMasaFixataUtilizator += kgf;
            } else {
                ingredienteActiveMatrice.push(ing);
            }
        });

        if(totalMasaFixataUtilizator > 100) {
            afiseazaEroriSpecifice(["Cantitățile fixe setate depășesc limita totală de 100 kg!"]);
            return;
        }

        var nVars = dbIngrediente.length;
        var constraints = [];

        // Masa totală = 1.0 (100 kg)
        constraints.push({ coeffs: dbIngrediente.map(() => 1), type: ">=", rhs: 1.0 });
        constraints.push({ coeffs: dbIngrediente.map(() => 1), type: "<=", rhs: 1.0 });

        // Nutrienți cu toleranță mică pentru pre-calculul Simplex
        constraints.push({ coeffs: dbIngrediente.map(i => i.proteina), type: ">=", rhs: targetProt - 0.02 });
        constraints.push({ coeffs: dbIngrediente.map(i => i.proteina), type: "<=", rhs: targetProt + 0.02 });

        constraints.push({ coeffs: dbIngrediente.map(i => i.me), type: ">=", rhs: targetMe - 2 });
        constraints.push({ coeffs: dbIngrediente.map(i => i.me), type: "<=", rhs: targetMe + 2 });

        constraints.push({ coeffs: dbIngrediente.map(i => i.grasimi), type: ">=", rhs: targetGrasimi - 0.02 });
        constraints.push({ coeffs: dbIngrediente.map(i => i.grasimi), type: "<=", rhs: targetGrasimi + 0.02 });

        constraints.push({ coeffs: dbIngrediente.map(i => i.calciu), type: ">=", rhs: targetCalciu - 0.008 });
        constraints.push({ coeffs: dbIngrediente.map(i => i.calciu), type: "<=", rhs: targetCalciu + 0.008 });

        constraints.push({ coeffs: dbIngrediente.map(i => i.na), type: ">=", rhs: targetNa - 0.002 });
        constraints.push({ coeffs: dbIngrediente.map(i => i.na), type: "<=", rhs: targetNa + 0.002 });

        constraints.push({ coeffs: dbIngrediente.map(i => i.cl), type: ">=", rhs: targetCl - 0.002 });
        constraints.push({ coeffs: dbIngrediente.map(i => i.cl), type: "<=", rhs: targetCl + 0.002 });

        if(!optFibraMinima) {
            constraints.push({ coeffs: dbIngrediente.map(i => i.fibre), type: ">=", rhs: targetFibra - 0.08 });
            constraints.push({ coeffs: dbIngrediente.map(i => i.fibre), type: "<=", rhs: targetFibra + 0.08 });
        }

        constraints.push({ coeffs: dbIngrediente.map(i => i.lizina), type: ">=", rhs: minLiz - 0.008 });
        constraints.push({ coeffs: dbIngrediente.map(i => i.metionina), type: ">=", rhs: minMet - 0.008 });

        dbIngrediente.forEach(function(ing, idx) {
            if(structuraMasaFixa[ing.nume] !== undefined) {
                var f = structuraMasaFixa[ing.nume] / 100;
                var arr1 = new Array(nVars).fill(0); arr1[idx] = 1;
                constraints.push({ coeffs: arr1, type: ">=", rhs: f - 0.000001 });
                var arr2 = new Array(nVars).fill(0); arr2[idx] = 1;
                constraints.push({ coeffs: arr2, type: "<=", rhs: f + 0.000001 });
            }
        });

        var objective = dbIngrediente.map(i => optFibraMinima ? (i.pret + (i.fibre * 4.5)) : i.pret);
        var rezultat = solveLinearProgramming(objective, constraints);

        if(rezultat.status === "OPTIMAL") {
            var solutieBruta = rezultat.solution;
            var rețetăKg = {};
            var masaAlocataFaza1 = 0;
            var macroIngredienteLibere = [];

            // FAZA 1: Aplicăm regulile stricte de rotunjire cerute
            dbIngrediente.forEach(function(ing, idx) {
                var kgMatematic = solutieBruta[idx] * 100;
                if(kgMatematic < 0.005) kgMatematic = 0;

                if(structuraMasaFixa[ing.nume] !== undefined) {
                    rețetăKg[ing.nume] = structuraMasaFixa[ing.nume];
                    masaAlocataFaza1 += structuraMasaFixa[ing.nume];
                } else {
                    if(verificaDacaEsteMicro(ing.nume)) {
                        // Pas de 10 grame = Rotunjire la 2 zecimale (Ex: 0.15 kg)
                        rețetăKg[ing.nume] = Math.round(kgMatematic * 100) / 100;
                        masaAlocataFaza1 += rețetăKg[ing.nume];
                    } else {
                        macroIngredienteLibere.push({ nume: ing.nume, valIdeala: kgMatematic });
                    }
                }
            });

            // FAZA 2: Distribuirea masei macro la pas de 100g (1 zecimală)
            var spatiuRamasMacro = 100 - masaAlocataFaza1;
            var sumaIdealaMacro = macroIngredienteLibere.reduce((a, b) => a + b.valIdeala, 0);

            if(sumaIdealaMacro > 0) {
                macroIngredienteLibere.forEach(macro => {
                    var pondere = macro.valIdeala / sumaIdealaMacro;
                    var calculCalculat = pondere * spatiuRamasMacro;
                    // Pas de 100 grame = Rotunjire la 1 singură zecimală (Ex: 45.3 kg)
                    rețetăKg[macro.nume] = Math.round(calculCalculat * 10) / 10;
                });
            }

            // FAZA 3: Calibrare micrometrică pentru a absorbi erorile de rotunjire
            // Găsim cel mai mare macro-ingredient activ pentru ajustare
            var numeMacroPrincipal = ""; var maxV = -1;
            dbIngrediente.forEach(i => {
                if(!verificaDacaEsteMicro(i.nume) && structuraMasaFixa[i.nume] === undefined) {
                    if((rețetăKg[i.nume] || 0) > maxV) {
                        maxV = rețetăKg[i.nume]; numeMacroPrincipal = i.nume;
                    }
                }
            });

            // Recalculăm suma lotului curent rotunjit
            var sumaCurentaVerificare = 0;
            dbIngrediente.forEach(i => { sumaCurentaVerificare += (rețetăKg[i.nume] || 0); });
            
            var diferentaInKg = 100 - sumaCurentaVerificare;
            if(Math.abs(diferentaInKg) > 0.001 && numeMacroPrincipal !== "") {
                // Corecția se face la nivel de 100 grame (0.1 kg) pentru a păstra regula
                rețetăKg[numeMacroPrincipal] = Math.round((rețetăKg[numeMacroPrincipal] + diferentaInKg) * 10) / 10;
            }

            // Randare tabel rezultate
            var tbody = document.getElementById("recipe-output-tbody");
            tbody.innerHTML = "";
            var costTotalReala = 0;
            var verificareMasaTotala = 0;

            dbIngrediente.forEach(function(ing) {
                var cantitateFinalaKg = rețetăKg[ing.nume] || 0;
                if(cantitateFinalaKg > 0) {
                    verificareMasaTotala += cantitateFinalaKg;
                    var costPondere = (cantitateFinalaKg / 100) * ing.pret * 100;
                    costTotalReala += (cantitateFinalaKg / 100) * ing.pret;
                    
                    var tipPas = verificaDacaEsteMicro(ing.nume) ? "±10g (Micro)" : "±100g (Cereale/Șrot)";

                    tbody.innerHTML += `<tr>
                        <td><b>${ing.nume}</b></td>
                        <td><span style="font-size:15px; color:var(--success); font-weight:bold;">${cantitateFinalaKg.toFixed(2)} kg</span></td>
                        <td><small style="font-weight:bold;">${tipPas}</small></td>
                        <td>${costPondere.toFixed(2)} lei</td>
                    </tr>`;
                }
            });

            document.getElementById("span-masa-totala").innerText = verificareMasaTotala.toFixed(1) + "0 kg";
            document.getElementById("total-pret-final").innerText = costTotalReala.toFixed(3) + " lei / kg";
            document.getElementById("results-card").style.display = "block";
            
            renderCaseteNutritiev84(rețetăKg);
        } else {
            executaDiagnosticDeficientev84(ingredienteActiveMatrice, targetProt, targetMe, targetGrasimi, targetCalciu, targetNa, targetCl, minLiz, minMet, totalMasaFixataUtilizator);
        }
    }

    function executaDiagnosticDeficientev84(activeIngs, tProt, tMe, tGras, tCa, tNa, tCl, tLiz, tMet, totalMasaFixataUtilizator) {
        var motive = [];
        var spatiuRamas = (100 - totalMasaFixataUtilizator) / 100;
        var aportFixat = { proteina: 0, me: 0, grasimi: 0, calciu: 0, na: 0, cl: 0, lizina: 0, metionina: 0 };
        var inputsMasa = document.querySelectorAll(".ing-exact-val");
        
        dbIngrediente.forEach(ing => {
            var v = ""; inputsMasa.forEach(inp => { if(inp.getAttribute("data-nume") === ing.nume) v = inp.value; });
            if(v !== "" && v !== "0") {
                var f = parseFloat(v) / 100;
                aportFixat.proteina += f * ing.proteina; aportFixat.me += f * ing.me; aportFixat.grasimi += f * ing.grasimi;
                aportFixat.calciu += f * ing.calciu; aportFixat.na += f * ing.na; aportFixat.cl += f * ing.cl;
                aportFixat.lizina += f * ing.lizina; aportFixat.metionina += f * ing.metionina;
            }
        });

        if(tProt > (aportFixat.proteina + (activeIngs.reduce((max, i) => Math.max(max, i.proteina), 0) * spatiuRamas))) {
            motive.push(`<b>Proteină Brută imposibilă:</b> Targetul de ${tProt}% e nerealizabil.`);
        }
        if(tMe > (aportFixat.me + (activeIngs.reduce((max, i) => Math.max(max, i.me), 0) * spatiuRamas))) {
            motive.push(`<b>Energie imposibilă:</b> Cerere de ${tMe} kcal prea mare.`);
        }
        if(tNa > (aportFixat.na + (activeIngs.reduce((max, i) => Math.max(max, i.na), 0) * spatiuRamas))) {
            motive.push(`<b>Sodiu (Na) blocat:</b> Deblochează Sarea/Bicarbonatul.`);
        }
        if(tCl > (aportFixat.cl + (activeIngs.reduce((max, i) => Math.max(max, i.cl), 0) * spatiuRamas))) {
            motive.push(`<b>Clor (Cl) irealizabil:</b> Necesită eliberare Sare.`);
        }

        if(motive.length === 0) {
            motive.push("<b>Conflict de Rotunjire în Rețetă:</b> Pasul fix de 100g la cereale combinat cu restricțiile introduse manual blochează posibilitatea găsirii unei soluții exacte. Oferă mai mult spațiu liber la cereale.");
        }
        afiseazaEroriSpecifice(motive);
    }

    function afiseazaEroriSpecifice(liste) {
        var panel = document.getElementById("diagnostic-panel");
        var ul = document.getElementById("diagnostic-list");
        ul.innerHTML = "";
        liste.forEach(m => { ul.innerHTML += `<li>${m}</li>`; });
        panel.style.display = "block";
    }

    function renderCaseteNutritiev84(rețetă) {
        var t = { proteina: 0, grasimi: 0, fibre: 0, calciu: 0, me: 0, lizina: 0, metionina: 0, cisteina: 0, na: 0, k: 0, cl: 0 };
        
        dbIngrediente.forEach(function(ing) {
            var kg = rețetă[ing.nume] || 0;
            var f = kg / 100;
            t.proteina += f * ing.proteina; t.grasimi += f * ing.grasimi; t.fibre += f * ing.fibre;
            t.calciu += f * ing.calciu; t.me += f * ing.me; t.lizina += f * ing.lizina;
            t.metionina += f * ing.metionina; t.cisteina += f * ing.cisteina;
            t.na += f * ing.na; t.k += f * ing.k; t.cl += f * ing.cl;
        });

        var DEB = ((t.na * 10)/0.023) + ((t.k * 10)/0.0391) - ((t.cl * 10)/0.0355);
        
        document.getElementById("results-nutrients-cards").innerHTML = `
            <div class="metric-card highlight" style="grid-column: span 3;"><div class="metric-title">Balanță Electrolitică (DEB Obținut Din Rețetă)</div><div class="metric-value" style="color:#34d399; font-size:1.6rem;">${Math.round(DEB)} mEq/kg</div></div>
            <div class="metric-card"><div class="metric-title">Proteină Brută</div><div class="metric-value">${t.proteina.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Energie (ME)</div><div class="metric-value">${Math.round(t.me)} kcal</div></div>
            <div class="metric-card"><div class="metric-title">Grăsimi</div><div class="metric-value">${t.grasimi.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Fibră Brută</div><div class="metric-value" style="color:#fbbf24;">${t.fibre.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Calciu (Ca)</div><div class="metric-value">${t.calciu.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Sodiu (Na)</div><div class="metric-value">${t.na.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Clor (Cl)</div><div class="metric-value">${t.cl.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Potasiu (K)*</div><div class="metric-value" style="color:#60a5fa;">${t.k.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Lizină</div><div class="metric-value">${t.lizina.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Metionină</div><div class="metric-value">${t.metionina.toFixed(2)} %</div></div>
            <div class="metric-card"><div class="metric-title">Cistină (Calculat)*</div><div class="metric-value" style="color:#a78bfa;">${t.cisteina.toFixed(2)} %</div></div>
        `;
    }

    // SIMPLEX ALGORITHM
    function solveLinearProgramming(objective, constraints) {
        var m = constraints.length; var n = objective.length; var table = [];
        for(var i = 0; i <= m; i++) table.push(new Array(n + m + 1).fill(0));
        for(var i = 0; i < m; i++) {
            var c = constraints[i];
            for(var j = 0; j < n; j++) table[i][j] = c.type === ">=" ? -c.coeffs[j] : c.coeffs[j];
            table[i][n + i] = 1; table[i][n + m] = c.type === ">=" ? -c.rhs : c.rhs;
        }
        for(var j = 0; j < n; j++) table[m][j] = objective[j];
        var basis = []; for(var i = 0; i < m; i++) basis.push(n + i);
        var maxIterations = 3000, iter = 0;
        while(iter < maxIterations) {
            var pivotRow = -1, minRhs = -1e-6;
            for(var i = 0; i < m; i++) { if(table[i][n+m] < minRhs) { minRhs = table[i][n+m]; pivotRow = i; } }
            if(pivotRow === -1) {
                var pivotCol = -1, maxCost = 1e-6;
                for(var j = 0; j < n + m; j++) { if(table[m][j] < -maxCost) { maxCost = -table[m][j]; pivotCol = j; } }
                if(pivotCol === -1) {
                    var solution = new Array(n).fill(0);
                    for(var i = 0; i < m; i++) { if(basis[i] < n) solution[basis[i]] = table[i][n+m]; }
                    return { status: "OPTIMAL", solution: solution };
                }
                var minRatio = Infinity; pivotRow = -1;
                for(var i = 0; i < m; i++) {
                    if(table[i][pivotCol] > 1e-6) {
                        var ratio = table[i][n+m] / table[i][pivotCol];
                        if(ratio < minRatio) { minRatio = ratio; pivotRow = i; }
                    }
                }
                if(pivotRow === -1) return { status: "UNBOUNDED", solution: null };
                pivot(table, pivotRow, pivotCol, m, n + m); basis[pivotRow] = pivotCol;
            } else {
                var pivotCol = -1, maxRatio = -Infinity;
                for(var j = 0; j < n + m; j++) {
                    if(table[pivotRow][j] < -1e-6) {
                        var ratio = table[m][j] / table[pivotRow][j];
                        if(ratio > maxRatio) { maxRatio = ratio; pivotCol = j; }
                    }
                }
                if(pivotCol === -1) return { status: "INFEASIBLE", solution: null };
                pivot(table, pivotRow, pivotCol, m, n + m); basis[pivotRow] = pivotCol;
            }
            iter++;
        }
        return { status: "FAILED", solution: null };
    }

    function pivot(table, row, col, m, numVars) {
        var pivotVal = table[row][col];
        for(var j = 0; j <= numVars; j++) table[row][j] /= pivotVal;
        for(var i = 0; i <= m; i++) {
            if(i !== row) {
                var factor = table[i][col];
                for(var j = 0; j <= numVars; j++) table[i][j] -= factor * table[row][j];
            }
        }
    }
    window.onload = init;
</script>
</body>
</html>