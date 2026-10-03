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

                <button onclick="fetchDatabaseFromDrive()" class="bg-sky-600 hover:bg-sky-500 text-white text-xs font-semibold px-4 py-2.5 rounded-lg transition shadow flex items-center gap-2" title="Atualizar dados do Drive">
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

        <!-- BARRA DE FILTROS E PESQUISA AVANÇADA (COLUNA D, L E CALENDÁRIO DUPLO) -->
        <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 no-print space-y-4">
            <div class="flex flex-col lg:flex-row lg:items-center justify-between gap-4 border-b border-slate-100 pb-3">
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-filter text-indigo-600 text-base"></i>
                    <h3 class="text-sm font-bold text-slate-800">Filtros de Pesquisa & Período Personalizado</h3>
                </div>
                <div class="flex items-center gap-2 flex-wrap">
                    <button onclick="setPresetPeriod('today')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Hoje</button>
                    <button onclick="setPresetPeriod('7d')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Últimos 7 dias</button>
                    <button onclick="setPresetPeriod('30d')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Últimos 30 dias</button>
                    <button onclick="setPresetPeriod('thisMonth')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Mês Atual</button>
                    <button onclick="setPresetPeriod('lastMonth')" class="text-[11px] font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-lg border border-slate-300 transition">Mês Anterior</button>
                    <button onclick="clearSearchFilters()" class="text-[11px] font-bold bg-rose-50 hover:bg-rose-100 text-rose-600 px-3 py-1 rounded-lg border border-rose-200 transition flex items-center gap-1">
                        <i class="fa-solid fa-rotate-left"></i> Limpar Filtros
                    </button>
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 text-xs">
                <!-- PESQUISA COLUNA D -->
                <div>
                    <label for="searchColD" class="block font-bold text-slate-700 mb-1">🔍 Pesquisar em Coluna D (Tipo/Descrição/OS):</label>
                    <input type="text" id="searchColD" oninput="applyGlobalFilters()" placeholder="Ex: Corretiva, Preventiva..." class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none bg-slate-50 font-medium">
                </div>

                <!-- PESQUISA COLUNA L -->
                <div>
                    <label for="searchColL" class="block font-bold text-slate-700 mb-1">🔍 Pesquisar em Coluna L (Obs/Assunto/Equip.):</label>
                    <input type="text" id="searchColL" oninput="applyGlobalFilters()" placeholder="Ex: VIP 1220, Câmera, Troca..." class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none bg-slate-50 font-medium">
                </div>

                <!-- CALENDÁRIO INÍCIO -->
                <div>
                    <label for="startDateFilter" class="block font-bold text-slate-700 mb-1">📅 Data Inicial (Abertura):</label>
                    <input type="date" id="startDateFilter" onchange="applyGlobalFilters()" class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none bg-slate-50 font-medium">
                </div>

                <!-- CALENDÁRIO FIM -->
                <div>
                    <label for="endDateFilter" class="block font-bold text-slate-700 mb-1">📅 Data Final (Abertura):</label>
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

        <!-- ================= TAB 1: VISÃO GERAL ================= -->
        <div id="tabGeral" class="tab-pane space-y-6">

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
                        <span>🖱️️ Clique para ver OSs fora do prazo</span>
                    </p>
                </div>

                <!-- CARD 4: TÉCNICOS ATIVOS -->
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
                        <span>🖱️ Clique para ir para a tabela</span>
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

            <!-- CONTROLES MENSAL / SEMANAL -->
            <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 flex flex-col md:flex-row items-start md:items-center justify-between gap-4">
                <div>
                    <h2 class="text-base font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-chart-line text-blue-600"></i> Análise Comparativa de Atendimentos
                    </h2>
                    <p class="text-xs text-slate-500">Selecione o técnico e o nível de detalhamento para comparar a produtividade real.</p>
                </div>

                <div class="flex flex-wrap items-center gap-4">
                    <div class="flex items-center gap-2">
                        <label for="selectTecnicoComp" class="text-xs font-bold text-slate-700">👨‍🔧 Técnico:</label>
                        <select id="selectTecnicoComp" onchange="updateComparativoCharts()" class="bg-slate-50 border border-slate-300 text-slate-800 text-xs font-semibold rounded-lg p-2.5 focus:ring-2 focus:ring-blue-500 focus:outline-none min-w-[200px]">
                            <option value="TODOS">Todos os Técnicos (Ativos)</option>
                        </select>
                    </div>

                    <div class="flex items-center gap-2">
                        <label for="selectRangeSemanal" class="text-xs font-bold text-slate-700">🗓 Zoom Semanal:</label>
                        <select id="selectRangeSemanal" onchange="updateComparativoCharts()" class="bg-slate-50 border border-slate-300 text-slate-800 text-xs font-semibold rounded-lg p-2.5 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                            <option value="CURRENT">Semana Atual (Mais Recente)</option>
                            <option value="PREV">Última Semana (Anterior)</option>
                            <option value="4">Últimas 4 Semanas</option>
                            <option value="10" selected>Últimas 10 Semanas</option>
                            <option value="16">Últimas 16 Semanas</option>
                            <option value="ALL">Todas as Semanas</option>
                        </select>
                    </div>
                </div>
            </div>

            <!-- CARDS INTERATIVOS DA TAB COMPARATIVO -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                
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
                        <span id="badgeSemanal" class="text-xs bg-emerald-100 text-emerald-800 font-semibold px-2 py-0.5 rounded">Últimas 10 Semanas</span>
                    </h4>
                    <div class="h-72 relative">
                        <canvas id="chartSemanal"></canvas>
                    </div>
                </div>
            </div>

            <!-- DETAILED COMPARATIVE TABLE -->
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
        // URL DO SEU GOOGLE APPS SCRIPT WEB APP (MANTIDA ORIGINAL DA SUA VERSÃO FUNCIONAL)
        const GOOGLE_SCRIPT_URL = "https://script.google.com/macros/s/AKfycbyCQgoB_cP3V3BAhFOGmqs1rCxW3Ae7l_a6aLXBWaV1FKkV2iuOGsXmSKTOp2FsckuRAw/exec";

        let driveHistoryData = [];
        let activeData = [];
        let currentKPIFilter = 'ALL';

        // SMART DATE PARSER FOR BRAZILIAN, ISO, AND EXCEL SERIAL DATES
        function parseDateSmart(val) {
            if (val === null || val === undefined || val === '') return null;
            if (val instanceof Date) return isNaN(val.getTime()) ? null : val;
            
            if (typeof val === 'number' || (!isNaN(val) && !val.toString().includes('/') && !val.toString().includes('-'))) {
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

            let brRegex = /^(\d{1,2})[\/\.-](\d{1,2})[\/\.-](\d{4})(?:\s+(\d{1,2}):(\d{1,2})(?::(\d{1,2}))?)?$/;
            let matchBr = str.match(brRegex);
            if (matchBr) {
                let day = parseInt(matchBr[1], 10);
                let month = parseInt(matchBr[2], 10) - 1;
                let year = parseInt(matchBr[3], 10);
                let hour = matchBr[4] ? parseInt(matchBr[4], 10) : 0;
                let min = matchBr[5] ? parseInt(matchBr[5], 10) : 0;
                let sec = matchBr[6] ? parseInt(matchBr[6], 10) : 0;
                let d = new Date(year, month, day, hour, min, sec);
                return isNaN(d.getTime()) || d.getFullYear() < 2000 ? null : d;
            }

            let isoRegex = /^(\d{4})[\/\.-](\d{1,2})[\/\.-](\d{1,2})(?:[T\s]+(\d{1,2}):(\d{1,2})(?::(\d{1,2}))?)?$/;
            let matchIso = str.match(isoRegex);
            if (matchIso) {
                let year = parseInt(matchIso[1], 10);
                let month = parseInt(matchIso[2], 10) - 1;
                let day = parseInt(matchIso[3], 10);
                let hour = matchIso[4] ? parseInt(matchIso[4], 10) : 0;
                let min = matchIso[5] ? parseInt(matchIso[5], 10) : 0;
                let sec = matchIso[6] ? parseInt(matchIso[6], 10) : 0;
                let d = new Date(year, month, day, hour, min, sec);
                return isNaN(d.getTime()) || d.getFullYear() < 2000 ? null : d;
            }

            let standard = new Date(str);
            if (!isNaN(standard.getTime()) && standard.getFullYear() >= 2000) {
                return standard;
            }

            return null;
        }

        // CÁLCULO DE ATRASO SLA BASEADO NAS COLUNAS E, I E J
        function isSLAOverdue(item) {
            let rawAbertura = item['Data de Abertura'] || item['Data_Abertura'] || item['Data Abertura'] || item['E'] || '';
            let rawPrevista = item['Data/Hora Prevista de Atendimento'] || item['Data/Hora Prevista'] || item['Data Prevista de Atendimento'] || item['Data Prevista'] || item['I'] || '';
            let rawFechamento = item['Data do Fechamento'] || item['Data_Fechamento'] || item['Data Fechamento'] || item['Data de Fechamento'] || item['J'] || '';

            let dtPrevista = parseDateSmart(rawPrevista);
            let dtFechamento = parseDateSmart(rawFechamento);

            if (dtPrevista) {
                if (dtFechamento) {
                    return dtFechamento.getTime() > dtPrevista.getTime();
                } else {
                    let now = new Date();
                    return now.getTime() > dtPrevista.getTime();
                }
            }

            let status = (item['Status da OS'] || item['Status'] || '').toString().toLowerCase();
            return status.includes('atrasad');
        }

        // EXCLUDED TECHNICIANS LIST
        const EXCLUDED_TECHS = [
            'wesley mendonça silva',
            'alex sandro da silva pedrosa',
            'técnico padrão',
            'tecnico padrão',
            'técnico são paulo',
            'tecnico são paulo'
        ];

        function isExcludedTech(techName) {
            if (!techName) return false;
            const normalized = techName.toString().trim().toLowerCase();
            return EXCLUDED_TECHS.some(ex => normalized.includes(ex));
        }

        // Logo fallbacks
        const logoVariants = ['logo.png', 'logo.jpg', 'logo.jpeg', 'logo.svg', 'logo', 'LOGO.png', 'LOGO.JPG', 'LOGO.PNG'];
        let logoAttemptIndex = 0;

        function tryNextLogo(imgElement) {
            logoAttemptIndex++;
            if (logoAttemptIndex < logoVariants.length) {
                imgElement.src = logoVariants[logoAttemptIndex];
            } else {
                imgElement.style.display = 'none';
                if (imgElement.parentElement && imgElement.parentElement.classList.contains('min-w-[120px]')) {
                    imgElement.parentElement.classList.add('hidden');
                }
            }
        }

        function updateEmissaoDateTime() {
            const now = new Date();
            const dateStr = now.toLocaleDateString('pt-BR');
            const timeStr = now.toLocaleTimeString('pt-BR');
            const fullStr = dateStr + ' às ' + timeStr;
            
            document.getElementById('appEmissaoDate').innerText = fullStr;
            document.getElementById('printDate').innerText = fullStr;
        }

        function switchTab(tabId) {
            document.getElementById('tabGeral').classList.add('hidden');
            document.getElementById('tabComparativo').classList.add('hidden');
            
            document.getElementById('btnTabGeral').classList.remove('active');
            document.getElementById('btnTabComparativo').classList.remove('active');

            if (tabId === 'tabGeral') {
                document.getElementById('tabGeral').classList.remove('hidden');
                document.getElementById('btnTabGeral').classList.add('active');
            } else if (tabId === 'tabComparativo') {
                document.getElementById('tabComparativo').classList.remove('hidden');
                document.getElementById('btnTabComparativo').classList.add('active');
                updateComparativoCharts();
            }
        }

        let chartStatusObj = null;
        let chartTecnicosObj = null;
        let chartLojasObj = null;
        let chartEquipamentosObj = null;

        let chartMensalObj = null;
        let chartSemanalObj = null;

        // FUNÇÃO DE BUSCA DOS DADOS NO GOOGLE DRIVE VIA WEB APP (EXATAMENTE COMO FUNCIONA)
        function fetchDatabaseFromDrive() {
            const bannerMsg = document.getElementById('bannerMessage');
            if (bannerMsg) {
                bannerMsg.innerHTML = '<span><i class="fa-solid fa-spinner fa-spin text-sky-600"></i> <b>Sincronizando:</b> Conectando com a pasta do Google Drive...</span>';
            }

            fetch(GOOGLE_SCRIPT_URL)
                .then(response => response.json())
                .then(historyRecords => {
                    if (Array.isArray(historyRecords) && historyRecords.length > 0) {
                        driveHistoryData = historyRecords;
                        const select = document.getElementById('historySelect');
                        if (select) {
                            select.innerHTML = '';
                            historyRecords.forEach((rec, index) => {
                                const opt = document.createElement('option');
                                opt.value = index;
                                opt.innerText = `${rec.fileName} (${rec.timestamp})`;
                                select.appendChild(opt);
                            });
                        }

                        // Carrega a planilha mais recente por padrão
                        loadDriveRecord(0);
                    } else if (historyRecords && historyRecords.status === "error") {
                        if (bannerMsg) {
                            bannerMsg.innerHTML = `<span>⚠️ Erro no Apps Script: ${historyRecords.message}</span>`;
                        }
                    } else {
                        if (bannerMsg) {
                            bannerMsg.innerHTML = '<span>⚠️ Nenhuma planilha encontrada na pasta do Google Drive.</span>';
                        }
                    }
                })
                .catch(err => {
                    console.error("Erro ao conectar com a API do Google Drive:", err);
                    if (bannerMsg) {
                        bannerMsg.innerHTML = '<span>⚠️ Falha na sincronização online com o Google Drive. Verifique a URL do App.</span>';
                    }
                });
        }

        function loadDriveRecord(index) {
            if (!driveHistoryData || !driveHistoryData[index]) return;
            const rec = driveHistoryData[index];
            activeData = rec.data;
            currentKPIFilter = 'ALL';
            
            const bannerMsg = document.getElementById('bannerMessage');
            if (bannerMsg) {
                bannerMsg.innerHTML = `<span><b>⚡ Conectado ao Drive:</b> Exibindo <b>${rec.fileName}</b> (${rec.data.length} registros - Atualizado em ${rec.timestamp}).</span>`;
            }

            document.getElementById('dbStatusBadge').innerText = `Status DB: Conectado (${rec.data.length} registros)`;
            document.getElementById('dbStatusBadge').className = "text-xs text-emerald-400 font-semibold";

            applyGlobalFilters();
        }

        function loadFromHistory(selectedVal) {
            const idx = parseInt(selectedVal, 10);
            if (!isNaN(idx)) {
                loadDriveRecord(idx);
            }
        }

        function initApp() {
            updateEmissaoDateTime();
            fetchDatabaseFromDrive();
        }

        document.addEventListener("DOMContentLoaded", function() {
            initApp();
        });

        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, {type: 'array', cellDates: true});

                let osSheetName = workbook.SheetNames.find(s => s.toLowerCase().includes('ordem') || s.toLowerCase().includes('os')) || workbook.SheetNames[0];
                let sheet = workbook.Sheets[osSheetName];
                let json = XLSX.utils.sheet_to_json(sheet);

                if (json.length > 0) {
                    updateEmissaoDateTime();
                    document.getElementById('bannerMessage').innerHTML = 
                        '<span><b>📁 Upload Manual Carregado:</b> ' + file.name + ' (' + json.length + ' registros).</span>';
                    activeData = json;
                    currentKPIFilter = 'ALL';
                    applyGlobalFilters();
                } else {
                    alert("Não foram encontrados dados válidos na planilha.");
                }
            };
            reader.readAsArrayBuffer(file);
        }

        // LÓGICA DOS FILTROS GLOBAIS (COLUNAS D, L, CALENDÁRIO E KPIS)
        function applyGlobalFilters() {
            if (!activeData || !activeData.length) return;

            const termD = (document.getElementById('searchColD').value || '').trim().toLowerCase();
            const termL = (document.getElementById('searchColL').value || '').trim().toLowerCase();
            
            const startVal = document.getElementById('startDateFilter').value;
            const endVal = document.getElementById('endDateFilter').value;
            
            let dtStart = startVal ? new Date(startVal + 'T00:00:00') : null;
            let dtEnd = endVal ? new Date(endVal + 'T23:59:59') : null;

            const filteredData = activeData.filter(item => {
                // Filtro por Coluna D
                if (termD) {
                    let colD = (item['Tipo da Ordem de Serviço'] || item['Tipo'] || item['Descrição'] || item['D'] || '').toString().toLowerCase();
                    if (!colD.includes(termD)) return false;
                }

                // Filtro por Coluna L
                if (termL) {
                    let colL = (item['Observação'] || item['Observacao'] || item['Assunto'] || item['Equipamento'] || item['Técnico'] || item['L'] || '').toString().toLowerCase();
                    if (!colL.includes(termL)) return false;
                }

                // Filtro por Calendário Duplo (Data de Abertura)
                if (dtStart || dtEnd) {
                    let rawDt = item['Data de Abertura'] || item['Data_Abertura'] || item['Data Abertura'] || item['E'] || '';
                    let dtItem = parseDateSmart(rawDt);

                    if (!dtItem) return false;
                    if (dtStart && dtItem.getTime() < dtStart.getTime()) return false;
                    if (dtEnd && dtItem.getTime() > dtEnd.getTime()) return false;
                }

                return true;
            });

            renderDashboard(filteredData);
        }

        function setPresetPeriod(preset) {
            const now = new Date();
            let start = new Date();
            let end = new Date();

            if (preset === 'today') {
                // Hoje
            } else if (preset === '7d') {
                start.setDate(now.getDate() - 7);
            } else if (preset === '30d') {
                start.setDate(now.getDate() - 30);
            } else if (preset === 'thisMonth') {
                start = new Date(now.getFullYear(), now.getMonth(), 1);
                end = new Date(now.getFullYear(), now.getMonth() + 1, 0);
            } else if (preset === 'lastMonth') {
                start = new Date(now.getFullYear(), now.getMonth() - 1, 1);
                end = new Date(now.getFullYear(), now.getMonth(), 0);
            }

            const formatDate = (d) => {
                let month = '' + (d.getMonth() + 1);
                let day = '' + d.getDate();
                let year = d.getFullYear();
                if (month.length < 2) month = '0' + month;
                if (day.length < 2) day = '0' + day;
                return [year, month, day].join('-');
            };

            document.getElementById('startDateFilter').value = formatDate(start);
            document.getElementById('endDateFilter').value = formatDate(end);

            applyGlobalFilters();
        }

        function clearSearchFilters() {
            document.getElementById('searchColD').value = '';
            document.getElementById('searchColL').value = '';
            document.getElementById('startDateFilter').value = '';
            document.getElementById('endDateFilter').value = '';
            currentKPIFilter = 'ALL';
            applyGlobalFilters();
        }

        // FUNÇÃO DE INTERAÇÃO DOS CARDS DE KPI
        function filterByKPI(filterType) {
            if (filterType === 'TECNICOS') {
                document.getElementById('tableTechSection').scrollIntoView({ behavior: 'smooth' });
                const tableElem = document.getElementById('tableTechSection');
                tableElem.classList.add('ring-4', 'ring-emerald-400');
                setTimeout(() => tableElem.classList.remove('ring-4', 'ring-emerald-400'), 2000);
                return;
            }

            if (currentKPIFilter === filterType && filterType !== 'ALL') {
                currentKPIFilter = 'ALL';
            } else {
                currentKPIFilter = filterType;
            }

            applyGlobalFilters();
        }

        function scrollToCompSection(sectionId) {
            const elem = document.getElementById(sectionId);
            if (elem) {
                elem.scrollIntoView({ behavior: 'smooth' });
                elem.classList.add('ring-4', 'ring-blue-400');
                setTimeout(() => elem.classList.remove('ring-4', 'ring-blue-400'), 2000);
            }
        }

        function resetTecnicoSelect() {
            const select = document.getElementById('selectTecnicoComp');
            if (select) {
                select.value = "TODOS";
                updateComparativoCharts();
                scrollToCompSection('tableCompSection');
            }
        }

        function highlightActiveCard() {
            const cards = ['cardTotalOS', 'cardCorretiva', 'cardAtraso', 'cardTecnicos'];
            cards.forEach(id => {
                const el = document.getElementById(id);
                if (el) el.classList.remove('ring-2', 'ring-blue-500', 'ring-amber-500', 'ring-rose-500', 'bg-blue-50/40', 'bg-amber-50/40', 'bg-rose-50/40');
            });

            const badge = document.getElementById('activeFilterBadge');
            const badgeText = document.getElementById('activeFilterText');

            if (currentKPIFilter === 'CORRETIVA') {
                document.getElementById('cardCorretiva').classList.add('ring-2', 'ring-amber-500', 'bg-amber-50/40');
                badge.classList.remove('hidden');
                badgeText.innerText = "Exibindo apenas Manutenção Corretiva (Emergencial)";
            } else if (currentKPIFilter === 'ATRASO') {
                document.getElementById('cardAtraso').classList.add('ring-2', 'ring-rose-500', 'bg-rose-50/40');
                badge.classList.remove('hidden');
                badgeText.innerText = "Exibindo apenas Ordens de Serviço Atrasadas / Fora do SLA";
            } else {
                document.getElementById('cardTotalOS').classList.add('ring-2', 'ring-blue-500', 'bg-blue-50/40');
                badge.classList.add('hidden');
            }
        }

        function renderDashboard(rawData) {
            highlightActiveCard();

            const validTechData = rawData.filter(item => {
                let tech = item['Técnico'] || item['Tecnico'] || '';
                return !isExcludedTech(tech);
            });

            const totalOS = validTechData.length;
            let corretivaCount = 0;
            let atrasoCount = 0;

            validTechData.forEach(item => {
                if (isSLAOverdue(item)) atrasoCount++;
                let tipo = item['Tipo da Ordem de Serviço'] || item['Tipo'] || '';
                if (tipo.toLowerCase().includes('corretiv')) corretivaCount++;
            });

            const pctCorretiva = totalOS > 0 ? ((corretivaCount / totalOS) * 100).toFixed(1) : "0";
            const pctAtraso = totalOS > 0 ? ((atrasoCount / totalOS) * 100).toFixed(1) : "0";

            let displayData = validTechData;
            if (currentKPIFilter === 'CORRETIVA') {
                displayData = validTechData.filter(item => {
                    let tipo = item['Tipo da Ordem de Serviço'] || item['Tipo'] || '';
                    return tipo.toLowerCase().includes('corretiv');
                });
            } else if (currentKPIFilter === 'ATRASO') {
                displayData = validTechData.filter(item => isSLAOverdue(item));
            }

            const statusCount = {};
            const techCount = {};
            const lojaCount = {};

            displayData.forEach(item => {
                let status = item['Status da OS'] || item['Status'] || 'Outros';
                statusCount[status] = (statusCount[status] || 0) + 1;

                let tech = item['Técnico'] || item['Tecnico'] || 'Não Atribuído';
                techCount[tech] = (techCount[tech] || 0) + 1;

                let loja = item['Fantasia Cliente'] || item['Cliente'] || 'Desconhecido';
                lojaCount[loja] = (lojaCount[loja] || 0) + 1;
            });

            const techList = Object.keys(techCount);

            document.getElementById('kpiTotalOS').innerText = totalOS;
            document.getElementById('kpiCorretiva').innerText = pctCorretiva + '%';
            document.getElementById('kpiAtraso').innerText = pctAtraso + '%';
            document.getElementById('kpiTecnicos').innerText = techList.length;

            renderChartStatus(statusCount);
            renderChartTecnicos(techCount);
            renderChartLojas(lojaCount);
            renderChartEquipamentos();
            renderTechTable(techCount, displayData.length);

            populateTecnicoSelect(techCount);
            updateComparativoCharts();
        }

        function populateTecnicoSelect(techCount) {
            const select = document.getElementById('selectTecnicoComp');
            const currentVal = select.value;
            select.innerHTML = '<option value="TODOS">Todos os Técnicos (Ativos)</option>';

            const sortedTechs = Object.keys(techCount).sort();
            sortedTechs.forEach(tech => {
                const opt = document.createElement('option');
                opt.value = tech;
                opt.innerText = tech + ' (' + techCount[tech] + ' OSs)';
                select.appendChild(opt);
            });

            if (sortedTechs.includes(currentVal)) {
                select.value = currentVal;
            } else {
                select.value = "TODOS";
            }
        }

        function renderChartStatus(statusCount) {
            const ctx = document.getElementById('chartStatus').getContext('2d');
            if (chartStatusObj) chartStatusObj.destroy();

            chartStatusObj = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: Object.keys(statusCount),
                    datasets: [{
                        data: Object.values(statusCount),
                        backgroundColor: ['#2563eb', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6', '#64748b']
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { position: 'bottom' } }
                }
            });
        }

        function renderChartTecnicos(techCount) {
            const ctx = document.getElementById('chartTecnicos').getContext('2d');
            if (chartTecnicosObj) chartTecnicosObj.destroy();

            const sorted = Object.entries(techCount).sort((a,b) => b[1] - a[1]).slice(0, 7);

            chartTecnicosObj = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: sorted.map(i => i[0]),
                    datasets: [{
                        label: 'Qtd. OSs',
                        data: sorted.map(i => i[1]),
                        backgroundColor: '#3b82f6'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } }
                }
            });
        }

        function renderChartLojas(lojaCount) {
            const ctx = document.getElementById('chartLojas').getContext('2d');
            if (chartLojasObj) chartLojasObj.destroy();

            const sorted = Object.entries(lojaCount).sort((a,b) => b[1] - a[1]).slice(0, 8);

            chartLojasObj = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: sorted.map(i => i[0].length > 20 ? i[0].substring(0,20)+'...' : i[0]),
                    datasets: [{
                        label: 'OSs Reincidentes',
                        data: sorted.map(i => i[1]),
                        backgroundColor: '#8b5cf6'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } }
                }
            });
        }

        function renderChartEquipamentos() {
            const ctx = document.getElementById('chartEquipamentos').getContext('2d');
            if (chartEquipamentosObj) chartEquipamentosObj.destroy();

            const equipData = {
                'Câmera Dome VIP 1220': 509,
                'Monitor Padrão': 105,
                'Nobreak ATIV 600': 57,
                'HD Interno 4 TB': 52,
                'Switch 24P PoE': 35,
                'Gravador NVD 3316-P': 15
            };

            chartEquipamentosObj = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: Object.keys(equipData),
                    datasets: [{
                        label: 'Unidades Utilizadas',
                        data: Object.values(equipData),
                        backgroundColor: '#10b981'
                    }]
                },
                options: {
                    indexAxis: 'y',
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } }
                }
            });
        }

        function renderTechTable(techCount, totalOS) {
            const tbody = document.getElementById('tableTechBody');
            tbody.innerHTML = '';

            const sorted = Object.entries(techCount).sort((a,b) => b[1] - a[1]);

            sorted.forEach(([tech, count]) => {
                const pct = totalOS > 0 ? ((count / totalOS) * 100).toFixed(1) : "0";
                
                let badgeClass = "bg-blue-100 text-blue-800";
                let badgeLabel = "Carga Normal";
                if (parseFloat(pct) > 40) {
                    badgeClass = "bg-rose-100 text-rose-800";
                    badgeLabel = "Alta Concentração";
                } else if (parseFloat(pct) < 5) {
                    badgeClass = "bg-amber-100 text-amber-800";
                    badgeLabel = "Carga Baixa";
                }

                tbody.innerHTML += 
                    '<tr class="hover:bg-slate-50 transition border-b border-slate-100">' +
                        '<td class="p-3 font-bold text-slate-800">' + tech + '</td>' +
                        '<td class="p-3 text-right font-bold text-blue-600">' + count + ' OSs</td>' +
                        '<td class="p-3 text-right font-semibold">' + pct + '%</td>' +
                        '<td class="p-3 text-center"><span class="text-[11px] font-bold px-2.5 py-1 rounded-lg ' + badgeClass + '">' + badgeLabel + '</span></td>' +
                    '</tr>';
            });
        }

        function updateComparativoCharts() {
            const selectedTech = document.getElementById('selectTecnicoComp').value;
            const selectedRangeSemanal = document.getElementById('selectRangeSemanal').value;

            let activeCompanyData = activeData.filter(item => {
                let tech = item['Técnico'] || item['Tecnico'] || '';
                return !isExcludedTech(tech);
            });

            if (currentKPIFilter === 'CORRETIVA') {
                activeCompanyData = activeCompanyData.filter(item => {
                    let tipo = item['Tipo da Ordem de Serviço'] || item['Tipo'] || '';
                    return tipo.toLowerCase().includes('corretiv');
                });
            } else if (currentKPIFilter === 'ATRASO') {
                activeCompanyData = activeCompanyData.filter(item => isSLAOverdue(item));
            }

            const totalCompanyOS = activeCompanyData.length;

            let techAllData = activeCompanyData;
            if (selectedTech !== "TODOS") {
                techAllData = activeCompanyData.filter(item => {
                    let tech = item['Técnico'] || item['Tecnico'] || '';
                    return tech === selectedTech;
                });
                document.getElementById('compTecnicoLabel').innerText = selectedTech;
            } else {
                document.getElementById('compTecnicoLabel').innerText = "Todos os Técnicos Ativos";
            }

            const monthNames = ['Jan', 'Fev', 'Mar', 'Abr', 'Mai', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez'];
            
            const histMensalMap = {};
            const histSemanalMap = {};

            techAllData.forEach(item => {
                let rawDt = item['Data_Abertura_Str'] || item['Data de Abertura'] || item['Data Abertura'] || item['Data/Hora Prevista de Atendimento'] || '';
                let dt = parseDateSmart(rawDt);

                if (dt) {
                    const ano = dt.getFullYear();
                    const mesIdx = dt.getMonth();
                    const mesSortKey = ano + '-' + String(mesIdx + 1).padStart(2, '0');
                    const mesDisplayLabel = monthNames[mesIdx] + '/' + ano;

                    if (!histMensalMap[mesSortKey]) {
                        histMensalMap[mesSortKey] = { label: mesDisplayLabel, count: 0 };
                    }
                    histMensalMap[mesSortKey].count += 1;

                    const dayOfWeek = dt.getDay();
                    const distToMon = (dayOfWeek + 6) % 7;
                    const monday = new Date(dt);
                    monday.setDate(dt.getDate() - distToMon);

                    const sunday = new Date(monday);
                    sunday.setDate(monday.getDate() + 6);

                    const weekKey = monday.getFullYear() + '-' + String(monday.getMonth()+1).padStart(2,'0') + '-' + String(monday.getDate()).padStart(2,'0');
                    const weekLabel = String(monday.getDate()).padStart(2,'0') + '/' + monthNames[monday.getMonth()] + ' a ' + String(sunday.getDate()).padStart(2,'0') + '/' + monthNames[sunday.getMonth()];

                    if (!histSemanalMap[weekKey]) {
                        histSemanalMap[weekKey] = { label: weekLabel, count: 0, sortDate: monday.getTime() };
                    }
                    histSemanalMap[weekKey].count += 1;
                }
            });

            const numHistMeses = Object.keys(histMensalMap).length || 1;
            const numHistSemanas = Object.keys(histSemanalMap).length || 1;

            const totalTechAllOS = techAllData.length;
            const mediaHistMes = (totalTechAllOS / numHistMeses).toFixed(1);
            const mediaHistSemana = (totalTechAllOS / numHistSemanas).toFixed(1);
            const pctShareTotal = totalCompanyOS > 0 ? ((totalTechAllOS / totalCompanyOS) * 100).toFixed(1) : "0";

            let sortedSemKeys = Object.keys(histSemanalMap).sort((a,b) => histSemanalMap[a].sortDate - histSemanalMap[b].sortDate);
            let displaySemKeys = sortedSemKeys;

            if (selectedRangeSemanal === "CURRENT") {
                if (sortedSemKeys.length > 0) {
                    displaySemKeys = [sortedSemKeys[sortedSemKeys.length - 1]];
                }
                document.getElementById('badgeSemanal').innerText = "Semana Atual (Mais Recente)";
            } else if (selectedRangeSemanal === "PREV") {
                if (sortedSemKeys.length > 1) {
                    displaySemKeys = [sortedSemKeys[sortedSemKeys.length - 2]];
                } else if (sortedSemKeys.length === 1) {
                    displaySemKeys = [sortedSemKeys[0]];
                }
                document.getElementById('badgeSemanal').innerText = "Última Semana (Anterior)";
            } else if (selectedRangeSemanal === "4" && sortedSemKeys.length > 4) {
                displaySemKeys = sortedSemKeys.slice(sortedSemKeys.length - 4);
                document.getElementById('badgeSemanal').innerText = "Últimas 4 Semanas";
            } else if (selectedRangeSemanal === "10" && sortedSemKeys.length > 10) {
                displaySemKeys = sortedSemKeys.slice(sortedSemKeys.length - 10);
                document.getElementById('badgeSemanal').innerText = "Últimas 10 Semanas";
            } else if (selectedRangeSemanal === "16" && sortedSemKeys.length > 16) {
                displaySemKeys = sortedSemKeys.slice(sortedSemKeys.length - 16);
                document.getElementById('badgeSemanal').innerText = "Últimas 16 Semanas";
            } else {
                document.getElementById('badgeSemanal').innerText = "Todas as Semanas (" + sortedSemKeys.length + ")";
            }

            let totalFilteredOS = 0;
            displaySemKeys.forEach(k => {
                totalFilteredOS += histSemanalMap[k] ? histSemanalMap[k].count : 0;
            });

            if (selectedRangeSemanal === "ALL") {
                totalFilteredOS = totalTechAllOS;
            }

            document.getElementById('compTotalOS').innerText = totalFilteredOS;
            document.getElementById('compMediaMes').innerText = mediaHistMes;
            document.getElementById('compMediaSemana').innerText = mediaHistSemana;
            document.getElementById('compPctTotal').innerText = pctShareTotal + '%';

            const sortedMesKeys = Object.keys(histMensalMap).sort();
            const mensalLabels = sortedMesKeys.map(k => histMensalMap[k].label);
            const mensalValues = sortedMesKeys.map(k => histMensalMap[k].count);

            renderChartMensal(mensalLabels, mensalValues);

            const semanalLabels = displaySemKeys.map(k => histSemanalMap[k].label);
            const semanalValues = displaySemKeys.map(k => histSemanalMap[k].count);

            renderChartSemanal(semanalLabels, semanalValues);

            renderTableComp(histMensalMap, totalTechAllOS);
        }

        function renderChartMensal(labels, values) {
            const ctx = document.getElementById('chartMensal').getContext('2d');
            if (chartMensalObj) chartMensalObj.destroy();

            chartMensalObj = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'Atendimentos no Mês',
                        data: values,
                        borderColor: '#2563eb',
                        backgroundColor: 'rgba(37, 99, 235, 0.12)',
                        borderWidth: 3,
                        fill: true,
                        tension: 0.3,
                        pointBackgroundColor: '#1d4ed8',
                        pointRadius: 5
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: true, position: 'top' } },
                    scales: {
                        y: { beginAtZero: true, ticks: { stepSize: 1 } }
                    }
                }
            });
        }

        function renderChartSemanal(labels, values) {
            const ctx = document.getElementById('chartSemanal').getContext('2d');
            if (chartSemanalObj) chartSemanalObj.destroy();

            chartSemanalObj = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'OSs na Semana',
                        data: values,
                        backgroundColor: '#10b981',
                        borderRadius: 6
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { 
                        legend: { display: true, position: 'top' }
                    },
                    scales: {
                        x: {
                            ticks: {
                                font: { size: 10 },
                                maxRotation: 45,
                                minRotation: 0
                            }
                        },
                        y: { beginAtZero: true, ticks: { stepSize: 1 } }
                    }
                }
            });
        }

        function renderTableComp(histMensalMap, totalTechAllOS) {
            const tbody = document.getElementById('tableCompBody');
            tbody.innerHTML = '';

            const keys = Object.keys(histMensalMap).sort();

            if (keys.length === 0) {
                tbody.innerHTML = '<tr><td colspan="4" class="p-4 text-center text-slate-400">Nenhum atendimento encontrado para os filtros selecionados.</td></tr>';
                return;
            }

            keys.forEach(k => {
                const label = histMensalMap[k].label;
                const count = histMensalMap[k].count;
                const pct = totalTechAllOS > 0 ? ((count / totalTechAllOS) * 100).toFixed(1) : "0";
                
                let badgeClass = "bg-blue-100 text-blue-800";
                let badgeLabel = "Produção Normal";
                if (count > 50) {
                    badgeClass = "bg-emerald-100 text-emerald-800";
                    badgeLabel = "Pico de Atendimentos";
                } else if (count < 5) {
                    badgeClass = "bg-amber-100 text-amber-800";
                    badgeLabel = "Baixo Volume";
                }

                tbody.innerHTML += 
                    '<tr class="hover:bg-slate-50 transition border-b border-slate-100">' +
                        '<td class="p-3 font-bold text-slate-800">' + label + '</td>' +
                        '<td class="p-3 text-right font-bold text-blue-600">' + count + ' OSs</td>' +
                        '<td class="p-3 text-right font-semibold">' + pct + '%</td>' +
                        '<td class="p-3 text-center"><span class="text-[11px] font-bold px-2.5 py-1 rounded-lg ' + badgeClass + '">' + badgeLabel + '</span></td>' +
                    '</tr>';
            });
        }

        // FUNÇÃO DE EXPORTAÇÃO EXCEL DA BASE
        function exportToExcel() {
            if (!activeData.length) return;
            const ws = XLSX.utils.json_to_sheet(activeData);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Atendimento Tecnico");
            XLSX.writeFile(wb, "Relatorio_Atendimento_Tecnico.xlsx");
        }

        // FUNÇÃO DE EXPORTAÇÃO PDF
        function exportToPDF() {
            const btnPdf = document.getElementById('btnPdf');
            const originalText = btnPdf.innerHTML;
            btnPdf.innerHTML = '<i class="fa-solid fa-spinner fa-spin text-base"></i> Gerando PDF...';
            btnPdf.disabled = true;

            window.scrollTo(0, 0);

            const element = document.getElementById('pdfContent');
            const opt = {
                margin:       [0.2, 0.2, 0.2, 0.2],
                filename:     'Relatorio_Atendimento_Tecnico_KPIs.pdf',
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

        // LÓGICA DO MODAL DE OBSERVAÇÃO DE TEXTO LONGO
        let activeObsInputId = null;

        function openObsModal(inputId, headerName) {
            const inputEl = document.getElementById(inputId);
            if (!inputEl) return;

            activeObsInputId = inputId;
            document.getElementById('obsModalTitle').innerText = `Editar ${headerName || 'Observação'}`;
            document.getElementById('obsModalTextarea').value = inputEl.value;
            document.getElementById('obsModal').classList.remove('hidden');
            
            setTimeout(() => {
                const txt = document.getElementById('obsModalTextarea');
                txt.focus();
                txt.select();
            }, 50);
        }

        function closeObsModal() {
            activeObsInputId = null;
            document.getElementById('obsModal').classList.add('hidden');
        }

        function saveObsModal() {
            if (!activeObsInputId) return;

            const inputEl = document.getElementById(activeObsInputId);
            if (inputEl) {
                const newVal = document.getElementById('obsModalTextarea').value;
                inputEl.value = newVal;
                inputEl.title = newVal;
            }

            closeObsModal();
        }
    </script>
</body>
</html>
