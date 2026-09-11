<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel Links GF - Automação Google Drive</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- SheetJS (XLSX) -->
    <script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- html2pdf.js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @page {
            size: A3 landscape;
            margin: 8mm;
        }

        @media print {
            .no-print { display: none !important; }
            body { 
                background: white !important; 
                font-size: 9pt !important;
                width: 100% !important;
                margin: 0 !important;
                padding: 0 !important;
            }
            main { 
                max-width: 100% !important; 
                padding: 0 !important; 
            }
            .overflow-x-auto { 
                overflow: visible !important; 
                max-height: none !important; 
            }
            table { 
                width: 100% !important; 
                page-break-inside: auto; 
            }
            tr { 
                page-break-inside: avoid; 
                page-break-after: auto; 
            }
        }
    </style>
</head>
<body class="bg-slate-100 font-sans min-h-screen text-slate-800">

    <!-- TELA DE LOGIN / BLOQUEIO POR SENHA -->
    <div id="loginOverlay" class="fixed inset-0 bg-slate-900/95 z-50 flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-2xl shadow-2xl p-8 max-w-md w-full text-center space-y-5 border border-slate-200">
            <img src="logo.png" alt="Logo GF Links" class="h-12 w-auto mx-auto object-contain" onerror="this.style.display='none'">
            <div class="bg-indigo-100 text-indigo-600 w-16 h-16 rounded-full flex items-center justify-center mx-auto text-2xl shadow-inner">
                <i class="fa-solid fa-shield-halved"></i>
            </div>
            <div>
                <h2 class="text-xl font-bold text-slate-800">Acesso Restrito</h2>
                <p class="text-xs text-slate-500 mt-1">Informe a senha de acesso para visualizar o Painel Links GF</p>
            </div>
            <form onsubmit="checkPassword(event)" class="space-y-4">
                <div>
                    <input type="password" id="accessPassword" placeholder="Digite a senha..." class="w-full text-sm px-4 py-3 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 text-center font-semibold tracking-wider bg-slate-50">
                    <p id="loginError" class="text-xs text-rose-600 font-semibold mt-2 hidden"><i class="fa-solid fa-circle-exclamation mr-1"></i>Senha incorreta. Tente novamente.</p>
                </div>
                <button type="submit" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-semibold py-3 rounded-lg transition text-sm shadow-md flex items-center justify-center gap-2">
                    <i class="fa-solid fa-key text-xs"></i> Acessar Painel
                </button>
            </form>
        </div>
    </div>

    <!-- Header Navbar -->
    <header class="bg-slate-900 text-white shadow-lg no-print">
        <div class="max-w-[1800px] mx-auto px-6 py-4 flex flex-col md:flex-row justify-between items-center gap-4">
            <div class="flex items-center space-x-4">
                <img src="logo.png" alt="Logo GF Links" class="h-10 w-auto max-h-12 object-contain" onerror="this.style.display='none'">
                <i class="fa-solid fa-network-wired text-emerald-400 text-2xl"></i>
                <div>
                    <h1 class="text-xl font-bold tracking-wide">Painel Links GF</h1>
                    <p class="text-xs text-slate-400" id="dbStatusBadge">Status DB: Vazio</p>
                </div>
            </div>
            
            <div class="flex items-center gap-3 flex-wrap">
                <button onclick="syncDriveData()" id="btnSyncDrive" class="bg-sky-600 hover:bg-sky-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-rotate text-base" id="syncIcon"></i> Sincronizar Google Drive
                </button>

                <label for="excelFile" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg cursor-pointer transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-file-excel text-base"></i> Carregar Manual
                </label>
                <input type="file" id="excelFile" accept=".xlsx, .xls, .csv" class="hidden">

                <button onclick="exportToPDF()" id="btnPdf" disabled class="bg-amber-600 hover:bg-amber-500 disabled:opacity-40 disabled:cursor-not-allowed text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-file-pdf text-base"></i> Salvar PDF
                </button>

                <button onclick="exportToExcel()" id="btnExport" disabled class="bg-emerald-600 hover:bg-emerald-500 disabled:opacity-40 disabled:cursor-not-allowed text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-download text-base"></i> Baixar Excel
                </button>

                <button onclick="clearDatabase()" id="btnClearDb" class="bg-rose-700 hover:bg-rose-600 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-trash-can text-base"></i> Limpar Banco
                </button>

                <button onclick="logout()" class="bg-slate-700 hover:bg-slate-600 text-white text-xs font-semibold px-3 py-2.5 rounded-lg transition shadow flex items-center gap-1.5" title="Sair do Painel">
                    <i class="fa-solid fa-right-from-bracket"></i> Sair
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-[1800px] mx-auto px-6 py-6 space-y-6" id="pdfContent">

        <!-- Banner de Carregamento Automático do Drive -->
        <div id="driveLoadingBanner" class="hidden bg-sky-50 border-l-4 border-sky-500 text-sky-800 p-4 rounded-xl shadow-sm flex items-center justify-between no-print">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-circle-notch fa-spin text-sky-600 text-xl"></i>
                <div>
                    <p class="text-xs font-bold">Sincronizando com o Google Drive...</p>
                    <p class="text-[11px] text-sky-600">Buscando atualizações da planilha na nuvem.</p>
                </div>
            </div>
        </div>

        <!-- MÓDULO 1: CONSULTA BD_AUXILIAR -->
        <section class="bg-white rounded-xl shadow-sm border border-slate-200 p-5">
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
                <div class="flex items-center gap-3">
                    <img src="logo.png" alt="Logo GF Links" class="h-8 w-auto object-contain hidden md:block" onerror="this.style.display='none'">
                    <div>
                        <h2 class="text-sm font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-address-book text-indigo-600 text-lg"></i>
                            Consulta de Cadastros e CIs (BD_Auxiliar)
                        </h2>
                        <p class="text-xs text-slate-500 mt-0.5" id="bdStatusText">
                            Pesquise por Código CIS, Sigla, Unidade ou Endereço cadastrado na aba BD_Auxiliar.
                        </p>
                    </div>
                </div>
                <div class="relative w-full md:w-96 no-print">
                    <input type="text" id="bdSearchInput" onkeyup="searchBDByCIS()" placeholder="Digite CIS, Sigla ou Endereço..." class="w-full text-xs pl-9 pr-3 py-2.5 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 font-medium bg-slate-50 focus:bg-white">
                    <i class="fa-solid fa-magnifying-glass absolute left-3 top-3 text-slate-400 text-xs"></i>
                </div>
            </div>

            <div id="bdSearchResult" class="mt-4 hidden border-t border-slate-100 pt-3">
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs border-collapse">
                        <thead>
                            <tr class="bg-slate-800 text-white font-semibold">
                                <th class="p-2.5">CI / CÓDIGO</th>
                                <th class="p-2.5">SIGLA</th>
                                <th class="p-2.5">RESTAURANTE / UNIDADE</th>
                                <th class="p-2.5">ENDEREÇO COMPLETO</th>
                                <th class="p-2.5">UF</th>
                            </tr>
                        </thead>
                        <tbody id="bdSearchResultBody" class="divide-y divide-slate-100 text-slate-700"></tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- Dropzone de Arquivo -->
        <div id="dropZone" class="bg-white border-2 border-dashed border-slate-300 rounded-xl p-12 text-center shadow-sm hover:border-indigo-500 transition cursor-pointer" onclick="document.getElementById('excelFile').click()">
            <img src="logo.png" alt="Logo GF Links" class="h-16 w-auto mx-auto mb-3 object-contain opacity-80" onerror="this.style.display='none'">
            <i class="fa-solid fa-cloud-arrow-up text-5xl text-indigo-500 mb-3"></i>
            <h3 class="text-lg font-bold text-slate-700">Clique para carregar uma planilha local ou use a sincronização do Drive</h3>
            <p class="text-xs text-slate-500 mt-1">Os dados do Painel Links GF são atualizados e salvos automaticamente.</p>
        </div>

        <!-- Dashboard -->
        <div id="dashboardSection" class="hidden space-y-6">

            <!-- MÉTRICAS STATUS -->
            <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                <div class="flex items-center justify-between mb-4 border-b border-slate-100 pb-3">
                    <h3 class="text-sm font-bold text-slate-800 uppercase tracking-wide flex items-center gap-2">
                        <i class="fa-solid fa-list-check text-sky-600 text-base"></i>
                        Métricas de STATUS (Coluna F)
                    </h3>
                    <span class="text-xs font-semibold text-indigo-600 bg-indigo-50 px-2.5 py-1 rounded-full"><i class="fa-solid fa-hand-pointer mr-1"></i>Clique no banner para filtrar</span>
                </div>
                <div id="statusCardsContainer" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4"></div>
            </div>

            <!-- MÉTRICAS COBRANÇA -->
            <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                <div class="flex items-center justify-between mb-4 border-b border-slate-100 pb-3">
                    <h3 class="text-sm font-bold text-slate-800 uppercase tracking-wide flex items-center gap-2">
                        <i class="fa-solid fa-file-invoice-dollar text-emerald-600 text-base"></i>
                        Métricas de COBRANÇA (Coluna E)
                    </h3>
                    <span class="text-xs font-semibold text-indigo-600 bg-indigo-50 px-2.5 py-1 rounded-full"><i class="fa-solid fa-hand-pointer mr-1"></i>Clique no banner para filtrar</span>
                </div>
                <div id="cobrancaCardsContainer" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4"></div>
            </div>

            <!-- Gráficos -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-chart-pie text-indigo-500"></i> Distribuição dos Status (Coluna F)
                    </h4>
                    <div class="h-64 relative">
                        <canvas id="chartStatus"></canvas>
                    </div>
                </div>

                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-chart-column text-emerald-500"></i> Distribuição das Cobranças (Coluna E)
                    </h4>
                    <div class="h-64 relative">
                        <canvas id="chartCobranca"></canvas>
                    </div>
                </div>
            </div>

            <!-- TABELA E FILTROS -->
            <div id="tableSectionContainer" class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <div class="p-4 border-b border-slate-100 flex flex-col lg:flex-row justify-between items-start lg:items-center gap-4 bg-slate-50/50">
                    <div>
                        <h3 class="text-sm font-bold text-slate-800">Tabela Geral de Registros - Painel Links GF</h3>
                        <p class="text-xs text-slate-500">Dados sincronizados do Google Drive & BD Local</p>
                    </div>

                    <div class="flex flex-wrap gap-2 items-center w-full lg:w-auto justify-end no-print">
                        <input type="text" id="tableSearchInput" onkeyup="filterTable()" placeholder="Buscar..." class="text-xs px-3 py-2 border border-slate-300 rounded-lg bg-white">
                        <select id="filterCisNumNI" onchange="filterTable()" class="text-xs bg-amber-50 border border-amber-300 text-amber-900 rounded-lg px-3 py-2 font-bold">
                            <option value="">Filtro CIS Numérica: Todos</option>
                            <option value="ONLY_NUMERIC_NI">Apenas CIS Numérica + Cobrança N/I</option>
                        </select>
                        <select id="filterStatusColF" onchange="filterTable()" class="text-xs bg-white border border-slate-300 rounded-lg px-3 py-2 font-semibold text-slate-700">
                            <option value="">STATUS: Todos</option>
                        </select>
                        <select id="filterCobrancaColE" onchange="filterTable()" class="text-xs bg-white border border-slate-300 rounded-lg px-3 py-2 font-semibold text-slate-700">
                            <option value="">COBRANÇA: Todas</option>
                        </select>
                        <button onclick="resetFilters()" class="text-xs bg-slate-200 hover:bg-slate-300 text-slate-700 font-semibold px-3 py-2 rounded-lg transition">Limpar Filtros</button>
                    </div>
                </div>

                <div class="overflow-x-auto max-h-[600px]">
                    <table class="w-full text-left border-collapse text-[11px]">
                        <thead>
                            <tr id="tableHeader" class="bg-slate-800 text-white font-semibold sticky top-0 z-10"></tr>
                        </thead>
                        <tbody id="ativosTableBody" class="divide-y divide-slate-100 text-slate-700"></tbody>
                    </table>
                </div>

                <div class="p-3 bg-slate-50 text-xs text-slate-500 flex justify-between items-center border-t border-slate-100">
                    <span>Mostrando <b id="displayedCount">0</b> de <b id="totalCount">0</b> registros</span>
                </div>
            </div>

        </div>
    </main>

    <script>
        const AUTH_KEY = 'GF_PANEL_AUTH';
        const TARGET_PASSWORD = 'gF@2026*Link';
        const DRIVE_FILE_ID = '1P88V6dzw8kXwkcIPCufkSMPdtp4DHPg02fdvcF8TYXE';

        const DB_KEY_DATA = 'APP_ATIVOS_DATA';
        const DB_KEY_HEADERS = 'APP_ATIVOS_HEADERS';
        const DB_KEY_AUX = 'APP_BD_AUXILIAR';

        let rawAtivosData = [];
        let excelHeaders = [];
        let bdAuxiliarMap = new Map();
        let chartStatusObj, chartCobrancaObj;
        let statusColName = "";
        let cobrancaColName = "";

        window.addEventListener('DOMContentLoaded', () => {
            if (sessionStorage.getItem(AUTH_KEY) === 'true') {
                document.getElementById('loginOverlay').classList.add('hidden');
                initApp();
            }
        });

        function checkPassword(e) {
            e.preventDefault();
            const input = document.getElementById('accessPassword').value;
            const error = document.getElementById('loginError');

            if (input === TARGET_PASSWORD) {
                sessionStorage.setItem(AUTH_KEY, 'true');
                document.getElementById('loginOverlay').classList.add('hidden');
                error.classList.add('hidden');
                initApp();
            } else {
                error.classList.remove('hidden');
            }
        }

        function initApp() {
            loadFromDatabase();
            syncDriveData();
        }

        function logout() {
            sessionStorage.removeItem(AUTH_KEY);
            document.getElementById('accessPassword').value = '';
            document.getElementById('loginOverlay').classList.remove('hidden');
        }

        // AUTOMATIZAÇÃO DE CARREGAMENTO VIA GOOGLE DRIVE API/EXPORT
        async function syncDriveData() {
            const banner = document.getElementById('driveLoadingBanner');
            const syncIcon = document.getElementById('syncIcon');

            if (banner) banner.classList.remove('hidden');
            if (syncIcon) syncIcon.classList.add('fa-spin');

            const downloadUrl = `https://docs.google.com/spreadsheets/d/${DRIVE_FILE_ID}/export?format=xlsx`;

            try {
                const response = await fetch(downloadUrl);
                if (!response.ok) throw new Error('Falha ao baixar do Google Drive');

                const arrayBuffer = await response.arrayBuffer();
                const workbook = XLSX.read(new Uint8Array(arrayBuffer), { type: 'array', cellDates: true, dateNF: 'dd/mm/yyyy' });
                parseWorkbook(workbook);
            } catch (err) {
                console.warn("Automação Drive necessita de permissão aberta ou execução via servidor web:", err);
            } finally {
                if (banner) banner.classList.add('hidden');
                if (syncIcon) syncIcon.classList.remove('fa-spin');
            }
        }

        document.getElementById('excelFile').addEventListener('change', (e) => {
            if (e.target.files.length) processExcelFile(e.target.files[0]);
        });

        const dropZone = document.getElementById('dropZone');
        ['dragenter', 'dragover'].forEach(name => {
            dropZone.addEventListener(name, (e) => { e.preventDefault(); dropZone.classList.add('border-indigo-500', 'bg-indigo-50'); });
        });
        ['dragleave', 'drop'].forEach(name => {
            dropZone.addEventListener(name, (e) => { e.preventDefault(); dropZone.classList.remove('border-indigo-500', 'bg-indigo-50'); });
        });
        dropZone.addEventListener('drop', (e) => {
            e.preventDefault();
            if (e.dataTransfer.files.length) processExcelFile(e.dataTransfer.files[0]);
        });

        function cleanStr(val) {
            if (val === undefined || val === null) return "";
            return String(val).trim();
        }

        function isNumericCIS(val) {
            if (val === undefined || val === null) return false;
            let str = String(val).trim();
            if (!str || str === "-" || str === "N/I" || str === "0") return false;
            if (str.endsWith('.0')) str = str.slice(0, -2);
            return /^\d+$/.test(str);
        }

        function formatValue(val) {
            if (val === undefined || val === null) return "-";
            if (val instanceof Date) {
                if (isNaN(val.getTime())) return "-";
                return `${String(val.getUTCDate()).padStart(2, '0')}/${String(val.getUTCMonth() + 1).padStart(2, '0')}/${val.getUTCFullYear()}`;
            }
            if (typeof val === 'number' && val > 20000 && val < 60000) {
                const dateObj = new Date(new Date(Date.UTC(1899, 11, 30)).getTime() + val * 86400000);
                return `${String(dateObj.getUTCDate()).padStart(2, '0')}/${String(dateObj.getUTCMonth() + 1).padStart(2, '0')}/${dateObj.getUTCFullYear()}`;
            }
            const str = String(val).trim();
            if (/^\d{4}-\d{2}-\d{2}/.test(str)) {
                const parts = str.split('T')[0].split('-');
                return `${parts[2]}/${parts[1]}/${parts[0]}`;
            }
            return str || "-";
        }

        function saveToDatabase() {
            try {
                localStorage.setItem(DB_KEY_DATA, JSON.stringify(rawAtivosData));
                localStorage.setItem(DB_KEY_HEADERS, JSON.stringify(excelHeaders));
                localStorage.setItem(DB_KEY_AUX, JSON.stringify(Array.from(bdAuxiliarMap.entries())));
                updateDbBadge(true);
            } catch (err) {
                console.error("Erro ao salvar no banco local:", err);
            }
        }

        function loadFromDatabase() {
            const storedData = localStorage.getItem(DB_KEY_DATA);
            const storedHeaders = localStorage.getItem(DB_KEY_HEADERS);
            const storedAux = localStorage.getItem(DB_KEY_AUX);

            if (storedData && storedHeaders) {
                rawAtivosData = JSON.parse(storedData);
                excelHeaders = JSON.parse(storedHeaders);
                if (storedAux) bdAuxiliarMap = new Map(JSON.parse(storedAux));

                if (excelHeaders.length >= 5) cobrancaColName = excelHeaders[4];
                if (excelHeaders.length >= 6) statusColName = excelHeaders[5];

                document.getElementById('dropZone').classList.add('hidden');
                document.getElementById('dashboardSection').classList.remove('hidden');
                document.getElementById('btnExport').disabled = false;
                document.getElementById('btnPdf').disabled = false;

                document.getElementById('bdStatusText').innerText = `Base BD_Auxiliar carregada do banco local (${bdAuxiliarMap.size} cadastros).`;

                renderTableHeaders();
                calculateMetrics();
                filterTable();
                updateDbBadge(true);
            } else {
                updateDbBadge(false);
            }
        }

        function clearDatabase() {
            if (confirm("Tem certeza que deseja apagar o Banco de Dados local e limpar o Painel Links GF?")) {
                localStorage.removeItem(DB_KEY_DATA);
                localStorage.removeItem(DB_KEY_HEADERS);
                localStorage.removeItem(DB_KEY_AUX);

                rawAtivosData = [];
                excelHeaders = [];
                bdAuxiliarMap.clear();

                document.getElementById('dashboardSection').classList.add('hidden');
                document.getElementById('dropZone').classList.remove('hidden');
                document.getElementById('btnExport').disabled = true;
                document.getElementById('btnPdf').disabled = true;
                document.getElementById('excelFile').value = '';
                document.getElementById('bdStatusText').innerText = "Pesquise por Código CIS, Sigla, Unidade ou Endereço cadastrado na aba BD_Auxiliar.";

                updateDbBadge(false);
                alert("Banco de dados local limpo com sucesso!");
            }
        }

        function updateDbBadge(hasData) {
            const badge = document.getElementById('dbStatusBadge');
            if (hasData) {
                badge.innerText = `Status DB: Ativo (${rawAtivosData.length} registros)`;
                badge.className = "text-xs text-emerald-400 font-semibold";
            } else {
                badge.innerText = "Status DB: Vazio";
                badge.className = "text-xs text-slate-400";
            }
        }

        function processExcelFile(file) {
            const reader = new FileReader();
            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, { type: 'array', cellDates: true, dateNF: 'dd/mm/yyyy' });
                parseWorkbook(workbook);
            };
            reader.readAsArrayBuffer(file);
        }

        function parseWorkbook(workbook) {
            let sheetAtivos = null, sheetBD = null;

            workbook.SheetNames.forEach(name => {
                const norm = name.trim().toUpperCase();
                if (norm.includes("ATIVO") || norm.includes("VIVO") || norm.includes("CLARO") || norm.includes("LINK")) sheetAtivos = workbook.Sheets[name];
                else if (norm.includes("BD") || norm.includes("AUXILIAR") || norm.includes("CADASTRO")) sheetBD = workbook.Sheets[name];
            });

            if (!sheetAtivos) sheetAtivos = workbook.Sheets[workbook.SheetNames[0]];
            if (!sheetBD && workbook.SheetNames.length > 1) sheetBD = workbook.Sheets[workbook.SheetNames[1]];

            bdAuxiliarMap.clear();
            if (sheetBD) {
                const rowsBD = XLSX.utils.sheet_to_json(sheetBD, { defval: "", cellDates: true });
                rowsBD.forEach(r => {
                    const ci = cleanStr(r["CIS"] || r["CI"] || r["CÓDIGO"] || r["CODIGO"] || r["CÓDIGO CIS"]);
                    const sigla = cleanStr(r["SIGLA"] || r["Sigla"]);
                    const nome = cleanStr(r["NOME DO RESTAURANTE"] || r["RESTAURANTE"] || r["UNIDADE"] || r["NOME"]);
                    const endereco = cleanStr(r["ENDEREÇO"] || r["ENDERECO"] || r["LOGRADOURO"]);
                    const uf = cleanStr(r["ESTADO"] || r["UF"]);

                    const entry = { ci, sigla, nome, endereco, uf };
                    if (ci) bdAuxiliarMap.set(ci.toUpperCase(), entry);
                    if (sigla) bdAuxiliarMap.set(sigla.toUpperCase(), entry);
                });
                document.getElementById('bdStatusText').innerText = `Base BD_Auxiliar carregada com ${bdAuxiliarMap.size} cadastros.`;
            }

            rawAtivosData = [];
            excelHeaders = [];

            if (sheetAtivos) {
                const jsonWithHeaders = XLSX.utils.sheet_to_json(sheetAtivos, { defval: "", cellDates: true });
                if (jsonWithHeaders.length > 0) {
                    excelHeaders = Object.keys(jsonWithHeaders[0]);
                    cobrancaColName = excelHeaders[4] || excelHeaders[0];
                    statusColName = excelHeaders[5] || excelHeaders[0];

                    if (!excelHeaders.some(h => h.toUpperCase().includes("ENDEREÇO") || h.toUpperCase().includes("ENDERECO"))) {
                        excelHeaders.push("ENDEREÇO (BD_AUXILIAR)");
                    }

                    jsonWithHeaders.forEach(row => {
                        let ci = "", sigla = "", operadora = "", linha = "", iccid = "";
                        const fullRowFormatted = {};

                        Object.keys(row).forEach(k => {
                            const formattedVal = formatValue(row[k]);
                            fullRowFormatted[k] = formattedVal;
                            const keyUpper = k.toUpperCase();
                            if (keyUpper.includes("CIS") || keyUpper === "CI" || keyUpper.includes("CÓDIGO")) ci = formattedVal;
                            if (keyUpper.includes("SIGLA")) sigla = formattedVal;
                            if (keyUpper.includes("OPERADORA") || keyUpper.includes("PROVEDORA")) operadora = formattedVal;
                            if (keyUpper.includes("LINHA")) linha = formattedVal;
                            if (keyUpper.includes("ICCID")) iccid = formattedVal;
                        });

                        let rawStatusColF = cleanStr(fullRowFormatted[statusColName]);
                        if (!rawStatusColF || rawStatusColF.toUpperCase() === "N/I") rawStatusColF = "N/I";

                        let rawCobrancaColE = cleanStr(fullRowFormatted[cobrancaColName]);
                        if (!rawCobrancaColE || rawCobrancaColE.toUpperCase() === "N/I") rawCobrancaColE = "N/I";

                        const bdInfo = bdAuxiliarMap.get(ci.toUpperCase()) || bdAuxiliarMap.get(sigla.toUpperCase()) || {};

                        fullRowFormatted[statusColName] = rawStatusColF;
                        fullRowFormatted[cobrancaColName] = rawCobrancaColE;
                        fullRowFormatted["ENDEREÇO (BD_AUXILIAR)"] = bdInfo.endereco || fullRowFormatted["ENDEREÇO"] || "-";

                        rawAtivosData.push({
                            ci: ci || bdInfo.ci || "-",
                            sigla: sigla || bdInfo.sigla || "-",
                            linha: linha || "-",
                            iccid: iccid || "-",
                            endereco: fullRowFormatted["ENDEREÇO (BD_AUXILIAR)"],
                            statusColF: rawStatusColF,
                            cobrancaColE: rawCobrancaColE,
                            isCisNumeric: isNumericCIS(ci || bdInfo.ci),
                            originalRow: fullRowFormatted
                        });
                    });
                }
            }

            document.getElementById('dropZone').classList.add('hidden');
            document.getElementById('dashboardSection').classList.remove('hidden');
            document.getElementById('btnExport').disabled = false;
            document.getElementById('btnPdf').disabled = false;

            saveToDatabase();
            renderTableHeaders();
            calculateMetrics();
            filterTable();
        }

        function renderTableHeaders() {
            const headerRow = document.getElementById('tableHeader');
            headerRow.innerHTML = '';
            excelHeaders.forEach(h => {
                const th = document.createElement('th');
                th.className = 'p-3 whitespace-nowrap border-b border-slate-700';
                th.innerText = h.toUpperCase();
                headerRow.appendChild(th);
            });
        }

        function searchBDByCIS() {
            const query = document.getElementById('bdSearchInput').value.trim().toUpperCase();
            const resultBox = document.getElementById('bdSearchResult');
            const tbody = document.getElementById('bdSearchResultBody');

            if (!query) { resultBox.classList.add('hidden'); return; }

            const matches = [];
            for (let [key, val] of bdAuxiliarMap.entries()) {
                if (key.includes(query) || val.nome.toUpperCase().includes(query) || val.endereco.toUpperCase().includes(query)) {
                    if (!matches.some(m => m.ci === val.ci && m.sigla === val.sigla)) matches.push(val);
                }
            }

            tbody.innerHTML = '';
            if (matches.length === 0) {
                tbody.innerHTML = `<tr><td colspan="5" class="p-3 text-center text-slate-400">Nenhum registro encontrado.</td></tr>`;
            } else {
                matches.slice(0, 10).forEach(m => {
                    const tr = document.createElement('tr');
                    tr.innerHTML = `<td class="p-2.5 font-bold">${m.ci || '-'}</td><td class="p-2.5 font-bold text-indigo-600">${m.sigla || '-'}</td><td class="p-2.5">${m.nome || '-'}</td><td class="p-2.5">${m.endereco || '-'}</td><td class="p-2.5">${m.uf || '-'}</td>`;
                    tbody.appendChild(tr);
                });
            }
            resultBox.classList.remove('hidden');
        }

        function setSelectOption(selectEl, targetValue) {
            if (!selectEl) return;
            const target = String(targetValue).trim().toUpperCase();
            let matched = false;
            for (let i = 0; i < selectEl.options.length; i++) {
                if (selectEl.options[i].value.trim().toUpperCase() === target) {
                    selectEl.selectedIndex = i;
                    matched = true;
                    break;
                }
            }
            if (!matched) selectEl.value = targetValue;
        }

        function triggerCardFilter(type, value) {
            document.getElementById('tableSearchInput').value = '';
            const selStatus = document.getElementById('filterStatusColF');
            const selCobranca = document.getElementById('filterCobrancaColE');
            const selCisNum = document.getElementById('filterCisNumNI');

            if (selStatus) selStatus.value = '';
            if (selCobranca) selCobranca.value = '';
            if (selCisNum) selCisNum.value = '';

            if (type === 'STATUS') {
                setSelectOption(selStatus, value);
            } else if (type === 'COBRANÇA') {
                setSelectOption(selCobranca, value);
            } else if (type === 'CIS_NUM_NI') {
                if (selCisNum) selCisNum.value = 'ONLY_NUMERIC_NI';
            }

            filterTable();

            const tableSection = document.getElementById('tableSectionContainer');
            if (tableSection) {
                tableSection.scrollIntoView({ behavior: 'smooth', block: 'start' });
            }
        }

        function calculateMetrics() {
            const total = rawAtivosData.length;
            const statusCounts = {}, cobrancaCounts = {};
            let cisNumCobrancaNI = 0;

            rawAtivosData.forEach(item => {
                statusCounts[item.statusColF] = (statusCounts[item.statusColF] || 0) + 1;
                cobrancaCounts[item.cobrancaColE] = (cobrancaCounts[item.cobrancaColE] || 0) + 1;
                if (item.isCisNumeric && item.cobrancaColE === "N/I") cisNumCobrancaNI++;
            });

            // Populate Status
            const statusContainer = document.getElementById('statusCardsContainer');
            statusContainer.innerHTML = '';
            statusContainer.appendChild(createMetricCard("TOTAL DE ATIVOS", total, "100%", "fa-list-check", () => triggerCardFilter('RESET', '')));

            populateCards(statusContainer, statusCounts, total, 'filterStatusColF', 'STATUS', 'STATUS');

            // Populate Cobrança
            const cobrancaContainer = document.getElementById('cobrancaCardsContainer');
            cobrancaContainer.innerHTML = '';
            cobrancaContainer.appendChild(createMetricCard("TOTAL DE REGISTROS", total, "100%", "fa-receipt", () => triggerCardFilter('RESET', '')));

            const pctCisNI = total ? ((cisNumCobrancaNI / total) * 100).toFixed(1) + "%" : "0.0%";
            const extraCard = createCardElement("CIS NUMÉRICA N/I", cisNumCobrancaNI, pctCisNI, {
                bg: "border-l-amber-500", text: "text-amber-700", badge: "bg-amber-100", icon: "fa-triangle-exclamation"
            }, () => triggerCardFilter('CIS_NUM_NI', 'ONLY_NUMERIC_NI'));
            cobrancaContainer.appendChild(extraCard);

            populateCards(cobrancaContainer, cobrancaCounts, total, 'filterCobrancaColE', 'COBRANÇA', 'COBRANÇA');

            renderCharts(statusCounts, cobrancaCounts);
        }

        function populateCards(container, countsObj, total, selectId, typeLabel, selectHeader) {
            const selectEl = document.getElementById(selectId);
            selectEl.innerHTML = `<option value="">${selectHeader}: Todos</option>`;

            const colorPalette = [
                { bg: "border-l-sky-500", text: "text-sky-600", badge: "bg-sky-100", icon: "fa-magnifying-glass-chart" },
                { bg: "border-l-emerald-500", text: "text-emerald-600", badge: "bg-emerald-100", icon: "fa-circle-check" },
                { bg: "border-l-amber-500", text: "text-amber-600", badge: "bg-amber-100", icon: "fa-triangle-exclamation" },
                { bg: "border-l-indigo-500", text: "text-indigo-600", badge: "bg-indigo-100", icon: "fa-sliders" }
            ];

            let idx = 0;
            Object.keys(countsObj).sort().forEach(key => {
                const count = countsObj[key];
                const pct = total ? ((count / total) * 100).toFixed(1) + "%" : "0.0%";
                let palette = colorPalette[idx % colorPalette.length];

                const card = createCardElement(key, count, pct, palette, () => triggerCardFilter(typeLabel, key));
                container.appendChild(card);
                idx++;

                const opt = document.createElement('option');
                opt.value = key;
                opt.textContent = `${key} (${count})`;
                selectEl.appendChild(opt);
            });
        }

        function createMetricCard(title, count, pct, iconClass, onClickHandler) {
            const div = document.createElement('div');
            div.className = `bg-slate-50 p-4 rounded-xl shadow-sm border border-slate-200 cursor-pointer hover:shadow-md hover:border-slate-400 transition-all active:scale-95 group`;
            div.onclick = onClickHandler;
            div.innerHTML = `
                <div class="flex items-center justify-between pointer-events-none">
                    <div>
                        <p class="text-[11px] font-semibold text-slate-400 uppercase tracking-wide group-hover:text-slate-600">${title}</p>
                        <h3 class="text-2xl font-bold text-slate-800 mt-1">${count}</h3>
                    </div>
                    <div class="bg-slate-200 p-3 rounded-lg text-slate-700 group-hover:bg-slate-300 transition">
                        <i class="fa-solid ${iconClass} text-lg"></i>
                    </div>
                </div>
                <p class="text-xs font-bold text-slate-500 mt-2 pointer-events-none">${pct} do total <span class="text-[10px] font-semibold text-indigo-600 ml-1">(Exibir todos)</span></p>
            `;
            return div;
        }

        function createCardElement(title, count, pct, palette, onClickHandler) {
            const div = document.createElement('div');
            div.className = `bg-white p-4 rounded-xl shadow-sm border border-slate-200 border-l-4 ${palette.bg} cursor-pointer hover:shadow-md hover:scale-[1.02] transition-all active:scale-95 group`;
            div.onclick = onClickHandler;
            div.innerHTML = `
                <div class="flex items-center justify-between pointer-events-none">
                    <div>
                        <p class="text-[11px] font-bold ${palette.text} uppercase tracking-wide truncate max-w-[130px]" title="${title}">${title}</p>
                        <h3 class="text-2xl font-bold ${palette.text} mt-1">${count}</h3>
                    </div>
                    <div class="${palette.badge} p-3 rounded-lg ${palette.text} group-hover:scale-110 transition">
                        <i class="fa-solid ${palette.icon || 'fa-tag'} text-lg"></i>
                    </div>
                </div>
                <p class="text-xs font-bold ${palette.text} mt-2 pointer-events-none">${pct} do total <i class="fa-solid fa-arrow-down text-[10px] ml-1"></i></p>
            `;
            return div;
        }

        function renderCharts(statusCountsObj, cobrancaCountsObj) {
            if (chartStatusObj) chartStatusObj.destroy();
            const statusLabels = Object.keys(statusCountsObj);
            const statusData = Object.values(statusCountsObj);
            const colors = ['#0284c7', '#10b981', '#f59e0b', '#6366f1', '#a855f7', '#f43f5e', '#64748b'];

            chartStatusObj = new Chart(document.getElementById('chartStatus'), {
                type: 'doughnut',
                data: {
                    labels: statusLabels.length ? statusLabels : ['Sem dados'],
                    datasets: [{
                        data: statusData.length ? statusData : [0],
                        backgroundColor: colors.slice(0, Math.max(statusLabels.length, 1))
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'bottom' } } }
            });

            if (chartCobrancaObj) chartCobrancaObj.destroy();
            const cobrancaLabels = Object.keys(cobrancaCountsObj);
            const cobrancaData = Object.values(cobrancaCountsObj);

            chartCobrancaObj = new Chart(document.getElementById('chartCobranca'), {
                type: 'bar',
                data: {
                    labels: cobrancaLabels.length ? cobrancaLabels : ['Sem dados'],
                    datasets: [{
                        label: 'Qtd Registros',
                        data: cobrancaData.length ? cobrancaData : [0],
                        backgroundColor: '#10b981'
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } } }
            });
        }

        function filterTable() {
            const q = document.getElementById('tableSearchInput').value.toLowerCase();
            const selectedStatus = document.getElementById('filterStatusColF').value;
            const selectedCobranca = document.getElementById('filterCobrancaColE').value;
            const onlyNumericNI = document.getElementById('filterCisNumNI').value;

            const filtered = rawAtivosData.filter(item => {
                const allValues = Object.values(item.originalRow).map(v => String(v).toLowerCase()).join(' ');
                const matchQuery = !q || allValues.includes(q);
                const matchStatus = !selectedStatus || item.statusColF.toUpperCase() === selectedStatus.toUpperCase();
                const matchCobranca = !selectedCobranca || item.cobrancaColE.toUpperCase() === selectedCobranca.toUpperCase();
                const matchCisNumNI = !onlyNumericNI || (item.isCisNumeric && item.cobrancaColE === "N/I");

                return matchQuery && matchStatus && matchCobranca && matchCisNumNI;
            });

            renderTableBody(filtered);
        }

        function renderTableBody(data) {
            const tbody = document.getElementById('ativosTableBody');
            tbody.innerHTML = '';

            data.forEach(item => {
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 transition border-b border-slate-100';

                excelHeaders.forEach(header => {
                    const td = document.createElement('td');
                    td.className = 'p-3 whitespace-nowrap font-medium';
                    td.innerText = item.originalRow[header] || "-";
                    tr.appendChild(td);
                });

                tbody.appendChild(tr);
            });

            document.getElementById('displayedCount').innerText = data.length;
            document.getElementById('totalCount').innerText = rawAtivosData.length;
        }

        function resetFilters() {
            document.getElementById('tableSearchInput').value = '';
            document.getElementById('filterStatusColF').value = '';
            document.getElementById('filterCobrancaColE').value = '';
            document.getElementById('filterCisNumNI').value = '';
            filterTable();
        }

        function exportToPDF() {
            const btnPdf = document.getElementById('btnPdf');
            const originalText = btnPdf.innerHTML;
            btnPdf.innerHTML = '<i class="fa-solid fa-spinner fa-spin text-base"></i> Gerando PDF...';
            btnPdf.disabled = true;

            window.scrollTo(0, 0);

            const element = document.getElementById('pdfContent');
            const opt = {
                margin:       [0.2, 0.2, 0.2, 0.2],
                filename:     'Relatorio_Painel_Links_GF.pdf',
                image:        { type: 'jpeg', quality: 0.98 },
                html2canvas:  { scale: 2, useCORS: true, logging: false, scrollX: 0, scrollY: 0 },
                jsPDF:        { unit: 'in', format: 'a3', orientation: 'landscape' }
            };

            if (typeof html2pdf !== 'undefined') {
                html2pdf().set(opt).from(element).save().then(() => {
                    btnPdf.innerHTML = originalText;
                    btnPdf.disabled = false;
                }).catch(err => {
                    btnPdf.innerHTML = originalText;
                    btnPdf.disabled = false;
                    window.print();
                });
            } else {
                btnPdf.innerHTML = originalText;
                btnPdf.disabled = false;
                window.print();
            }
        }

        function exportToExcel() {
            if (!rawAtivosData.length) return;
            const exportRows = rawAtivosData.map(item => item.originalRow);
            const ws = XLSX.utils.json_to_sheet(exportRows);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Painel Links GF");
            XLSX.writeFile(wb, "Relatorio_Painel_Links_GF.xlsx");
        }
    </script>
</body>
</html>
