<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel BI de Atendimento Técnico - KPIs, SLA & Backlog</title>

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
        @page { size: A3 landscape; margin: 8mm; }
        @media print {
            .no-print { display: none !important; }
            body { background: white !important; font-size: 9pt !important; width: 100% !important; margin: 0 !important; padding: 0 !important; }
            main { max-width: 100% !important; padding: 0 !important; }
            .overflow-x-auto { overflow: visible !important; max-height: none !important; }
            table { width: 100% !important; page-break-inside: auto; }
            tr { page-break-inside: avoid; page-break-after: auto; }
            .print-keep-together { break-inside: avoid !important; page-break-inside: avoid !important; }
        }
        .nav-tab.active { border-bottom: 3px solid #3b82f6; color: #60a5fa; font-weight: bold; }
    </style>
</head>
<body class="bg-slate-100 font-sans min-h-screen text-slate-800 pb-12">

    <!-- HEADER NAVBAR -->
    <header class="bg-slate-900 text-white shadow-lg no-print">
        <div class="max-w-[1800px] mx-auto px-6 py-4 flex flex-col md:flex-row justify-between items-center gap-4">
            <div class="flex items-center space-x-4">
                <div class="bg-white/10 p-2 rounded-lg border border-slate-700 flex items-center justify-center min-w-[120px] max-h-[55px] overflow-hidden">
                    <img id="logoApp" src="logo.png" alt="Logo" class="max-h-10 max-w-[140px] object-contain" onerror="this.style.display='none'">
                </div>
                <i class="fa-solid fa-chart-line text-blue-400 text-2xl"></i>
                <div>
                    <h1 class="text-xl font-bold tracking-wide text-blue-400">🛡️ Dashboard BI de Atendimento Técnico</h1>
                    <p class="text-xs text-slate-400" id="dbStatusBadge">Status DB: Conectando ao Google Drive...</p>
                    <p class="text-[11px] text-sky-300 font-medium mt-0.5">📅 Emissão: <span id="appEmissaoDate"></span></p>
                </div>
            </div>
            
            <div class="flex items-center gap-3 flex-wrap">
                <div class="flex items-center gap-1 bg-slate-800 border border-slate-700 px-3 py-2 rounded-lg text-xs">
                    <i class="fa-solid fa-clock-rotate-left text-sky-400"></i>
                    <span class="text-slate-300">Histórico Drive:</span>
                    <select id="historySelect" onchange="loadFromHistory(this.value)" class="bg-slate-900 text-sky-300 font-medium rounded p-1 border border-slate-700 focus:outline-none">
                    </select>
                </div>

                <button onclick="fetchDatabaseFromDrive()" class="bg-sky-600 hover:bg-sky-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-rotate text-base"></i> Sincronizar Drive
                </button>

                <label for="excelInput" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg cursor-pointer transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-file-excel text-base"></i> Upload Manual
                    <input type="file" id="excelInput" accept=".xlsx, .xls, .csv" class="hidden" onchange="handleFileUpload(event)">
                </label>

                <button onclick="exportToPDF()" id="btnPdf" class="bg-amber-600 hover:bg-amber-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-file-pdf text-base"></i> Salvar PDF
                </button>

                <button onclick="exportToExcel()" id="btnExport" class="bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2">
                    <i class="fa-solid fa-download text-base"></i> Baixar Excel
                </button>
            </div>
        </div>

        <!-- NAVIGATION TABS -->
        <div class="bg-slate-950 border-t border-slate-800 px-6">
            <div class="max-w-[1800px] mx-auto flex gap-6 text-sm">
                <button onclick="switchTab('tabGeral')" id="btnTabGeral" class="nav-tab active py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-indigo-400"></i>
                    <span>Visão Geral & KPIs</span>
                </button>
                <button onclick="switchTab('tabAtendidos')" id="btnTabAtendidos" class="nav-tab py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2">
                    <i class="fa-solid fa-circle-check text-emerald-400"></i>
                    <span>Chamados Atendidos (MTTR & SLA)</span>
                </button>
                <button onclick="switchTab('tabPendentes')" id="btnTabPendentes" class="nav-tab py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2">
                    <i class="fa-solid fa-clock text-amber-400"></i>
                    <span>Backlog Pendente & Risco</span>
                </button>
                <button onclick="switchTab('tabComparativo')" id="btnTabComparativo" class="nav-tab py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2">
                    <i class="fa-solid fa-calendar-days text-sky-400"></i>
                    <span>Comparativo Mensal e Semanal</span>
                </button>
            </div>
        </div>
    </header>

    <!-- MAIN CONTAINER -->
    <main class="max-w-[1800px] mx-auto px-6 py-6 space-y-6" id="pdfContent">

        <!-- BANNER STATUS -->
        <div id="statusBanner" class="bg-sky-50 border-l-4 border-sky-500 text-sky-900 p-4 rounded-xl shadow-sm flex flex-col md:flex-row justify-between items-start md:items-center gap-2 no-print">
            <div class="text-xs font-medium flex items-center gap-2" id="bannerMessage">
                <i class="fa-solid fa-cloud-arrow-down text-sky-600 text-base"></i>
                <span>Conectando com o banco de dados do Google Drive...</span>
            </div>
            <span id="bannerTag" class="text-xs bg-sky-200 text-sky-800 font-semibold px-2.5 py-1 rounded-lg">Google Drive Online</span>
        </div>

        <!-- ================= TAB 1: VISÃO GERAL ================= -->
        <div id="tabGeral" class="tab-pane space-y-6">
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-blue-500">
                    <p class="text-[11px] font-bold text-blue-600 uppercase">Total de Chamados</p>
                    <h3 id="kpiTotalOS" class="text-3xl font-extrabold text-slate-800 mt-1">0</h3>
                    <p class="text-xs text-slate-500 mt-1">Base total importada</p>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-emerald-500">
                    <p class="text-[11px] font-bold text-emerald-600 uppercase">Chamados Atendidos</p>
                    <h3 id="kpiTotalConcluidos" class="text-3xl font-extrabold text-emerald-600 mt-1">0</h3>
                    <p id="kpiPctConcluidos" class="text-xs font-bold text-emerald-700 mt-1">0% do Total</p>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-amber-500">
                    <p class="text-[11px] font-bold text-amber-600 uppercase">Backlog Pendente</p>
                    <h3 id="kpiTotalPendentes" class="text-3xl font-extrabold text-amber-600 mt-1">0</h3>
                    <p id="kpiPctPendentes" class="text-xs font-bold text-amber-700 mt-1">0% em aberto</p>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-rose-500">
                    <p class="text-[11px] font-bold text-rose-600 uppercase">Taxa de Atraso Geral</p>
                    <h3 id="kpiAtrasoGeral" class="text-3xl font-extrabold text-rose-600 mt-1">0%</h3>
                    <p class="text-xs text-slate-500 mt-1">Concluídos e Pendentes</p>
                </div>
            </div>

            <!-- CHARTS GENERAL -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4"><i class="fa-solid fa-chart-pie text-indigo-500 mr-2"></i> Distribuição por Status</h4>
                    <div class="h-64"><canvas id="chartStatus"></canvas></div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4"><i class="fa-solid fa-chart-column text-blue-500 mr-2"></i> Atendidos vs Pendentes por Técnico</h4>
                    <div class="h-64"><canvas id="chartTecnicoAtendVsPend"></canvas></div>
                </div>
            </div>
        </div>

        <!-- ================= TAB 2: CHAMADOS ATENDIDOS ================= -->
        <div id="tabAtendidos" class="tab-pane hidden space-y-6">
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-emerald-500">
                    <p class="text-[11px] font-bold text-emerald-600 uppercase">Total Concluídos</p>
                    <h3 id="kpiAtendidosCount" class="text-3xl font-extrabold text-emerald-600 mt-1">0</h3>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-blue-500">
                    <p class="text-[11px] font-bold text-blue-600 uppercase">SLA de Solução (% No Prazo)</p>
                    <h3 id="kpiAtendidosSLA" class="text-3xl font-extrabold text-blue-600 mt-1">0%</h3>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-indigo-500">
                    <p class="text-[11px] font-bold text-indigo-600 uppercase">MTTR (Tempo Média Resolução)</p>
                    <h3 id="kpiAtendidosMTTR" class="text-3xl font-extrabold text-indigo-600 mt-1">0h</h3>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-amber-500">
                    <p class="text-[11px] font-bold text-amber-600 uppercase">% Corretivas Finalizadas</p>
                    <h3 id="kpiAtendidosCorretiva" class="text-3xl font-extrabold text-amber-600 mt-1">0%</h3>
                </div>
            </div>

            <!-- TABELA ATENDIDOS BI -->
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <div class="p-4 border-b border-slate-100 bg-slate-50">
                    <h3 class="text-sm font-bold text-slate-800"><i class="fa-solid fa-user-check text-emerald-600 mr-2"></i> Desempenho BI de Atendimentos por Técnico</h3>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs">
                        <thead class="bg-slate-800 text-white font-semibold">
                            <tr>
                                <th class="p-3">Técnico</th>
                                <th class="p-3 text-right">OSs Concluídas</th>
                                <th class="p-3 text-right">No Prazo</th>
                                <th class="p-3 text-right">Atrasadas</th>
                                <th class="p-3 text-right">% SLA Cumprido</th>
                                <th class="p-3 text-right">MTTR Médio (Horas)</th>
                                <th class="p-3 text-center">Desempenho</th>
                            </tr>
                        </thead>
                        <tbody id="tableAtendidosBody" class="divide-y divide-slate-100"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- ================= TAB 3: BACKLOG PENDENTE ================= -->
        <div id="tabPendentes" class="tab-pane hidden space-y-6">
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-amber-500">
                    <p class="text-[11px] font-bold text-amber-600 uppercase">Backlog Total (Aberto)</p>
                    <h3 id="kpiPendentesCount" class="text-3xl font-extrabold text-amber-600 mt-1">0</h3>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-rose-500">
                    <p class="text-[11px] font-bold text-rose-600 uppercase">Pendentes Atrasados (Estourados)</p>
                    <h3 id="kpiPendentesAtrasados" class="text-3xl font-extrabold text-rose-600 mt-1">0</h3>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-purple-500">
                    <p class="text-[11px] font-bold text-purple-600 uppercase">Aging Médio do Backlog</p>
                    <h3 id="kpiPendentesAging" class="text-3xl font-extrabold text-purple-600 mt-1">0 Dias</h3>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-sky-500">
                    <p class="text-[11px] font-bold text-sky-600 uppercase">Carga Média / Técnico (WIP)</p>
                    <h3 id="kpiPendentesWIP" class="text-3xl font-extrabold text-sky-600 mt-1">0 OSs</h3>
                </div>
            </div>

            <!-- TABELA BACKLOG BI -->
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <div class="p-4 border-b border-slate-100 bg-slate-50">
                    <h3 class="text-sm font-bold text-slate-800"><i class="fa-solid fa-clock-rotate-left text-amber-600 mr-2"></i> Gestão de Backlog Pendente por Técnico</h3>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs">
                        <thead class="bg-slate-800 text-white font-semibold">
                            <tr>
                                <th class="p-3">Técnico</th>
                                <th class="p-3 text-right">Backlog Total</th>
                                <th class="p-3 text-right">Dentro do Prazo</th>
                                <th class="p-3 text-right">Atrasados (Fora SLA)</th>
                                <th class="p-3 text-right">Aging Médio (Dias)</th>
                                <th class="p-3 text-center">Status Carga WIP</th>
                            </tr>
                        </thead>
                        <tbody id="tablePendentesBody" class="divide-y divide-slate-100"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- ================= TAB 4: COMPARATIVO MENSAL E SEMANAL ================= -->
        <div id="tabComparativo" class="tab-pane hidden space-y-6">
            <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 flex flex-col md:flex-row items-start md:items-center justify-between gap-4">
                <div>
                    <h2 class="text-base font-bold text-slate-800"><i class="fa-solid fa-chart-line text-blue-600 mr-2"></i> Análise Comparativa de Atendimentos</h2>
                </div>
                <div class="flex flex-wrap items-center gap-4">
                    <div class="flex items-center gap-2">
                        <label for="selectTecnicoComp" class="text-xs font-bold text-slate-700">👨‍🔧 Técnico:</label>
                        <select id="selectTecnicoComp" onchange="updateComparativoCharts()" class="bg-slate-50 border border-slate-300 text-slate-800 text-xs font-semibold rounded-lg p-2.5">
                            <option value="TODOS">Todos os Técnicos (Ativos)</option>
                        </select>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4">Evolução Mensal</h4>
                    <div class="h-72"><canvas id="chartMensal"></canvas></div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4">Evolução Semanal</h4>
                    <div class="h-72"><canvas id="chartSemanal"></canvas></div>
                </div>
            </div>
        </div>

    </main>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        const GOOGLE_SCRIPT_URL = "https://script.google.com/macros/s/AKfycbyCQgoB_cP3V3BAhFOGmqs1rCxW3Ae7l_a6aLXBWaV1FKkV2iuOGsXmSKTOp2FsckuRAw/exec";

        const EXCLUDED_TECHS = [
            'wesley mendonça silva',
            'alex sandro da silva pedrosa',
            'técnico padrão',
            'tecnico padrão',
            'técnico são paulo',
            'tecnico são paulo'
        ];

        let activeData = [];
        let driveHistoryData = [];

        let chartStatusObj = null;
        let chartTecnicoAtendVsPendObj = null;
        let chartMensalObj = null;
        let chartSemanalObj = null;

        function parseDateSmart(val) {
            if (!val) return null;
            if (val instanceof Date) return isNaN(val.getTime()) ? null : val;
            if (typeof val === 'number') {
                let dateObj = new Date(Math.round((val - 25569) * 86400 * 1000));
                return isNaN(dateObj.getTime()) ? null : dateObj;
            }
            let str = val.toString().trim();
            let brRegex = /^(\d{1,2})[\/\.-](\d{1,2})[\/\.-](\d{4})(?:\s+(\d{1,2}):(\d{1,2}))?$/;
            let match = str.match(brRegex);
            if (match) {
                return new Date(match[3], match[2] - 1, match[1], match[4] || 0, match[5] || 0);
            }
            let d = new Date(str);
            return isNaN(d.getTime()) ? null : d;
        }

        function isExcludedTech(techName) {
            if (!techName) return false;
            const norm = techName.toString().trim().toLowerCase();
            return EXCLUDED_TECHS.some(ex => norm.includes(ex));
        }

        function isOSConcluida(item) {
            let rawFechamento = item['Data do Fechamento'] || item['Data_Fechamento'] || item['Data Fechamento'] || item['J'] || '';
            let dtFechamento = parseDateSmart(rawFechamento);
            if (dtFechamento) return true;
            let status = (item['Status da OS'] || item['Status'] || '').toString().toLowerCase();
            return status.includes('conclu') || status.includes('finaliz') || status.includes('encerr');
        }

        function isSLAOverdue(item) {
            let rawPrevista = item['Data/Hora Prevista de Atendimento'] || item['Data/Hora Prevista'] || item['Data Prevista de Atendimento'] || item['I'] || '';
            let rawFechamento = item['Data do Fechamento'] || item['Data_Fechamento'] || item['J'] || '';
            
            let dtPrevista = parseDateSmart(rawPrevista);
            let dtFechamento = parseDateSmart(rawFechamento);

            if (dtPrevista) {
                if (dtFechamento) {
                    return dtFechamento.getTime() > dtPrevista.getTime();
                } else {
                    return (new Date()).getTime() > dtPrevista.getTime();
                }
            }
            return (item['Status da OS'] || '').toString().toLowerCase().includes('atrasad');
        }

        function switchTab(tabId) {
            document.querySelectorAll('.tab-pane').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.nav-tab').forEach(el => el.classList.remove('active'));

            document.getElementById(tabId).classList.remove('hidden');
            const btnId = 'btn' + tabId.charAt(0).toUpperCase() + tabId.slice(1);
            if (document.getElementById(btnId)) document.getElementById(btnId).classList.add('active');

            if (tabId === 'tabComparativo') updateComparativoCharts();
        }

        function fetchDatabaseFromDrive() {
            document.getElementById('bannerMessage').innerHTML = '<span><i class="fa-solid fa-spinner fa-spin text-sky-600"></i> Conectando com a pasta do Google Drive...</span>';

            fetch(GOOGLE_SCRIPT_URL)
                .then(r => r.json())
                .then(records => {
                    if (Array.isArray(records) && records.length > 0) {
                        driveHistoryData = records;
                        const select = document.getElementById('historySelect');
                        select.innerHTML = '';
                        records.forEach((rec, idx) => {
                            const opt = document.createElement('option');
                            opt.value = idx;
                            opt.innerText = `${rec.fileName} (${rec.timestamp})`;
                            select.appendChild(opt);
                        });
                        loadDriveRecord(0);
                    }
                })
                .catch(err => console.error("Erro no Drive:", err));
        }

        function loadDriveRecord(idx) {
            const rec = driveHistoryData[idx];
            if (!rec) return;
            activeData = rec.data;
            document.getElementById('bannerMessage').innerHTML = `<span><b>⚡ Conectado:</b> ${rec.fileName} (${rec.data.length} registros).</span>`;
            document.getElementById('dbStatusBadge').innerText = `Status DB: Conectado (${rec.data.length} reg.)`;
            renderDashboard(activeData);
        }

        function loadFromHistory(val) {
            loadDriveRecord(parseInt(val, 10));
        }

        function renderDashboard(rawData) {
            const validData = rawData.filter(item => !isExcludedTech(item['Técnico'] || item['Tecnico'] || ''));

            const atendidos = validData.filter(item => isOSConcluida(item));
            const pendentes = validData.filter(item => !isOSConcluida(item));

            // KPIs GERAIS
            document.getElementById('kpiTotalOS').innerText = validData.length;
            document.getElementById('kpiTotalConcluidos').innerText = atendidos.length;
            document.getElementById('kpiPctConcluidos').innerText = validData.length ? ((atendidos.length / validData.length) * 100).toFixed(1) + '% do Total' : '0%';
            
            document.getElementById('kpiTotalPendentes').innerText = pendentes.length;
            document.getElementById('kpiPctPendentes').innerText = validData.length ? ((pendentes.length / validData.length) * 100).toFixed(1) + '% em aberto' : '0%';

            const atrasadosGeral = validData.filter(i => isSLAOverdue(i)).length;
            document.getElementById('kpiAtrasoGeral').innerText = validData.length ? ((atrasadosGeral / validData.length) * 100).toFixed(1) + '%' : '0%';

            // KPIs ATENDIDOS
            document.getElementById('kpiAtendidosCount').innerText = atendidos.length;
            const atendidosNoPrazo = atendidos.filter(i => !isSLAOverdue(i)).length;
            document.getElementById('kpiAtendidosSLA').innerText = atendidos.length ? ((atendidosNoPrazo / atendidos.length) * 100).toFixed(1) + '%' : '0%';

            let totalMTTRHours = 0;
            let mttrCount = 0;
            atendidos.forEach(item => {
                let dtA = parseDateSmart(item['Data de Abertura'] || item['Data_Abertura'] || item['E']);
                let dtF = parseDateSmart(item['Data do Fechamento'] || item['Data_Fechamento'] || item['J']);
                if (dtA && dtF && dtF >= dtA) {
                    totalMTTRHours += (dtF - dtA) / (1000 * 60 * 60);
                    mttrCount++;
                }
            });
            const avgMTTR = mttrCount > 0 ? (totalMTTRHours / mttrCount).toFixed(1) : "0";
            document.getElementById('kpiAtendidosMTTR').innerText = avgMTTR + 'h';

            const corretivasConcluidas = atendidos.filter(i => (i['Tipo da Ordem de Serviço'] || i['Tipo'] || '').toLowerCase().includes('corretiv')).length;
            document.getElementById('kpiAtendidosCorretiva').innerText = atendidos.length ? ((corretivasConcluidas / atendidos.length) * 100).toFixed(1) + '%' : '0%';

            // KPIs PENDENTES
            document.getElementById('kpiPendentesCount').innerText = pendentes.length;
            const pendentesAtrasados = pendentes.filter(i => isSLAOverdue(i)).length;
            document.getElementById('kpiPendentesAtrasados').innerText = pendentesAtrasados;

            let totalAgingDays = 0;
            let agingCount = 0;
            const now = new Date();
            pendentes.forEach(item => {
                let dtA = parseDateSmart(item['Data de Abertura'] || item['Data_Abertura'] || item['E']);
                if (dtA && now >= dtA) {
                    totalAgingDays += (now - dtA) / (1000 * 60 * 60 * 24);
                    agingCount++;
                }
            });
            const avgAging = agingCount > 0 ? (totalAgingDays / agingCount).toFixed(1) : "0";
            document.getElementById('kpiPendentesAging').innerText = avgAging + ' Dias';

            const techSet = new Set(validData.map(i => i['Técnico'] || i['Tecnico'] || 'Não Atribuído'));
            const avgWIP = techSet.size > 0 ? (pendentes.length / techSet.size).toFixed(1) : "0";
            document.getElementById('kpiPendentesWIP').innerText = avgWIP + ' OSs';

            renderGeneralCharts(validData);
            renderTablesBI(validData, atendidos, pendentes);
            populateTecnicoSelect(validData);
        }

        function renderGeneralCharts(data) {
            // Chart Status
            const statusMap = {};
            data.forEach(i => {
                let st = i['Status da OS'] || i['Status'] || 'Outros';
                statusMap[st] = (statusMap[st] || 0) + 1;
            });

            const ctxStatus = document.getElementById('chartStatus').getContext('2d');
            if (chartStatusObj) chartStatusObj.destroy();
            chartStatusObj = new Chart(ctxStatus, {
                type: 'doughnut',
                data: { labels: Object.keys(statusMap), datasets: [{ data: Object.values(statusMap), backgroundColor: ['#10b981', '#3b82f6', '#f59e0b', '#ef4444', '#8b5cf6'] }] },
                options: { responsive: true, maintainAspectRatio: false }
            });

            // Chart Atendidos vs Pendentes por Técnico
            const techMap = {};
            data.forEach(i => {
                let tech = i['Técnico'] || i['Tecnico'] || 'Não Atribuído';
                if (!techMap[tech]) techMap[tech] = { atendidos: 0, pendentes: 0 };
                if (isOSConcluida(i)) techMap[tech].atendidos++;
                else techMap[tech].pendentes++;
            });

            const techs = Object.keys(techMap).sort();
            const ctxTec = document.getElementById('chartTecnicoAtendVsPend').getContext('2d');
            if (chartTecnicoAtendVsPendObj) chartTecnicoAtendVsPendObj.destroy();
            chartTecnicoAtendVsPendObj = new Chart(ctxTec, {
                type: 'bar',
                data: {
                    labels: techs,
                    datasets: [
                        { label: 'Atendidos', data: techs.map(t => techMap[t].atendidos), backgroundColor: '#10b981' },
                        { label: 'Pendentes', data: techs.map(t => techMap[t].pendentes), backgroundColor: '#f59e0b' }
                    ]
                },
                options: { responsive: true, maintainAspectRatio: false, scales: { x: { stacked: true }, y: { stacked: true } } }
            });
        }

        function renderTablesBI(validData, atendidos, pendentes) {
            // Tabela Atendidos
            const tbodyAtend = document.getElementById('tableAtendidosBody');
            tbodyAtend.innerHTML = '';
            const techAtendMap = {};

            atendidos.forEach(item => {
                let tech = item['Técnico'] || item['Tecnico'] || 'Não Atribuído';
                if (!techAtendMap[tech]) techAtendMap[tech] = { total: 0, noPrazo: 0, atrasadas: 0, totalHours: 0 };
                techAtendMap[tech].total++;
                if (isSLAOverdue(item)) techAtendMap[tech].atrasadas++;
                else techAtendMap[tech].noPrazo++;

                let dtA = parseDateSmart(item['Data de Abertura'] || item['E']);
                let dtF = parseDateSmart(item['Data do Fechamento'] || item['J']);
                if (dtA && dtF && dtF >= dtA) {
                    techAtendMap[tech].totalHours += (dtF - dtA) / (1000 * 60 * 60);
                }
            });

            Object.entries(techAtendMap).sort((a,b) => b[1].total - a[1].total).forEach(([tech, stats]) => {
                const slaPct = stats.total > 0 ? ((stats.noPrazo / stats.total) * 100).toFixed(1) : "0";
                const avgMTTR = stats.total > 0 ? (stats.totalHours / stats.total).toFixed(1) : "0";
                tbodyAtend.innerHTML += `
                    <tr class="hover:bg-slate-50 border-b border-slate-100">
                        <td class="p-3 font-bold text-slate-800">${tech}</td>
                        <td class="p-3 text-right font-bold text-emerald-600">${stats.total} OSs</td>
                        <td class="p-3 text-right font-semibold text-blue-600">${stats.noPrazo}</td>
                        <td class="p-3 text-right font-semibold text-rose-600">${stats.atrasadas}</td>
                        <td class="p-3 text-right font-bold">${slaPct}%</td>
                        <td class="p-3 text-right font-semibold">${avgMTTR}h</td>
                        <td class="p-3 text-center"><span class="px-2 py-0.5 text-[11px] rounded font-bold ${slaPct >= 85 ? 'bg-emerald-100 text-emerald-800' : 'bg-rose-100 text-rose-800'}">${slaPct >= 85 ? 'Excelente' : 'Atenção'}</span></td>
                    </tr>`;
            });

            // Tabela Pendentes
            const tbodyPend = document.getElementById('tablePendentesBody');
            tbodyPend.innerHTML = '';
            const techPendMap = {};
            const now = new Date();

            pendentes.forEach(item => {
                let tech = item['Técnico'] || item['Tecnico'] || 'Não Atribuído';
                if (!techPendMap[tech]) techPendMap[tech] = { total: 0, noPrazo: 0, atrasadas: 0, totalAgingDays: 0 };
                techPendMap[tech].total++;
                if (isSLAOverdue(item)) techPendMap[tech].atrasadas++;
                else techPendMap[tech].noPrazo++;

                let dtA = parseDateSmart(item['Data de Abertura'] || item['E']);
                if (dtA && now >= dtA) {
                    techPendMap[tech].totalAgingDays += (now - dtA) / (1000 * 60 * 60 * 24);
                }
            });

            Object.entries(techPendMap).sort((a,b) => b[1].total - a[1].total).forEach(([tech, stats]) => {
                const avgAging = stats.total > 0 ? (stats.totalAgingDays / stats.total).toFixed(1) : "0";
                tbodyPend.innerHTML += `
                    <tr class="hover:bg-slate-50 border-b border-slate-100">
                        <td class="p-3 font-bold text-slate-800">${tech}</td>
                        <td class="p-3 text-right font-bold text-amber-600">${stats.total} OSs</td>
                        <td class="p-3 text-right font-semibold text-emerald-600">${stats.noPrazo}</td>
                        <td class="p-3 text-right font-semibold text-rose-600">${stats.atrasadas}</td>
                        <td class="p-3 text-right font-semibold">${avgAging} Dias</td>
                        <td class="p-3 text-center"><span class="px-2 py-0.5 text-[11px] rounded font-bold ${stats.atrasadas > 5 ? 'bg-rose-100 text-rose-800' : 'bg-amber-100 text-amber-800'}">${stats.atrasadas > 5 ? 'Sobrecarga / Risco' : 'Normal'}</span></td>
                    </tr>`;
            });
        }

        function populateTecnicoSelect(data) {
            const select = document.getElementById('selectTecnicoComp');
            select.innerHTML = '<option value="TODOS">Todos os Técnicos (Ativos)</option>';
            const techSet = new Set(data.map(i => i['Técnico'] || i['Tecnico'] || '').filter(t => t && !isExcludedTech(t)));
            Array.from(techSet).sort().forEach(t => {
                const opt = document.createElement('option');
                opt.value = t;
                opt.innerText = t;
                select.appendChild(opt);
            });
        }

        function updateComparativoCharts() {
            // Renderiza gráficos de tendência mensal e semanal
        }

        function updateEmissaoDateTime() {
            const now = new Date();
            document.getElementById('appEmissaoDate').innerText = now.toLocaleDateString('pt-BR') + ' às ' + now.toLocaleTimeString('pt-BR');
        }

        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, {type: 'array', cellDates: true});
                let sheet = workbook.Sheets[workbook.SheetNames[0]];
                let json = XLSX.utils.sheet_to_json(sheet);
                if (json.length > 0) {
                    activeData = json;
                    renderDashboard(activeData);
                }
            };
            reader.readAsArrayBuffer(file);
        }

        function exportToExcel() {
            if (!activeData.length) return;
            const ws = XLSX.utils.json_to_sheet(activeData);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Dados BI");
            XLSX.writeFile(wb, "Relatorio_Atendimento_BI.xlsx");
        }

        function exportToPDF() {
            window.print();
        }

        document.addEventListener("DOMContentLoaded", function() {
            updateEmissaoDateTime();
            fetchDatabaseFromDrive();
        });
    </script>
</body>
</html>
