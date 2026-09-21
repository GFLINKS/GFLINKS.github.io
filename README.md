<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel Links GF </title>
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
                    <i class="fa-solid fa-floppy-disk text-xs"></i> Aplicar e Salvar no Drive
                </button>
            </div>
        </div>
    </div>

    <!-- MENU FLUTUANTE DE FILTRO ESTILO EXCEL -->
    <div id="excelFilterDropdown" class="fixed hidden z-[100] bg-white border border-slate-300 shadow-2xl rounded-xl p-3 w-64 text-xs font-sans text-slate-700" onclick="event.stopPropagation()">
        <!-- Ações de Ordenação -->
        <div class="space-y-1 pb-2 border-b border-slate-200">
            <button onclick="applyColumnSort('asc')" class="w-full text-left px-2.5 py-1.5 hover:bg-slate-100 rounded-md flex items-center gap-2 font-semibold text-slate-700 transition">
                <i class="fa-solid fa-arrow-down-a-z text-indigo-600 text-sm"></i> Classificar de A a Z
            </button>
            <button onclick="applyColumnSort('desc')" class="w-full text-left px-2.5 py-1.5 hover:bg-slate-100 rounded-md flex items-center gap-2 font-semibold text-slate-700 transition">
                <i class="fa-solid fa-arrow-up-z-a text-indigo-600 text-sm"></i> Classificar de Z a A
            </button>
        </div>

        <!-- Limpar Filtro -->
        <div class="py-1.5 border-b border-slate-200">
            <button onclick="clearCurrentColumnFilter()" class="w-full text-left px-2.5 py-1.5 hover:bg-rose-50 rounded-md text-rose-600 flex items-center gap-2 font-semibold transition">
                <i class="fa-solid fa-filter-circle-xmark text-sm"></i> Limpar Filtro de "<span id="excelFilterColName"></span>"
            </button>
        </div>

        <!-- Seleção de Valores Únicos -->
        <div class="pt-2 space-y-2">
            <div class="relative">
                <input type="text" id="excelFilterSearch" oninput="filterExcelUniqueList()" placeholder="Pesquisar..." class="w-full pl-7 pr-2 py-1.5 border border-slate-300 rounded-lg text-xs focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50">
                <i class="fa-solid fa-magnifying-glass absolute left-2.5 top-2.5 text-slate-400 text-[10px]"></i>
            </div>

            <div class="p-1 border border-slate-200 rounded-lg bg-slate-50">
                <label class="flex items-center gap-2 px-2 py-1 border-b border-slate-200 font-bold text-slate-800 cursor-pointer hover:bg-slate-200/60 rounded">
                    <input type="checkbox" id="excelSelectAllCb" onchange="toggleSelectAllExcelList(this.checked)" class="rounded text-indigo-600 focus:ring-indigo-500">
                    <span>(Selecionar Tudo)</span>
                </label>
                <div id="excelFilterList" class="max-h-40 overflow-y-auto space-y-1 p-1 text-[11px]"></div>
            </div>
        </div>

        <!-- Botões de Ação -->
        <div class="flex justify-end gap-2 pt-2.5 border-t border-slate-200 mt-2">
            <button onclick="closeExcelFilterDropdown()" class="px-3 py-1.5 bg-slate-200 hover:bg-slate-300 text-slate-700 font-semibold rounded-lg text-xs transition">Cancelar</button>
            <button onclick="confirmExcelColumnFilter()" class="px-3 py-1.5 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold rounded-lg text-xs transition shadow flex items-center gap-1">
                <i class="fa-solid fa-check text-[10px]"></i> OK
            </button>
        </div>
    </div>

    <!-- MODAL DE CONFIRMAÇÃO DE ALTERAÇÃO (GF01) -->
    <div id="confirmModal" class="fixed inset-0 bg-slate-900/80 z-[60] hidden flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-2xl shadow-2xl p-6 max-w-md w-full text-center space-y-4 border border-slate-200">
            <div class="bg-amber-100 text-amber-600 w-14 h-14 rounded-full flex items-center justify-center mx-auto text-2xl shadow-inner">
                <i class="fa-solid fa-triangle-exclamation"></i>
            </div>
            <div>
                <h3 class="text-lg font-bold text-slate-800">Confirmar Alterações Modificadas</h3>
                <p class="text-xs text-slate-500 mt-1">Para autorizar a gravação dos registros alterados no Google Drive, digite o código de confirmação:</p>
            </div>
            <div>
                <input type="text" id="confirmCodeInput" placeholder="Digite gf01..." class="w-full text-sm px-4 py-2.5 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 text-center font-bold tracking-wider bg-slate-50 uppercase">
                <p id="confirmError" class="text-xs text-rose-600 font-semibold mt-1.5 hidden"><i class="fa-solid fa-circle-xmark mr-1"></i>Código incorreto! Digite gf01 para confirmar.</p>
            </div>
            <div class="flex gap-2">
                <button onclick="cancelarEdicao()" class="w-1/2 bg-slate-200 hover:bg-slate-300 text-slate-700 font-semibold py-2.5 rounded-lg transition text-xs">Cancelar</button>
                <button onclick="validarEExecutarEdicao()" class="w-1/2 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold py-2.5 rounded-lg transition text-xs shadow flex items-center justify-center gap-1.5">
                    <i class="fa-solid fa-check"></i> Confirmar e Salvar
                </button>
            </div>
        </div>
    </div>

    <!-- TELA DE LOGIN / BLOQUEIO POR SENHA FUNCIONAL -->
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
                    <p class="text-[11px] text-slate-400 font-medium mt-0.5" id="lastUpdateBadge">Ultima atualização: Nunca</p>
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

        <!-- Banner de Carregamento do Drive -->
        <div id="driveLoadingBanner" class="hidden bg-sky-50 border-l-4 border-sky-500 text-sky-800 p-4 rounded-xl shadow-sm flex items-center justify-between no-print">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-circle-notch fa-spin text-sky-600 text-xl"></i>
                <div>
                    <p class="text-xs font-bold">Sincronizando com o Google Drive...</p>
                    <p class="text-[11px] text-sky-600">Buscando atualizações da planilha na nuvem (Atualização automática a cada 5 min).</p>
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
                <div class="overflow-x-auto w-full">
                    <table class="w-full min-w-max text-left text-xs border-collapse">
                        <thead>
                            <tr id="bdSearchResultHeader" class="bg-slate-800 text-white font-semibold">
                                <th class="p-2.5 bg-slate-800 text-white font-bold border-b border-slate-700">CI / CÓDIGO</th>
                                <th class="p-2.5 bg-slate-800 text-white font-bold border-b border-slate-700">SIGLA</th>
                                <th class="p-2.5 bg-slate-800 text-white font-bold border-b border-slate-700">RESTAURANTE / UNIDADE</th>
                                <th class="p-2.5 bg-slate-800 text-white font-bold border-b border-slate-700">ENDEREÇO COMPLETO</th>
                                <th class="p-2.5 bg-slate-800 text-white font-bold border-b border-slate-700">UF</th>
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
            <p class="text-xs text-slate-500 mt-1">Os dados do Painel Links GF são atualizados e salvos automaticamente a cada 5 minutos.</p>
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
                
                <!-- CONTAINER GERAL DOS CARDS DE COBRANÇA -->
                <div id="cobrancaCardsContainer" class="space-y-4"></div>

                <!-- BOTÃO DE EXPANSÃO / RECOLHIMENTO -->
                <div class="mt-4 pt-3 border-t border-slate-100 flex justify-center no-print">
                    <button id="btnToggleCobrancaCards" onclick="toggleCobrancaCards()" class="text-xs font-bold text-indigo-600 hover:text-indigo-800 bg-indigo-50 hover:bg-indigo-100 px-4 py-2 rounded-lg transition flex items-center gap-2 border border-indigo-200 shadow-sm">
                        <i id="toggleCobrancaIcon" class="fa-solid fa-chevron-down text-xs"></i>
                        <span id="toggleCobrancaText">Ver outros cards de cobrança</span>
                    </button>
                </div>
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
                        <p class="text-xs text-slate-500">Clique nos ícones de filtro nos cabeçalhos das colunas para ordenar (A-Z) e selecionar valores únicos.</p>
                    </div>

                    <div class="flex flex-wrap gap-2 items-center w-full lg:w-auto justify-end no-print">
                        <!-- INDICADOR VISUAL DA ÚLTIMA ALTERAÇÃO NO BD -->
                        <span id="lastSaveIndicator" class="text-[11px] font-medium px-2.5 py-1.5 rounded-lg border bg-slate-100 text-slate-600 border-slate-200 flex items-center gap-1.5 transition-all">
                            <i class="fa-solid fa-clock-rotate-left text-indigo-500"></i>
                            <span>Última alteração no BD: Nunca</span>
                        </span>

                        <button onclick="solicitarSalvarEmMassa()" id="btnSaveBatch" class="text-xs bg-emerald-600 hover:bg-emerald-500 disabled:opacity-50 text-white font-bold px-4 py-2 rounded-lg transition shadow-md flex items-center gap-1.5">
                            <i class="fa-solid fa-floppy-disk text-sm"></i> Salvar Alterações em Massa
                        </button>

                        <input type="text" id="tableSearchInput" onkeyup="filterTable()" placeholder="Buscar..." class="text-xs px-3 py-2 border border-slate-300 rounded-lg bg-white">
                        <select id="filterCisNumNI" onchange="filterTable()" class="text-xs bg-amber-50 border border-amber-300 text-amber-900 rounded-lg px-3 py-2 font-bold">
                            <option value="">Filtro Especial: Todos</option>
                            <option value="ONLY_NUMERIC_NI">CIS Numérica + Cobrança N/I</option>
                            <option value="ONLY_DUPLICATED_CIS">Duplicidade de Link na CIS (Apenas Números)</option>
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

                <div class="overflow-x-auto overflow-y-auto max-h-[600px] w-full">
                    <table class="min-w-max w-full text-left border-collapse text-[11px]">
                        <thead>
                            <tr id="tableHeader" class="bg-slate-800 text-white font-semibold sticky top-0 z-20"></tr>
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
        const APPS_SCRIPT_WEBAPP_URL = "https://script.google.com/macros/s/AKfycbzoYrxjqF6OgzsZ3_ieKAM7JmS9qyVDIOJOzVqApwws0Myy8f5KAeTfJjjbKB6E_WCFJw/exec";

        const DB_KEY_DATA = 'APP_ATIVOS_DATA';
        const DB_KEY_HEADERS = 'APP_ATIVOS_HEADERS';
        const DB_KEY_AUX = 'APP_BD_AUXILIAR';
        const DB_KEY_AUX_HEADERS = 'APP_BD_AUXILIAR_HEADERS';
        const DB_KEY_AUX_DATA = 'APP_BD_AUXILIAR_DATA';
        const DB_KEY_LAST_SAVE = 'GF_LAST_BD_SAVE_TIMESTAMP';

        // LISTAS OFICIAIS ATUALIZADAS E CORRIGIDAS CONFORME REGRAS DO GOOGLE SHEETS
        const OPCOES_COBRANCA = [
            "ATIVA", "SUSPENSA", "N/I", "GUARDIAN", "CORPORATIVO", "ESTOQUE", 
            "CANCELADA", "INATIVO", "DESATIVADA", "COBRANDO", "COBRAR NA PROXIMA MEDIÇÃO", "NÃO COBRANDO"
        ];
        const OPCOES_STATUS = [
            "OK", "CANCELADA", "SUSPENSA", "EM ANALISE", "DUPLICIDADE", "EM CANCELAMENTO", 
            "MANUTENCAO", "TRANSFERENCIA CNPJ", "TRANSFERENCIA ENDERECO", "SUBSTITUIÇÃO", 
            "PENDENTE*", "DESATIVADOS"
        ];
        const OPCOES_OPERADORA = ["VIVO", "CLARO", "TIM", "OI", "ALGAR", "OUTROS", "N/I"];

        let rawAtivosData = [];
        let excelHeaders = [];
        let bdAuxiliarMap = new Map();
        let bdAuxiliarHeaders = [];
        let bdAuxiliarData = [];
        let chartStatusObj, chartCobrancaObj;
        let statusColName = "";
        let cobrancaColName = "";
        let driveTimer = null;

        let pendingEdit = null;

        // ESTADOS DOS AUTOFILTROS ESTILO EXCEL
        let activeColumnFilters = {}; 
        let currentSort = { colHeader: null, direction: null }; 
        let activeDropdownCol = null;

        // ESTADO DO MODAL DE OBSERVAÇÃO
        let activeObsInputId = null;

        window.addEventListener('DOMContentLoaded', () => {
            if (sessionStorage.getItem(AUTH_KEY) === 'true') {
                document.getElementById('loginOverlay').classList.add('hidden');
                initApp();
            }

            window.addEventListener('click', (e) => {
                const dropdown = document.getElementById('excelFilterDropdown');
                if (dropdown && !dropdown.classList.contains('hidden')) {
                    const isClickInside = e.target.closest('#excelFilterDropdown');
                    const isFilterBtn = e.target.closest('.excel-filter-btn');
                    if (!isClickInside && !isFilterBtn) {
                        dropdown.classList.add('hidden');
                    }
                }
            });
        });

        // FUNÇÃO DE EXPANSAO / RECOLHIMENTO DOS CARDS SECUNDÁRIOS DE COBRANÇA
        function toggleCobrancaCards() {
            const secondaryContainer = document.getElementById('cobrancaSecondaryCardsContainer');
            const icon = document.getElementById('toggleCobrancaIcon');
            const text = document.getElementById('toggleCobrancaText');

            if (!secondaryContainer) return;

            if (secondaryContainer.classList.contains('hidden')) {
                secondaryContainer.classList.remove('hidden');
                if (icon) icon.className = 'fa-solid fa-chevron-up text-xs';
                if (text) text.innerText = 'Ocultar outros cards de cobrança';
            } else {
                secondaryContainer.classList.add('hidden');
                if (icon) icon.className = 'fa-solid fa-chevron-down text-xs';
                if (text) text.innerText = 'Ver outros cards de cobrança';
            }
        }

        // LÓGICA DO MODAL DE OBSERVAÇÃO / TEXTO LONGO
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

        // SALVAMENTO INDIVIDUAL COM CONFIRMAÇÃO (GF01)
        function saveObsModal() {
            if (!activeObsInputId) return;

            const inputEl = document.getElementById(activeObsInputId);
            if (!inputEl) {
                closeObsModal();
                return;
            }

            const newVal = document.getElementById('obsModalTextarea').value;
            const originalVal = inputEl.dataset.original || "";

            // Atualiza o valor visual na célula da tabela
            inputEl.value = newVal;
            inputEl.title = newVal;

            // Se houve alteração no conteúdo, solicita senha e grava individualmente no Drive
            if (newVal !== originalVal) {
                const tr = inputEl.closest('tr');
                if (tr) {
                    const excelRowIndex = parseInt(tr.dataset.excelRowIndex, 10);
                    const parts = activeObsInputId.split('_col_');
                    const colIdx = parseInt(parts[1], 10);
                    const header = excelHeaders[colIdx];

                    if (!isNaN(excelRowIndex) && !isNaN(colIdx) && header) {
                        const batch = [{
                            rowIndex: excelRowIndex,
                            changes: [{
                                colIndex: colIdx + 1,
                                header: header,
                                value: newVal
                            }]
                        }];

                        pendingEdit = { batch: batch };

                        closeObsModal();

                        // Abre o modal de confirmação por senha (gf01)
                        document.getElementById('confirmCodeInput').value = '';
                        document.getElementById('confirmError').classList.add('hidden');
                        document.getElementById('confirmModal').classList.remove('hidden');
                        document.getElementById('confirmCodeInput').focus();
                        return;
                    }
                }
            }

            closeObsModal();
        }

        function checkPassword(e) {
            e.preventDefault();
            const input = document.getElementById('accessPassword').value.trim();
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

        function updateSyncTimestamp() {
            const now = new Date();
            const dateStr = now.toLocaleDateString('pt-BR');
            const timeStr = now.toLocaleTimeString('pt-BR');
            const badge = document.getElementById('lastUpdateBadge');
            if (badge) {
                badge.innerText = `Ultima atualização: ${dateStr} às ${timeStr}`;
            }
        }

        function updateSaveIndicator(text, iconClass = "fa-clock-rotate-left text-indigo-500", bgClass = "bg-slate-100 text-slate-600 border-slate-200") {
            const el = document.getElementById('lastSaveIndicator');
            if (el) {
                el.className = `text-[11px] font-medium px-2.5 py-1.5 rounded-lg border flex items-center gap-1.5 transition-all ${bgClass}`;
                el.innerHTML = `<i class="fa-solid ${iconClass}"></i><span>${text}</span>`;
            }
        }

        function initApp() {
            loadFromDatabase();
            syncDriveData();

            const storedLastSave = localStorage.getItem(DB_KEY_LAST_SAVE);
            if (storedLastSave) {
                updateSaveIndicator(`Última alteração no BD: ${storedLastSave}`, "fa-circle-check text-emerald-600", "bg-emerald-50 text-emerald-800 border-emerald-300");
            }

            if (driveTimer) clearInterval(driveTimer);
            driveTimer = setInterval(() => {
                syncDriveData();
            }, 300000);
        }

        function logout() {
            if (driveTimer) clearInterval(driveTimer);
            sessionStorage.removeItem(AUTH_KEY);
            document.getElementById('accessPassword').value = '';
            document.getElementById('loginOverlay').classList.remove('hidden');
        }

        async function syncDriveData() {
            const banner = document.getElementById('driveLoadingBanner');
            const syncIcon = document.getElementById('syncIcon');

            if (banner) banner.classList.remove('hidden');
            if (syncIcon) syncIcon.classList.add('fa-spin');

            const downloadUrl = `https://docs.google.com/spreadsheets/d/${DRIVE_FILE_ID}/export?format=xlsx`;

            try {
                const response = await fetch(downloadUrl);
                if (!response.ok) throw new Error('Falha ao carregar arquivo do Google Drive');

                const arrayBuffer = await response.arrayBuffer();
                const workbook = XLSX.read(new Uint8Array(arrayBuffer), { type: 'array', cellDates: true, dateNF: 'dd/mm/yyyy' });
                parseWorkbook(workbook);
                updateSyncTimestamp();
            } catch (err) {
                console.warn("Automação do Drive concluída ou mantendo base local:", err);
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

        function toInputDate(valStr) {
            if (!valStr || valStr === "-") return "";
            if (/^\d{4}-\d{2}-\d{2}$/.test(valStr)) return valStr;
            const parts = valStr.split('/');
            if (parts.length === 3) return `${parts[2]}-${parts[1].padStart(2, '0')}-${parts[0].padStart(2, '0')}`;
            return "";
        }

        function fromInputDate(valStr) {
            if (!valStr) return "-";
            const parts = valStr.split('-');
            if (parts.length === 3) return `${parts[2]}/${parts[1]}/${parts[0]}`;
            return valStr;
        }

        function getEnderecoStartIndex() {
            let idx = excelHeaders.findIndex(h => {
                const u = h.toUpperCase();
                return u.includes("ENDEREÇO") || u.includes("ENDERECO");
            });
            return idx !== -1 ? idx : 4;
        }

        function getEditorType(headerName) {
            const u = String(headerName || "").toUpperCase();
            if (u.includes("STATUS")) return "STATUS_SELECT";
            if (u.includes("COBRANÇ") || u.includes("COBRANC")) return "COBRANCA_SELECT";
            if (u.includes("OPERADORA") || u.includes("PROVEDOR")) return "OPERADORA_SELECT";
            if (u.includes("DATA") || u.includes("VENCIMENTO") || u.includes("ATIVAC") || u.includes("DT_") || u.includes("DT ")) return "DATE_INPUT";
            return "TEXT_INPUT";
        }

        function buildSelectHtml(inputId, rawVal, optionsList) {
            const currentUpper = cleanStr(rawVal).toUpperCase();
            let hasMatch = false;

            let optionsHtml = optionsList.map(opt => {
                const isSelected = (currentUpper === opt.toUpperCase());
                if (isSelected) hasMatch = true;
                return `<option value="${opt}" ${isSelected ? 'selected' : ''}>${opt}</option>`;
            }).join('');

            if (currentUpper && currentUpper !== "-" && !hasMatch) {
                optionsHtml += `<option value="${rawVal}" selected>${rawVal}</option>`;
            }

            return `<select id="${inputId}" data-original="${rawVal}" class="text-[11px] bg-white border border-slate-300 rounded px-2 py-1 font-semibold text-slate-700 focus:ring-1 focus:ring-indigo-500">${optionsHtml}</select>`;
        }

        function saveToDatabase() {
            try {
                localStorage.setItem(DB_KEY_DATA, JSON.stringify(rawAtivosData));
                localStorage.setItem(DB_KEY_HEADERS, JSON.stringify(excelHeaders));
                localStorage.setItem(DB_KEY_AUX, JSON.stringify(Array.from(bdAuxiliarMap.entries())));
                localStorage.setItem(DB_KEY_AUX_HEADERS, JSON.stringify(bdAuxiliarHeaders));
                localStorage.setItem(DB_KEY_AUX_DATA, JSON.stringify(bdAuxiliarData));
                updateDbBadge(true);
            } catch (err) {
                console.error("Erro ao salvar no banco local:", err);
            }
        }

        function loadFromDatabase() {
            const storedData = localStorage.getItem(DB_KEY_DATA);
            const storedHeaders = localStorage.getItem(DB_KEY_HEADERS);
            const storedAux = localStorage.getItem(DB_KEY_AUX);
            const storedAuxHeaders = localStorage.getItem(DB_KEY_AUX_HEADERS);
            const storedAuxData = localStorage.getItem(DB_KEY_AUX_DATA);

            if (storedData && storedHeaders) {
                rawAtivosData = JSON.parse(storedData);
                excelHeaders = JSON.parse(storedHeaders);
                if (storedAux) bdAuxiliarMap = new Map(JSON.parse(storedAux));
                if (storedAuxHeaders) bdAuxiliarHeaders = JSON.parse(storedAuxHeaders);
                if (storedAuxData) bdAuxiliarData = JSON.parse(storedAuxData);

                if (excelHeaders.length >= 5) cobrancaColName = excelHeaders[4];
                if (excelHeaders.length >= 6) statusColName = excelHeaders[5];

                document.getElementById('dropZone').classList.add('hidden');
                document.getElementById('dashboardSection').classList.remove('hidden');
                document.getElementById('btnExport').disabled = false;
                document.getElementById('btnPdf').disabled = false;

                document.getElementById('bdStatusText').innerText = `Base BD_Auxiliar carregada do banco local (${bdAuxiliarData.length || bdAuxiliarMap.size} cadastros, ${bdAuxiliarHeaders.length} colunas).`;

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
                localStorage.removeItem(DB_KEY_AUX_HEADERS);
                localStorage.removeItem(DB_KEY_AUX_DATA);
                localStorage.removeItem(DB_KEY_LAST_SAVE);

                rawAtivosData = [];
                excelHeaders = [];
                bdAuxiliarMap.clear();
                bdAuxiliarHeaders = [];
                bdAuxiliarData = [];
                activeColumnFilters = {};
                currentSort = { colHeader: null, direction: null };

                document.getElementById('dashboardSection').classList.add('hidden');
                document.getElementById('dropZone').classList.remove('hidden');
                document.getElementById('btnExport').disabled = true;
                document.getElementById('btnPdf').disabled = true;
                document.getElementById('excelFile').value = '';
                document.getElementById('bdStatusText').innerText = "Pesquise por Código CIS, Sigla, Unidade ou Endereço cadastrado na aba BD_Auxiliar.";
                document.getElementById('lastUpdateBadge').innerText = "Ultima atualização: Nunca";
                updateSaveIndicator("Última alteração no BD: Nunca");

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
                updateSyncTimestamp();
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
            bdAuxiliarHeaders = [];
            bdAuxiliarData = [];

            if (sheetBD) {
                const rowsBD = XLSX.utils.sheet_to_json(sheetBD, { defval: "", cellDates: true });
                if (rowsBD.length > 0) {
                    bdAuxiliarHeaders = Object.keys(rowsBD[0]);
                    rowsBD.forEach(r => {
                        const isBDRowEmpty = Object.values(r).every(v => v === undefined || v === null || String(v).trim() === "");
                        if (isBDRowEmpty) return;

                        const formattedRow = {};
                        Object.keys(r).forEach(k => {
                            formattedRow[k] = formatValue(r[k]);
                        });

                        const ci = cleanStr(r["CIS"] || r["CI"] || r["CÓDIGO"] || r["CODIGO"] || r["CÓDIGO CIS"] || (bdAuxiliarHeaders[0] ? r[bdAuxiliarHeaders[0]] : ""));
                        const sigla = cleanStr(r["SIGLA"] || r["Sigla"] || (bdAuxiliarHeaders[1] ? r[bdAuxiliarHeaders[1]] : ""));
                        const nome = cleanStr(r["NOME DO RESTAURANTE"] || r["RESTAURANTE"] || r["UNIDADE"] || r["NOME"] || (bdAuxiliarHeaders[2] ? r[bdAuxiliarHeaders[2]] : ""));
                        const endereco = cleanStr(r["ENDEREÇO"] || r["ENDERECO"] || r["LOGRADOURO"] || (bdAuxiliarHeaders[3] ? r[bdAuxiliarHeaders[3]] : ""));
                        const uf = cleanStr(r["ESTADO"] || r["UF"] || (bdAuxiliarHeaders[4] ? r[bdAuxiliarHeaders[4]] : ""));

                        const entry = { ci, sigla, nome, endereco, uf, rawRow: formattedRow };
                        bdAuxiliarData.push(entry);

                        if (ci) bdAuxiliarMap.set(ci.toUpperCase(), entry);
                        if (sigla) bdAuxiliarMap.set(sigla.toUpperCase(), entry);
                    });
                }
                document.getElementById('bdStatusText').innerText = `Base BD_Auxiliar carregada com ${bdAuxiliarData.length} cadastros (${bdAuxiliarHeaders.length} colunas).`;
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

                    jsonWithHeaders.forEach((row, index) => {
                        const isRowEmpty = Object.values(row).every(v => v === undefined || v === null || String(v).trim() === "");
                        if (isRowEmpty) return;

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
                            excelRowIndex: index + 2,
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

        // CABEÇALHOS COM BOTÕES DE FILTRO/ORDENAÇÃO ESTILO EXCEL
        function renderTableHeaders() {
            const headerRow = document.getElementById('tableHeader');
            headerRow.innerHTML = '';

            excelHeaders.forEach((h) => {
                const th = document.createElement('th');
                const isFiltered = activeColumnFilters[h] && activeColumnFilters[h].size > 0;
                const isSorted = currentSort.colHeader === h;

                th.className = `p-3 whitespace-nowrap bg-slate-800 text-white font-bold border-b border-slate-700 select-none ${isFiltered ? 'bg-slate-700' : ''}`;
                
                let sortBadge = "";
                if (isSorted) {
                    sortBadge = currentSort.direction === 'asc' ? '<i class="fa-solid fa-arrow-down-a-z text-amber-400 ml-1"></i>' : '<i class="fa-solid fa-arrow-up-z-a text-amber-400 ml-1"></i>';
                }

                let filterBtnClass = isFiltered ? "text-emerald-400 bg-emerald-950/60" : "text-slate-400 hover:text-white hover:bg-slate-700";

                th.innerHTML = `
                    <div class="flex items-center justify-between gap-2">
                        <span>${h.toUpperCase()}${sortBadge}</span>
                        <button onclick="openExcelFilterDropdown(event, '${h.replace(/'/g, "\\'")}')" class="excel-filter-btn p-1.5 rounded transition ${filterBtnClass}" title="Filtrar/Ordenar ${h}">
                            <i class="fa-solid fa-filter text-xs"></i>
                        </button>
                    </div>
                `;
                headerRow.appendChild(th);
            });
        }

        function openExcelFilterDropdown(event, colHeader) {
            if (event) {
                event.preventDefault();
                event.stopPropagation();
            }
            activeDropdownCol = colHeader;

            const dropdown = document.getElementById('excelFilterDropdown');
            document.getElementById('excelFilterColName').innerText = colHeader;
            document.getElementById('excelFilterSearch').value = '';

            const btn = event ? event.currentTarget : null;
            if (btn) {
                const rect = btn.getBoundingClientRect();
                dropdown.style.top = `${rect.bottom + 4}px`;
                dropdown.style.left = `${Math.max(10, Math.min(rect.left, window.innerWidth - 270))}px`;
            }

            const uniqueSet = new Set();
            rawAtivosData.forEach(item => {
                const val = String(item.originalRow[colHeader] || "-").trim();
                uniqueSet.add(val || "-");
            });

            const uniqueList = Array.from(uniqueSet).sort((a, b) => a.localeCompare(b, 'pt-BR', { numeric: true }));

            const selectedSet = activeColumnFilters[colHeader];
            const listContainer = document.getElementById('excelFilterList');
            listContainer.innerHTML = '';

            let allChecked = !selectedSet;

            uniqueList.forEach((val) => {
                const isChecked = !selectedSet || selectedSet.has(val);
                const label = document.createElement('label');
                label.className = 'flex items-center gap-2 px-2 py-1 hover:bg-slate-200/60 rounded cursor-pointer text-slate-700 excel-item-label';
                label.dataset.val = val.toLowerCase();
                label.innerHTML = `
                    <input type="checkbox" value="${val.replace(/"/g, '&quot;')}" ${isChecked ? 'checked' : ''} class="excel-val-cb rounded text-indigo-600 focus:ring-indigo-500">
                    <span class="truncate">${val}</span>
                `;
                listContainer.appendChild(label);
            });

            document.getElementById('excelSelectAllCb').checked = allChecked || (selectedSet && selectedSet.size === uniqueList.length);
            dropdown.classList.remove('hidden');
        }

        function closeExcelFilterDropdown() {
            document.getElementById('excelFilterDropdown').classList.add('hidden');
        }

        function filterExcelUniqueList() {
            const query = document.getElementById('excelFilterSearch').value.toLowerCase();
            const labels = document.querySelectorAll('#excelFilterList .excel-item-label');
            labels.forEach(label => {
                if (label.dataset.val.includes(query)) {
                    label.classList.remove('hidden');
                } else {
                    label.classList.add('hidden');
                }
            });
        }

        function toggleSelectAllExcelList(checked) {
            const checkboxes = document.querySelectorAll('#excelFilterList .excel-val-cb');
            checkboxes.forEach(cb => {
                if (!cb.parentElement.classList.contains('hidden')) {
                    cb.checked = checked;
                }
            });
        }

        function applyColumnSort(dir) {
            if (!activeDropdownCol) return;
            currentSort = { colHeader: activeDropdownCol, direction: dir };
            renderTableHeaders();
            filterTable();
            closeExcelFilterDropdown();
        }

        function clearCurrentColumnFilter() {
            if (!activeDropdownCol) return;
            delete activeColumnFilters[activeDropdownCol];
            renderTableHeaders();
            filterTable();
            closeExcelFilterDropdown();
        }

        function confirmExcelColumnFilter() {
            if (!activeDropdownCol) return;

            const checkboxes = document.querySelectorAll('#excelFilterList .excel-val-cb');
            const totalBoxes = checkboxes.length;
            const selectedValues = new Set();

            checkboxes.forEach(cb => {
                if (cb.checked) {
                    selectedValues.add(cb.value);
                }
            });

            if (selectedValues.size === totalBoxes) {
                delete activeColumnFilters[activeDropdownCol];
            } else {
                activeColumnFilters[activeDropdownCol] = selectedValues;
            }

            renderTableHeaders();
            filterTable();
            closeExcelFilterDropdown();
        }

        function renderBdSearchResultHeaders() {
            const headerRow = document.getElementById('bdSearchResultHeader');
            if (!headerRow) return;
            headerRow.innerHTML = '';
            
            const headersToUse = bdAuxiliarHeaders.length > 0 ? bdAuxiliarHeaders : ["CI / CÓDIGO", "SIGLA", "RESTAURANTE / UNIDADE", "ENDEREÇO COMPLETO", "UF"];
            
            headersToUse.forEach(h => {
                const th = document.createElement('th');
                th.className = 'p-2.5 whitespace-nowrap bg-slate-800 text-white font-bold border-b border-slate-700';
                th.innerText = String(h).toUpperCase();
                headerRow.appendChild(th);
            });
        }

        function searchBDByCIS() {
            const query = document.getElementById('bdSearchInput').value.trim().toUpperCase();
            const resultBox = document.getElementById('bdSearchResult');
            const tbody = document.getElementById('bdSearchResultBody');

            if (!query) { resultBox.classList.add('hidden'); return; }

            const matches = [];
            const seen = new Set();
            const dataToSearch = bdAuxiliarData.length > 0 ? bdAuxiliarData : Array.from(bdAuxiliarMap.values());

            for (let i = 0; i < dataToSearch.length; i++) {
                const val = dataToSearch[i];
                const uniqueKey = `${val.ci}_${val.sigla}_${i}`;
                if (seen.has(uniqueKey)) continue;

                const ciStr = String(val.ci || '').trim().toUpperCase();
                const siglaStr = String(val.sigla || '').trim().toUpperCase();
                
                const allRowStr = val.rawRow 
                    ? Object.values(val.rawRow).map(v => String(v).toUpperCase()).join(' ')
                    : `${ciStr} ${siglaStr} ${String(val.nome || '').toUpperCase()} ${String(val.endereco || '').toUpperCase()} ${String(val.uf || '').toUpperCase()}`;

                let priority = -1;

                if (ciStr === query || siglaStr === query) {
                    priority = 1;
                } else if (ciStr.startsWith(query) || siglaStr.startsWith(query)) {
                    priority = 2;
                } else if (ciStr.includes(query) || siglaStr.includes(query)) {
                    priority = 3;
                } else if (allRowStr.includes(query)) {
                    priority = 4;
                }

                if (priority > 0) {
                    seen.add(uniqueKey);
                    const ciNum = parseInt(ciStr, 10);
                    matches.push({
                        item: val,
                        priority: priority,
                        ciNum: isNaN(ciNum) ? 999999 : ciNum
                    });
                }
            }

            matches.sort((a, b) => {
                if (a.priority !== b.priority) return a.priority - b.priority;
                return a.ciNum - b.ciNum;
            });

            tbody.innerHTML = '';
            renderBdSearchResultHeaders();

            if (matches.length === 0) {
                const colSpan = bdAuxiliarHeaders.length || 5;
                tbody.innerHTML = `<tr><td colspan="${colSpan}" class="p-3 text-center text-slate-400">Nenhum registro encontrado.</td></tr>`;
            } else {
                matches.slice(0, 25).forEach(({ item: m }) => {
                    const tr = document.createElement('tr');
                    tr.className = 'hover:bg-slate-50 transition border-b border-slate-100';

                    if (bdAuxiliarHeaders.length > 0 && m.rawRow) {
                        bdAuxiliarHeaders.forEach(col => {
                            const td = document.createElement('td');
                            td.className = 'p-2.5 whitespace-nowrap font-medium';
                            td.innerText = (m.rawRow[col] !== undefined && m.rawRow[col] !== null && m.rawRow[col] !== "") ? m.rawRow[col] : "-";
                            tr.appendChild(td);
                        });
                    } else {
                        tr.innerHTML = `<td class="p-2.5 font-bold">${m.ci || '-'}</td><td class="p-2.5 font-bold text-indigo-600">${m.sigla || '-'}</td><td class="p-2.5">${m.nome || '-'}</td><td class="p-2.5">${m.endereco || '-'}</td><td class="p-2.5">${m.uf || '-'}</td>`;
                    }
                    tbody.appendChild(tr);
                });
            }
            resultBox.classList.remove('hidden');
        }

        function solicitarSalvarEmMassa() {
            const startIdx = getEnderecoStartIndex();
            const batch = [];
            const trList = document.querySelectorAll('#ativosTableBody tr');

            if (trList.length === 0) {
                alert("Nenhum registro exibido na tabela para salvar.");
                return;
            }

            trList.forEach((tr) => {
                const excelRowIndex = parseInt(tr.dataset.excelRowIndex, 10);
                const rowIdx = tr.dataset.rowIdx;
                if (isNaN(excelRowIndex)) return;

                const changes = [];
                excelHeaders.forEach((header, colIdx) => {
                    if (colIdx >= startIdx) {
                        const inputEl = document.getElementById(`input_row_${rowIdx}_col_${colIdx}`);
                        if (inputEl) {
                            let val = inputEl.value;
                            let originalVal = inputEl.dataset.original || "";

                            if (val !== originalVal) {
                                let valueToSend = val;
                                if (inputEl.type === 'date') {
                                    valueToSend = fromInputDate(val);
                                }
                                changes.push({
                                    colIndex: colIdx + 1,
                                    header: header,
                                    value: valueToSend
                                });
                            }
                        }
                    }
                });

                if (changes.length > 0) {
                    batch.push({
                        rowIndex: excelRowIndex,
                        changes: changes
                    });
                }
            });

            if (batch.length === 0) {
                alert("Nenhuma alteração foi detectada para salvar.");
                return;
            }

            pendingEdit = { batch: batch };

            document.getElementById('confirmCodeInput').value = '';
            document.getElementById('confirmError').classList.add('hidden');
            document.getElementById('confirmModal').classList.remove('hidden');
            document.getElementById('confirmCodeInput').focus();
        }

        function cancelarEdicao() {
            pendingEdit = null;
            document.getElementById('confirmModal').classList.add('hidden');
        }

        function validarEExecutarEdicao() {
            const codeInput = document.getElementById('confirmCodeInput').value.trim().toLowerCase();
            const errorMsg = document.getElementById('confirmError');

            if (codeInput === 'gf01') {
                document.getElementById('confirmModal').classList.add('hidden');
                if (pendingEdit && pendingEdit.batch) {
                    executarSalvarEmMassaAppsScript(pendingEdit.batch);
                }
            } else {
                errorMsg.classList.remove('hidden');
            }
        }

        async function executarSalvarEmMassaAppsScript(batch) {
            const btnSave = document.getElementById('btnSaveBatch');
            if (btnSave) btnSave.disabled = true;

            updateSaveIndicator("Salvando alterações no BD...", "fa-spinner fa-spin text-amber-600", "bg-amber-50 text-amber-800 border-amber-300");

            try {
                await fetch(APPS_SCRIPT_WEBAPP_URL, {
                    method: "POST",
                    mode: "no-cors",
                    headers: {
                        "Content-Type": "text/plain;charset=utf-8"
                    },
                    body: JSON.stringify({ batch: batch })
                });

                const now = new Date();
                const dateStr = now.toLocaleDateString('pt-BR');
                const timeStr = now.toLocaleTimeString('pt-BR');
                const timestampStr = `${dateStr} às ${timeStr}`;

                localStorage.setItem(DB_KEY_LAST_SAVE, timestampStr);

                updateSaveIndicator(`Última alteração no BD: ${timestampStr}`, "fa-circle-check text-emerald-600", "bg-emerald-50 text-emerald-800 border-emerald-300");

                setTimeout(() => {
                    syncDriveData();
                }, 4000);
            } catch (err) {
                updateSaveIndicator("Erro ao salvar no BD", "fa-circle-xmark text-rose-600", "bg-rose-50 text-rose-800 border-rose-300");
            } finally {
                if (btnSave) btnSave.disabled = false;
            }
        }

        // RENDERIZAÇÃO DA TABELA COM BOTÃO DE EXPANSÃO PARA INPUTS DE TEXTO
        function renderTableBody(data) {
            const tbody = document.getElementById('ativosTableBody');
            tbody.innerHTML = '';

            const startIdx = getEnderecoStartIndex();

            data.forEach((item, rowIdx) => {
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 transition border-b border-slate-100';
                tr.dataset.excelRowIndex = item.excelRowIndex;
                tr.dataset.rowIdx = rowIdx;

                excelHeaders.forEach((header, colIdx) => {
                    const td = document.createElement('td');
                    td.className = 'p-3 whitespace-nowrap font-medium';

                    const rawVal = item.originalRow[header] || "";
                    const editorType = getEditorType(header);
                    const inputId = `input_row_${rowIdx}_col_${colIdx}`;

                    if (colIdx >= startIdx) {
                        if (editorType === "COBRANCA_SELECT") {
                            td.innerHTML = buildSelectHtml(inputId, rawVal, OPCOES_COBRANCA);
                        } else if (editorType === "STATUS_SELECT") {
                            td.innerHTML = buildSelectHtml(inputId, rawVal, OPCOES_STATUS);
                        } else if (editorType === "OPERADORA_SELECT") {
                            td.innerHTML = buildSelectHtml(inputId, rawVal, OPCOES_OPERADORA);
                        } else if (editorType === "DATE_INPUT") {
                            const formattedIsoDate = toInputDate(rawVal);
                            td.innerHTML = `
                                <input type="date" id="${inputId}" data-original="${formattedIsoDate}" value="${formattedIsoDate}" class="text-[11px] bg-white border border-slate-300 rounded px-2 py-1 font-medium text-slate-700 focus:ring-1 focus:ring-indigo-500">
                            `;
                        } else {
                            td.innerHTML = `
                                <div class="flex items-center gap-1 min-w-[170px]">
                                    <input type="text" id="${inputId}" data-original="${rawVal}" value="${rawVal}" class="text-[11px] bg-white border border-slate-300 rounded px-2 py-1 font-medium text-slate-700 focus:ring-1 focus:ring-indigo-500 w-full" title="${rawVal}">
                                    <button type="button" onclick="openObsModal('${inputId}', '${header.replace(/'/g, "\\'")}')" class="text-slate-400 hover:text-indigo-600 p-1.5 rounded hover:bg-slate-100 transition" title="Expandir para leitura e edição">
                                        <i class="fa-solid fa-expand text-xs"></i>
                                    </button>
                                </div>
                            `;
                        }
                    } else {
                        td.innerText = rawVal || "-";
                    }

                    tr.appendChild(td);
                });

                tbody.appendChild(tr);
            });

            document.getElementById('displayedCount').innerText = data.length;
            document.getElementById('totalCount').innerText = rawAtivosData.length;
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
            } else if (type === 'DUPLICIDADE_CIS') {
                if (selCisNum) selCisNum.value = 'ONLY_DUPLICATED_CIS';
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

            // ANÁLISE E CONTAGEM DE REPETIÇÃO/DUPLICIDADE APENAS PARA CIS NUMÉRICA (IGNORA LETRAS E TEXTOS)
            const cisFrequency = {};
            rawAtivosData.forEach(item => {
                const cleanCis = cleanStr(item.ci).toUpperCase();
                if (isNumericCIS(cleanCis)) {
                    cisFrequency[cleanCis] = (cisFrequency[cleanCis] || 0) + 1;
                }
            });

            let totalDuplicadosCis = 0;
            rawAtivosData.forEach(item => {
                const cleanCis = cleanStr(item.ci).toUpperCase();
                item.isCisDuplicated = (isNumericCIS(cleanCis) && cisFrequency[cleanCis] > 1);
                if (item.isCisDuplicated) {
                    totalDuplicadosCis++;
                }

                statusCounts[item.statusColF] = (statusCounts[item.statusColF] || 0) + 1;
                cobrancaCounts[item.cobrancaColE] = (cobrancaCounts[item.cobrancaColE] || 0) + 1;
                if (item.isCisNumeric && item.cobrancaColE === "N/I") cisNumCobrancaNI++;
            });

            // MÉTRICAS DE STATUS
            const statusContainer = document.getElementById('statusCardsContainer');
            statusContainer.innerHTML = '';
            statusContainer.appendChild(createMetricCard("TOTAL DE ATIVOS", total, "100%", "fa-list-check", () => triggerCardFilter('RESET', '')));

            // Cartão de Duplicidade na CIS (Apenas Números)
            const pctDup = total ? ((totalDuplicadosCis / total) * 100).toFixed(1) + "%" : "0.0%";
            const dupCard = createCardElement("DUPLICIDADE DE LINK NA CIS", totalDuplicadosCis, pctDup, {
                bg: "border-l-rose-500", text: "text-rose-700", badge: "bg-rose-100", icon: "fa-copy"
            }, () => triggerCardFilter('DUPLICIDADE_CIS', 'ONLY_DUPLICATED_CIS'));
            statusContainer.appendChild(dupCard);

            populateCards(statusContainer, statusCounts, total, 'filterStatusColF', 'STATUS', 'STATUS');

            // MÉTRICAS DE COBRANÇA (COM EXPANSÃO E CARDS PRINCIPAIS)
            const cobrancaContainer = document.getElementById('cobrancaCardsContainer');
            cobrancaContainer.innerHTML = '';

            // Grid para os Cards Principais
            const cobrancaMainGrid = document.createElement('div');
            cobrancaMainGrid.className = 'grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4 w-full';

            // Grid para os Cards Secundários (Oculto por padrão)
            const cobrancaSecondaryGrid = document.createElement('div');
            cobrancaSecondaryGrid.id = 'cobrancaSecondaryCardsContainer';
            cobrancaSecondaryGrid.className = 'grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4 w-full hidden mt-4 pt-4 border-t border-slate-100';

            cobrancaContainer.appendChild(cobrancaMainGrid);
            cobrancaContainer.appendChild(cobrancaSecondaryGrid);

            // Card Principal 1: TOTAL DE REGISTROS
            cobrancaMainGrid.appendChild(createMetricCard("TOTAL DE REGISTROS", total, "100%", "fa-receipt", () => triggerCardFilter('RESET', '')));

            // Definir os 4 rótulos dos Cards Principais Exigidos
            const mainKeysOrder = ["NÃO COBRANDO", "COBRANDO", "SUSPENSA", "COBRAR NA PROXIMA MEDIÇÃO"];

            const selectCobranca = document.getElementById('filterCobrancaColE');
            if (selectCobranca) selectCobranca.innerHTML = `<option value="">COBRANÇA: Todas</option>`;

            const colorPalette = [
                { bg: "border-l-sky-500", text: "text-sky-600", badge: "bg-sky-100", icon: "fa-magnifying-glass-chart" },
                { bg: "border-l-emerald-500", text: "text-emerald-600", badge: "bg-emerald-100", icon: "fa-circle-check" },
                { bg: "border-l-amber-500", text: "text-amber-600", badge: "bg-amber-100", icon: "fa-triangle-exclamation" },
                { bg: "border-l-indigo-500", text: "text-indigo-600", badge: "bg-indigo-100", icon: "fa-sliders" }
            ];

            const allKeys = Object.keys(cobrancaCounts).sort();
            const renderedMainKeys = new Set();

            function isMainKey(k) {
                const u = String(k || '').toUpperCase().trim();
                return u === "NÃO COBRANDO" || u === "NAO COBRANDO" || u === "COBRANDO" || u.includes("SUSPENS") || u.includes("COBRAR NA PROXIMA");
            }

            // Gerar os 4 Cards Principais Exigidos
            mainKeysOrder.forEach((targetLabel, idx) => {
                const realKey = allKeys.find(k => {
                    const u = k.toUpperCase().trim();
                    if (targetLabel === "NÃO COBRANDO") return u === "NÃO COBRANDO" || u === "NAO COBRANDO";
                    if (targetLabel === "COBRANDO") return u === "COBRANDO";
                    if (targetLabel === "SUSPENSA") return u.includes("SUSPENS");
                    if (targetLabel === "COBRAR NA PROXIMA MEDIÇÃO") return u.includes("COBRAR NA PROXIMA");
                    return false;
                }) || targetLabel;

                const count = cobrancaCounts[realKey] || 0;
                const pct = total ? ((count / total) * 100).toFixed(1) + "%" : "0.0%";
                let palette = colorPalette[idx % colorPalette.length];

                const card = createCardElement(realKey, count, pct, palette, () => triggerCardFilter('COBRANÇA', realKey));
                cobrancaMainGrid.appendChild(card);
                renderedMainKeys.add(realKey);
            });

            // Card extra: CIS NUMÉRICA N/I (vai para o grid secundário)
            const pctCisNI = total ? ((cisNumCobrancaNI / total) * 100).toFixed(1) + "%" : "0.0%";
            const extraCard = createCardElement("CIS NUMÉRICA N/I", cisNumCobrancaNI, pctCisNI, {
                bg: "border-l-amber-500", text: "text-amber-700", badge: "bg-amber-100", icon: "fa-triangle-exclamation"
            }, () => triggerCardFilter('CIS_NUM_NI', 'ONLY_NUMERIC_NI'));
            cobrancaSecondaryGrid.appendChild(extraCard);

            // Popula os Cards Secundários com o restante dos status existentes
            let secondaryIdx = 1;
            allKeys.forEach(key => {
                if (!renderedMainKeys.has(key) && !isMainKey(key)) {
                    const count = cobrancaCounts[key];
                    const pct = total ? ((count / total) * 100).toFixed(1) + "%" : "0.0%";
                    let palette = colorPalette[secondaryIdx % colorPalette.length];

                    const card = createCardElement(key, count, pct, palette, () => triggerCardFilter('COBRANÇA', key));
                    cobrancaSecondaryGrid.appendChild(card);
                    secondaryIdx++;
                }

                // Popula o filtro dropdown
                if (selectCobranca) {
                    const opt = document.createElement('option');
                    opt.value = key;
                    opt.textContent = `${key} (${cobrancaCounts[key]})`;
                    selectCobranca.appendChild(opt);
                }
            });

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

        // LÓGICA DE FILTRAGEM COMBINADA + ORDENAÇÃO
        function filterTable() {
            const q = document.getElementById('tableSearchInput').value.toLowerCase();
            const selectedStatus = document.getElementById('filterStatusColF').value;
            const selectedCobranca = document.getElementById('filterCobrancaColE').value;
            const specialFilter = document.getElementById('filterCisNumNI').value;

            let filtered = rawAtivosData.filter(item => {
                const allValues = Object.values(item.originalRow).map(v => String(v).toLowerCase()).join(' ');
                const matchQuery = !q || allValues.includes(q);
                const matchStatus = !selectedStatus || item.statusColF.toUpperCase() === selectedStatus.toUpperCase();
                const matchCobranca = !selectedCobranca || item.cobrancaColE.toUpperCase() === selectedCobranca.toUpperCase();
                
                let matchSpecial = true;
                if (specialFilter === "ONLY_NUMERIC_NI") {
                    matchSpecial = item.isCisNumeric && item.cobrancaColE === "N/I";
                } else if (specialFilter === "ONLY_DUPLICATED_CIS") {
                    matchSpecial = item.isCisDuplicated === true;
                }

                let matchColFilters = true;
                for (const [colHeader, allowedSet] of Object.entries(activeColumnFilters)) {
                    if (allowedSet && allowedSet.size > 0) {
                        const cellVal = String(item.originalRow[colHeader] || "-").trim();
                        if (!allowedSet.has(cellVal)) {
                            matchColFilters = false;
                            break;
                        }
                    }
                }

                return matchQuery && matchStatus && matchCobranca && matchSpecial && matchColFilters;
            });

            if (currentSort.colHeader) {
                const h = currentSort.colHeader;
                const dir = currentSort.direction === 'asc' ? 1 : -1;
                filtered.sort((a, b) => {
                    const valA = String(a.originalRow[h] || '').toLowerCase();
                    const valB = String(b.originalRow[h] || '').toLowerCase();
                    return valA.localeCompare(valB, 'pt-BR', { numeric: true }) * dir;
                });
            }

            renderTableBody(filtered);
        }

        function resetFilters() {
            document.getElementById('tableSearchInput').value = '';
            document.getElementById('filterStatusColF').value = '';
            document.getElementById('filterCobrancaColE').value = '';
            document.getElementById('filterCisNumNI').value = '';

            activeColumnFilters = {};
            currentSort = { colHeader: null, direction: null };

            renderTableHeaders();
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
