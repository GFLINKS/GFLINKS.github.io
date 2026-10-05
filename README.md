<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel de Atendimento Técnico - KPIs & O.S Abertas</title>

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
            .print-keep-together {
                break-inside: avoid !important;
                page-break-inside: avoid !important;
            }
        }

        .nav-tab.active {
            border-bottom: 3px solid #3b82f6;
            color: #60a5fa;
            font-weight: bold;
        }

        .card-active {
            ring: 2px solid;
            transform: scale(1.02);
        }
    </style>
</head>
<body class="bg-slate-100 font-sans min-h-screen text-slate-800 pb-12">

    <!-- MODAL DE OBSERVAÇÃO / TEXTO LONGO -->
    <div id="obsModal" class="fixed inset-0 bg-slate-900/80 z-[70] hidden flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-2xl shadow-2xl p-6 max-w-lg w-full space-y-4 border border-slate-200">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 class="text-base font-bold text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-pen-to-square text-indigo-600"></i>
                    <span id="obsModalTitle">Visualizar / Editar Observação</span>
                </h3>
                <button onclick="closeObsModal()" class="text-slate-400 hover:text-slate-600 text-base">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            <div>
                <label class="block text-xs font-semibold text-slate-500 mb-1">Conteúdo Completo:</label>
                <textarea id="obsModalTextarea" rows="6" class="w-full text-xs p-3 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50 font-medium leading-relaxed"></textarea>
            </div>
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeObsModal()" class="px-4 py-2 bg-slate-200 hover:bg-slate-300 text-slate-700 font-semibold rounded-lg text-xs transition">Cancelar</button>
                <button onclick="saveObsModal()" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold rounded-lg text-xs transition shadow flex items-center gap-1.5">
                    <i class="fa-solid fa-floppy-disk text-xs"></i> Aplicar e Salvar
                </button>
            </div>
        </div>
    </div>

    <!-- HEADER NAVBAR ESTILO GF -->
    <header class="bg-slate-900 text-white shadow-lg no-print">
        <div class="max-w-[1800px] mx-auto px-6 py-4 flex flex-col md:flex-row justify-between items-center gap-4">
            <div class="flex items-center space-x-4">
                <div class="bg-white/10 p-2 rounded-lg border border-slate-700 flex items-center justify-center min-w-[120px] max-h-[55px] overflow-hidden">
                    <img id="logoApp" src="logo.png" alt="Logo" class="max-h-10 max-w-[140px] object-contain" onerror="tryNextLogo(this)">
                </div>
                <i class="fa-solid fa-screwdriver-wrench text-blue-400 text-2xl"></i>
                <div>
                    <h1 class="text-xl font-bold tracking-wide text-blue-400">🛡️ Dashboard de Atendimento Técnico</h1>
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
            <div class="max-w-[1800px] mx-auto flex gap-6 text-sm overflow-x-auto">
                <button onclick="switchTab('tabGeral')" id="btnTabGeral" class="nav-tab active py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2 whitespace-nowrap">
                    <i class="fa-solid fa-chart-pie text-indigo-400"></i>
                    <span>Visão Geral & KPIs</span>
                </button>
                <button onclick="switchTab('tabComparativo')" id="btnTabComparativo" class="nav-tab py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2 whitespace-nowrap">
                    <i class="fa-solid fa-calendar-days text-sky-400"></i>
                    <span>Comparativo Mensal e Semanal</span>
                </button>
                <button onclick="switchTab('tabOSAbertas')" id="btnTabOSAbertas" class="nav-tab py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2 whitespace-nowrap">
                    <i class="fa-solid fa-folder-open text-amber-400"></i>
                    <span>O.S Abertas & Rotas</span>
                </button>
            </div>
        </div>
    </header>

    <!-- PRINT HEADER -->
    <div class="print-header px-6 pt-4 hidden">
        <div class="flex items-center gap-4">
            <img id="logoPrint" src="logo.png" alt="Logo" class="h-12 w-auto object-contain" onerror="tryNextLogo(this)">
            <div>
                <h1 class="text-xl font-bold text-slate-900">Relatório de KPIs - Atendimento Técnico</h1>
                <p class="text-xs text-slate-600">Segurança Eletrônica | Gestão de Chamados e Produtividade</p>
            </div>
        </div>
        <div class="text-right text-xs text-slate-700 font-medium">
            <p class="bg-slate-100 border border-slate-300 px-3 py-1.5 rounded">📅 <strong>Emissão:</strong> <span id="printDate"></span></p>
        </div>
    </div>

    <!-- MAIN CONTAINER -->
    <main class="max-w-[1800px] mx-auto px-6 py-6 space-y-6" id="pdfContent">

        <!-- BANNER DE STATUS DE CARREGAMENTO -->
        <div id="statusBanner" class="bg-sky-50 border-l-4 border-sky-500 text-sky-900 p-4 rounded-xl shadow-sm flex flex-col md:flex-row justify-between items-start md:items-center gap-2 no-print">
            <div class="text-xs font-medium flex items-center gap-2" id="bannerMessage">
                <i class="fa-solid fa-cloud-arrow-down text-sky-600 text-base"></i>
                <span>Conectando com o banco de dados do Google Drive...</span>
            </div>
            <div class="flex items-center gap-2">
                <span id="bannerTag" class="text-xs bg-sky-200 text-sky-800 font-semibold px-2.5 py-1 rounded-lg">Google Drive Online</span>
            </div>
        </div>

        <!-- ================= TAB 1: VISÃO GERAL ================= -->
        <div id="tabGeral" class="tab-pane space-y-6">

            <!-- FILTRO DE PERÍODO PERSONALIZADO -->
            <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 no-print space-y-4">
                <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 border-b border-slate-100 pb-3">
                    <div class="flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-indigo-600 text-base"></i>
                        <h3 class="text-sm font-bold text-slate-800">Filtro de Período Personalizado (Abertura)</h3>
                    </div>
                    <div class="flex items-center gap-2 flex-wrap">
                        <button onclick="setPresetPeriod('today', 'geral')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Hoje</button>
                        <button onclick="setPresetPeriod('7d', 'geral')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Últimos 7 dias</button>
                        <button onclick="setPresetPeriod('30d', 'geral')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Últimos 30 dias</button>
                        <button onclick="setPresetPeriod('thisMonth', 'geral')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Mês Atual</button>
                        <button onclick="setPresetPeriod('lastMonth', 'geral')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Mês Anterior</button>
                        <button onclick="clearDateFilters('geral')" class="text-[11px] font-bold bg-rose-50 hover:bg-rose-100 text-rose-600 px-3 py-1 rounded-lg border border-rose-200 transition flex items-center gap-1">
                            <i class="fa-solid fa-rotate-left"></i> Limpar Datas
                        </button>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-xs">
                    <div>
                        <label for="startDateFilter" class="block font-bold text-slate-700 mb-1">📅 Data Inicial:</label>
                        <input type="date" id="startDateFilter" onchange="applyGlobalFilters()" class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none bg-slate-50 font-medium">
                    </div>

                    <div>
                        <label for="endDateFilter" class="block font-bold text-slate-700 mb-1">📅 Data Final:</label>
                        <input type="date" id="endDateFilter" onchange="applyGlobalFilters()" class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none bg-slate-50 font-medium">
                    </div>
                </div>
            </div>

            <!-- INDICADOR DE FILTRO DE CARD ATIVO -->
            <div id="activeFilterBadge" class="hidden bg-amber-50 border-l-4 border-amber-500 text-amber-900 p-3.5 rounded-xl shadow-sm flex justify-between items-center no-print">
                <div class="text-xs flex items-center gap-2">
                    <i class="fa-solid fa-filter text-amber-600 text-sm"></i>
                    <span class="font-bold">Filtro de Card Ativo:</span>
                    <span id="activeFilterText" class="font-semibold text-amber-800">Nenhum</span>
                </div>
                <button onclick="filterByKPI('ALL')" class="text-xs bg-amber-200 hover:bg-amber-300 text-amber-900 px-3 py-1 rounded-lg font-bold border border-amber-400 transition">
                    ✕ Limpar Filtro
                </button>
            </div>

            <!-- INTERACTIVE KPI SUMMARY CARDS -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                
                <!-- CARD 1: TOTAL DE CHAMADOS -->
                <div id="cardTotalOS" onclick="filterByKPI('ALL')" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-blue-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-blue-600 uppercase tracking-wide">Total de Chamados</p>
                            <h3 id="kpiTotalOS" class="text-3xl font-extrabold text-slate-800 mt-1">0</h3>
                        </div>
                        <div class="bg-blue-100 p-3 rounded-lg text-blue-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-list-check text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-blue-600 mt-2 pointer-events-none flex items-center gap-1">
                        <span>🖱️ Clique para ver todas as OSs</span>
                    </p>
                </div>

                <!-- CARD 2: MANUTENÇÃO CORRETIVA -->
                <div id="cardCorretiva" onclick="filterByKPI('CORRETIVA')" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-amber-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-amber-600 uppercase tracking-wide">% Manut. Corretiva</p>
                            <h3 id="kpiCorretiva" class="text-3xl font-extrabold text-amber-600 mt-1">0%</h3>
                        </div>
                        <div class="bg-amber-100 p-3 rounded-lg text-amber-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-triangle-exclamation text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-amber-600 mt-2 pointer-events-none flex items-center gap-1">
                        <span>🖱️ Clique para filtrar emergências</span>
                    </p>
                </div>

                <!-- CARD 3: ATRASO SLA -->
                <div id="cardAtraso" onclick="filterByKPI('ATRASO')" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-rose-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden
