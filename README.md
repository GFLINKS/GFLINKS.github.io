<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel de Atendimento Técnico - KPIs & SLA</title>

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
            <div class="max-w-[1800px] mx-auto flex gap-6 text-sm">
                <button onclick="switchTab('tabGeral')" id="btnTabGeral" class="nav-tab active py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-indigo-400"></i>
                    <span>Visão Geral & KPIs</span>
                </button>
                <button onclick="switchTab('tabComparativo')" id="btnTabComparativo" class="nav-tab py-3 px-2 text-slate-300 hover:text-white transition flex items-center gap-2">
                    <i class="fa-solid fa-calendar-days text-sky-400"></i>
                    <span>Comparativo Mensal e Semanal</span>
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
                        <h3 class="text-sm font-bold text-slate-800">Filtro de Período Personalizado (Filtrado por Data de Término)</h3>
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
                        <label for="startDateFilter" class="block font-bold text-slate-700 mb-1">📅 Data Inicial (Término):</label>
                        <input type="date" id="startDateFilter" onchange="applyGlobalFilters()" class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none bg-slate-50 font-medium">
                    </div>

                    <div>
                        <label for="endDateFilter" class="block font-bold text-slate-700 mb-1">📅 Data Final (Término):</label>
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
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-4">
                
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
                        <span>🖱️ Ver todas as OSs</span>
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
                        <span>🖱️ Filtrar emergências</span>
                    </p>
                </div>

                <!-- CARD 3: ATRASO SLA -->
                <div id="cardAtraso" onclick="filterByKPI('ATRASO')" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-rose-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-rose-600 uppercase tracking-wide">Taxa de Atraso SLA</p>
                            <h3 id="kpiAtraso" class="text-3xl font-extrabold text-rose-600 mt-1">0%</h3>
                        </div>
                        <div class="bg-rose-100 p-3 rounded-lg text-rose-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-clock-rotate-left text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-rose-600 mt-2 pointer-events-none flex items-center gap-1">
                        <span>🖱️ OSs fora do prazo</span>
                    </p>
                </div>

                <!-- CARD 4: NOVO CARD SLA TEMPO MÉDIO -->
                <div id="cardTempoMedio" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-purple-500 transition-all hover:scale-[1.02] hover:shadow-md select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-purple-600 uppercase tracking-wide">Tempo Médio SLA</p>
                            <h3 id="kpiTempoMedio" class="text-3xl font-extrabold text-purple-600 mt-1">00:00</h3>
                        </div>
                        <div class="bg-purple-100 p-3 rounded-lg text-purple-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-stopwatch text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-purple-600 mt-2 pointer-events-none flex items-center gap-1">
                        <span>⏱️ Média de Duração / OS</span>
                    </p>
                </div>

                <!-- CARD 5: TÉCNICOS ATIVOS -->
                <div id="cardTecnicos" onclick="filterByKPI('TECNICOS')" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-emerald-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-emerald-600 uppercase tracking-wide">Técnicos Ativos</p>
                            <h3 id="kpiTecnicos" class="text-3xl font-extrabold text-emerald-600 mt-1">0</h3>
                        </div>
                        <div class="bg-emerald-100 p-3 rounded-lg text-emerald-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-user-gear text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-emerald-600 mt-2 pointer-events-none flex items-center gap-1">
                        <span>🖱️ Ir para a tabela</span>
                    </p>
                </div>

            </div>

            <!-- CHARTS SECTION GRID -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 print-keep-together">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-chart-pie text-indigo-500"></i> Distribuição por Status da OS
                    </h4>
                    <div class="h-64 relative">
                        <canvas id="chartStatus"></canvas>
                    </div>
                </div>

                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 print-keep-together">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-chart-column text-blue-500"></i> Volume de OSs por Técnico
                    </h4>
                    <div class="h-64 relative">
                        <canvas id="chartTecnicos"></canvas>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 print-keep-together">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-store text-purple-500"></i> Top 10 Lojas / Unidades com Mais Chamados
                    </h4>
                    <div class="h-64 relative">
                        <canvas id="chartLojas"></canvas>
                    </div>
                </div>

                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 print-keep-together">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-video text-emerald-500"></i> Top Equipamentos & Peças Substituídas
                    </h4>
                    <div class="h-64 relative">
                        <canvas id="chartEquipamentos"></canvas>
                    </div>
                </div>
            </div>

            <!-- DATA TABLE SECTION -->
            <div id="tableTechSection" class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden transition-all duration-300">
                <div class="p-4 border-b border-slate-100 flex justify-between items-center bg-slate-50/50">
                    <div>
                        <h3 class="text-sm font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-users-gear text-indigo-600"></i> Tabela Resumo da Equipe Técnica
                        </h3>
                        <p class="text-xs text-slate-500">Resumo de volume e carga de trabalho por técnico em campo.</p>
                    </div>
                </div>
                <div class="overflow-x-auto w-full">
                    <table class="w-full text-left text-xs border-collapse">
                        <thead class="bg-slate-800 text-white font-semibold sticky top-0 z-20">
                            <tr>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700">Técnico de Campo</th>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700 text-right">Qtd. OSs Atendidas</th>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700 text-right">% Participação no Total</th>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700 text-center">Status de Carga</th>
                            </tr>
                        </thead>
                        <tbody id="tableTechBody" class="divide-y divide-slate-100 text-slate-700">
                        </tbody>
                    </table>
                </div>
            </div>

        </div>

        <!-- ================= TAB 2: COMPARATIVO MENSAL E SEMANAL ================= -->
        <div id="tabComparativo" class="tab-pane hidden space-y-6">

            <!-- CONTROLES COM CALENDÁRIO EMBUTIDO NA ABA COMPARATIVO -->
            <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 space-y-4">
                <div class="flex flex-col md:flex-row items-start md:items-center justify-between gap-4 border-b border-slate-100 pb-3">
                    <div>
                        <h2 class="text-base font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-chart-line text-blue-600"></i> Análise Comparativa de Atendimentos
                        </h2>
                        <p class="text-xs text-slate-500">Selecione o técnico e o período (Filtrado por Data de Término) para comparar a produtividade real.</p>
                    </div>

                    <div class="flex items-center gap-2 flex-wrap">
                        <button onclick="setPresetPeriod('today', 'comp')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Hoje</button>
                        <button onclick="setPresetPeriod('7d', 'comp')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Últimos 7 dias</button>
                        <button onclick="setPresetPeriod('30d', 'comp')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Últimos 30 dias</button>
                        <button onclick="setPresetPeriod('thisMonth', 'comp')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Mês Atual</button>
                        <button onclick="setPresetPeriod('lastMonth', 'comp')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Mês Anterior</button>
                        <button onclick="clearDateFilters('comp')" class="text-[11px] font-bold bg-rose-50 hover:bg-rose-100 text-rose-600 px-3 py-1 rounded-lg border border-rose-200 transition flex items-center gap-1">
                            <i class="fa-solid fa-rotate-left"></i> Limpar Datas
                        </button>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 text-xs">
                    <div>
                        <label for="selectTecnicoComp" class="block font-bold text-slate-700 mb-1">👨‍🔧 Técnico de Campo:</label>
                        <select id="selectTecnicoComp" onchange="updateComparativoCharts()" class="w-full bg-slate-50 border border-slate-300 text-slate-800 text-xs font-semibold rounded-lg p-2.5 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                            <option value="TODOS">Todos os Técnicos (Ativos)</option>
                        </select>
                    </div>

                    <div>
                        <label for="startDateComp" class="block font-bold text-slate-700 mb-1">📅 Data Inicial (Término):</label>
                        <input type="date" id="startDateComp" onchange="updateComparativoCharts()" class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none bg-slate-50 font-medium">
                    </div>

                    <div>
                        <label for="endDateComp" class="block font-bold text-slate-700 mb-1">📅 Data Final (Término):</label>
                        <input type="date" id="endDateComp" onchange="updateComparativoCharts()" class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none bg-slate-50 font-medium">
                    </div>
                </div>
            </div>

            <!-- CARDS INTERATIVOS DA TAB COMPARATIVO -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-4">
                
                <div id="cardCompTotalOS" onclick="scrollToCompSection('tableCompSection')" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-blue-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-blue-600 uppercase tracking-wide">Atendimentos no Filtro</p>
                            <h3 id="compTotalOS" class="text-3xl font-extrabold text-blue-600 mt-1">0</h3>
                        </div>
                        <div class="bg-blue-100 p-3 rounded-lg text-blue-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-receipt text-xl"></i>
                        </div>
                    </div>
                    <p id="compTecnicoLabel" class="text-xs font-bold text-slate-400 mt-2 pointer-events-none">Todos os Técnicos Ativos</p>
                </div>

                <div id="cardCompTempoMedio" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-purple-500 transition-all hover:scale-[1.02] hover:shadow-md select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-purple-600 uppercase tracking-wide">Tempo Médio SLA</p>
                            <h3 id="compTempoMedio" class="text-3xl font-extrabold text-purple-600 mt-1">00:00</h3>
                        </div>
                        <div class="bg-purple-100 p-3 rounded-lg text-purple-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-stopwatch text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-slate-400 mt-2 pointer-events-none">Média no Período Selecionado</p>
                </div>

                <div id="cardCompMediaMes" onclick="scrollToCompSection('chartMensalSection')" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-indigo-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-indigo-600 uppercase tracking-wide">Média Histórica Mensal</p>
                            <h3 id="compMediaMes" class="text-3xl font-extrabold text-indigo-600 mt-1">0.0</h3>
                        </div>
                        <div class="bg-indigo-100 p-3 rounded-lg text-indigo-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-chart-line text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-slate-400 mt-2 pointer-events-none">OSs / Mês Ativo do Técnico</p>
                </div>

                <div id="cardCompMediaSemana" onclick="scrollToCompSection('chartSemanalSection')" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-emerald-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-emerald-600 uppercase tracking-wide">Média Histórica Semanal</p>
                            <h3 id="compMediaSemana" class="text-3xl font-extrabold text-emerald-600 mt-1">0.0</h3>
                        </div>
                        <div class="bg-emerald-100 p-3 rounded-lg text-emerald-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-calendar-week text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-slate-400 mt-2 pointer-events-none">OSs / Semana Ativa do Técnico</p>
                </div>

                <div id="cardCompPctTotal" onclick="resetTecnicoSelect()" 
                     class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 border-l-4 border-l-amber-500 cursor-pointer transition-all hover:scale-[1.02] hover:shadow-md active:scale-95 select-none relative overflow-hidden group">
                    <div class="flex justify-between items-start pointer-events-none">
                        <div>
                            <p class="text-[11px] font-bold text-amber-600 uppercase tracking-wide">Participação na Equipe</p>
                            <h3 id="compPctTotal" class="text-3xl font-extrabold text-amber-600 mt-1">0%</h3>
                        </div>
                        <div class="bg-amber-100 p-3 rounded-lg text-amber-600 group-hover:scale-110 transition">
                            <i class="fa-solid fa-pie-chart text-xl"></i>
                        </div>
                    </div>
                    <p class="text-xs font-bold text-slate-400 mt-2 pointer-events-none">Do Total do Período Mapeado</p>
                </div>

            </div>

            <!-- CHARTS GRID FOR MENSAL AND SEMANAL -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                
                <div id="chartMensalSection" class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 print-keep-together transition-all duration-300">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4 flex items-center justify-between">
                        <span class="flex items-center gap-2"><i class="fa-solid fa-calendar-days text-blue-500"></i> Comparativo Mensal (Mês a Mês)</span>
                        <span class="text-xs bg-blue-100 text-blue-800 font-semibold px-2 py-0.5 rounded">Evolução Mensal</span>
                    </h4>
                    <div class="h-72 relative">
                        <canvas id="chartMensal"></canvas>
                    </div>
                </div>

                <div id="chartSemanalSection" class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 print-keep-together transition-all duration-300">
                    <h4 class="text-xs font-bold text-slate-700 uppercase mb-4 flex items-center justify-between">
                        <span class="flex items-center gap-2"><i class="fa-solid fa-calendar-week text-emerald-500"></i> Comparativo Semanal</span>
                        <span id="badgeSemanal" class="text-xs bg-emerald-100 text-emerald-800 font-semibold px-2 py-0.5 rounded">Evolução Semanal</span>
                    </h4>
                    <div class="h-72 relative">
                        <canvas id="chartSemanal"></canvas>
                    </div>
                </div>
            </div>

            <!-- DETAILED COMPARATIVE TABLE COM LISTAGEM EXPANSÍVEL DE OSs -->
            <div id="tableCompSection" class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden transition-all duration-300">
                <div class="p-4 border-b border-slate-100 flex justify-between items-center bg-slate-50/50">
                    <h3 class="text-sm font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-table text-indigo-600"></i> Tabela Detalhada por Mês do Técnico Selecionado
                    </h3>
                </div>
                <div class="overflow-x-auto w-full">
                    <table class="w-full text-left text-xs border-collapse">
                        <thead class="bg-slate-800 text-white font-semibold sticky top-0 z-20">
                            <tr>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700">Mês / Ano</th>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700 text-right">Qtd. OSs Atendidas</th>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700 text-right">% do Total Mapeado do Técnico</th>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700 text-center">Status Mês</th>
                                <th class="p-3 bg-slate-800 text-white font-bold border-b border-slate-700 text-center">Listar OSs</th>
                            </tr>
                        </thead>
                        <tbody id="tableCompBody" class="divide-y divide-slate-100 text-slate-700">
                        </tbody>
                    </table>
                </div>
            </div>

        </div>

    </main>

    <!-- EMBEDDED JAVASCRIPT LOGIC -->
    <script>
        // URL DO SEU GOOGLE APPS SCRIPT WEB APP
        const GOOGLE_SCRIPT_URL = "https://script.google.com/macros/s/AKfycbyCQgoB_cP3V3BAhFOGmqs1rCxW3Ae7l_a6aLXBWaV1FKkV2iuOGsXmSKTOp2FsckuRAw/exec";

        let driveHistoryData = [];
        let activeData = [];
        let currentKPIFilter = 'ALL';

        // HELPER PARA MAPEAR O TÉCNICO/RESPONSÁVEL
        function getTechName(item) {
            return item['Responsável'] || item['Responsavel'] || item['Técnico'] || item['Tecnico'] || item['Técnico Responsável'] || 'Não Atribuído';
        }

        // HELPER PARA MAPEAR A DATA DE TÉRMINO (FECHAMENTO DA OS)
        function getTerminoDate(item) {
            let raw = item['Término'] || item['Termino'] || item['Data do Fechamento'] || item['Data_Fechamento'] || item['Data Fechamento'] || item['Data de Fechamento'] || item['J'] || item['Início'] || item['Inicio'] || item['Data de Abertura'] || '';
            return parseDateSmart(raw);
        }

        // HELPER PARA MAPEAR A DATA DE INÍCIO
        function getInicioDate(item) {
            let raw = item['Início'] || item['Inicio'] || item['Data de Abertura'] || item['Data_Abertura'] || item['Data Abertura'] || item['E'] || '';
            return parseDateSmart(raw);
        }

        // HELPER PARA EXTRAIR O TEMPO DA OS EM MINUTOS
        function parseTempoToMinutes(item) {
            let rawTempo = item['Tempo'] || item['Tempo de Atendimento'] || item['Duração'] || item['Duracao'] || '';
            if (rawTempo !== null && rawTempo !== undefined && rawTempo !== '') {
                if (typeof rawTempo === 'number') {
                    if (rawTempo < 1) return rawTempo * 24 * 60; // fração de dia do Excel
                    return rawTempo;
                }
                let str = rawTempo.toString().trim();
                let parts = str.split(':');
                if (parts.length >= 2) {
                    let h = parseFloat(parts[0]) || 0;
                    let m = parseFloat(parts[1]) || 0;
                    let s = parts[2] ? (parseFloat(parts[2]) || 0) : 0;
                    return h * 60 + m + s / 60;
                }
                let num = parseFloat(str);
                if (!isNaN(num)) return num;
            }

            // Cálculo reserva caso a coluna Tempo esteja ausente: diferença entre Término e Início
            let dtIni = getInicioDate(item);
            let dtEnd = getTerminoDate(item);
            if (dtIni && dtEnd && dtEnd >= dtIni) {
                return (dtEnd.getTime() - dtIni.getTime()) / 60000;
            }
            return 0;
        }

        // FORMATADOR DE MINUTOS PARA FORMATO HH:MM
        function formatMinutesToDisplay(totalMinutes) {
            if (!totalMinutes || isNaN(totalMinutes) || totalMinutes <= 0) return "00:00";
            let hours = Math.floor(totalMinutes / 60);
            let mins = Math.round(totalMinutes % 60);
            if (mins >= 60) {
                hours += 1;
                mins = 0;
            }
            return String(hours).padStart(2, '0') + ':' + String(mins).padStart(2, '0');
        }

        // SMART DATE PARSER (SUPORTA FORMATO 15:14 05/10/2026, EXCEL E ISO)
        function parseDateSmart(val) {
            if (val === null || val === undefined || val === '') return null;
            if (val instanceof Date) return isNaN(val.getTime()) ? null : val;
            
            if (typeof val === 'number' || (!isNaN(val) && !val.toString().includes('/') && !val.toString().includes('-') && !val.toString().includes(':'))) {
                let num = parseFloat(val);
                if (num > 20000 && num < 60000) {
                    let dateObj = new Date(Math.round((num - 25569) * 86400 * 1000));
                    let userOffset = dateObj.getTimezoneOffset() * 60000;
                    let cleanDate = new Date(dateObj.getTime() + userOffset);
                    return isNaN(cleanDate.getTime()) ? null : cleanDate;
                }
            }
            
            let str = val.toString().trim();
            if (!str) return null;

            // Formato com Hora antes da Data: "15:14 05/10/2026" ou "15:14:00 05/10/2026"
            let timeFirstRegex = /^(\d{1,2}):(\d{1,2})(?
