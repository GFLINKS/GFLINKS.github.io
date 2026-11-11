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

    <!-- MODAL DE OBSERVAÇÃO / TEXTO LONGO (ATUALIZADO E ORGANIZADO) -->
    <div id="obsModal" class="fixed inset-0 bg-slate-900/80 z-[70] hidden flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-2xl shadow-2xl p-6 max-w-2xl w-full space-y-4 border border-slate-200">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 class="text-base font-bold text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-pen-to-square text-indigo-600"></i>
                    <span id="obsModalTitle">Visualizar / Editar Observação</span>
                </h3>
                <button onclick="closeObsModal()" class="text-slate-400 hover:text-slate-600 text-base">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            <div class="space-y-2">
                <div class="flex flex-wrap justify-between items-center gap-2">
                    <label class="block text-xs font-semibold text-slate-600">Conteúdo Completo da Observação:</label>
                    <div class="flex items-center gap-2">
                        <button type="button" onclick="autoFormatObsTextarea()" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 font-semibold px-2.5 py-1.5 rounded-lg border border-slate-300 transition flex items-center gap-1.5 shadow-sm" title="Organizar frases em parágrafos separados">
                            <i class="fa-solid fa-align-left text-slate-500"></i> Organizar Parágrafos
                        </button>
                        <button type="button" onclick="insertObsTimestamp()" class="text-xs bg-indigo-50 hover:bg-indigo-100 text-indigo-700 font-bold px-3 py-1.5 rounded-lg border border-indigo-200 transition flex items-center gap-1.5 shadow-sm" title="Adicionar nova nota com a data/hora atual">
                            <i class="fa-regular fa-calendar-plus text-indigo-600"></i> + Nova Nota com Data
                        </button>
                    </div>
                </div>
                <textarea id="obsModalTextarea" rows="10" placeholder="Digite ou cole suas observações aqui..." class="w-full text-xs p-3.5 border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-slate-50 font-sans leading-relaxed text-slate-800 placeholder-slate-400 resize-y"></textarea>
            </div>
            <div class="flex justify-end gap-2 pt-2 border-t border-slate-100">
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
            <p class="text-xs text-slate-500 mt-1">Os dados do Painel Links GF são atualizados e salvos ao confirmar alterações.</p>
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

        // LISTAS OFICIAIS ATUALIZADAS CONFORME REGRAS DO GOOGLE SHEETS
        const OPCOES_COBRANCA = [
            "ATIVA", "SUSPENSA", "N/I", "GUARDIAN", "CORPORATIVO", "ESTOQUE", 
            "CANCELADA", "INATIVO", "DESATIVADA", "COBRANDO", "COBRAR NA PROXIMA MEDIÇÃO", "NÃO COBRANDO",
            "STORAGE", "TÉCNICO MARCELO"
        ];
        const OPCOES_STATUS = [
            "OK", "CANCELADA", "SUSPENSA", "EM ANALISE", "DUPLICIDADE", "EM CANCELAMENTO", 
            "MANUTENCAO", "TRANSFERENCIA CNPJ", "TRANSFERENCIA ENDERECO", "SUBSTITUIÇÃO", 
            "PENDENTE*", "DESATIVADOS", "TÉCNICO MARCELO", "CHAMADO EM ABERTO", "EQUIPAMENTO PERDIDO"
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

        // LÓGICA DE FORMATAÇÃO E ORGANIZAÇÃO AUTOMÁTICA DE OBSERVAÇÕES
        function formatObsText(text) {
            if (!text) return "";
            let str = String(text).trim();
            if (!str) return "";

            // Padrões comuns para inserir quebras de linha em textos concatenados
            const patterns = [
                /(?<!\n)(Reaberto chamado em)/gi,
                /(?<!\n)(Chamado aberto em)/gi,
                /(?<!\n)(PROTOCOLO:)/gi,
                /(?<!\n)(Previsão de atendimento)/gi,
                /(?<!\n)(ORDEM DE SERVIÇO:)/gi,
                /(?<!\n)(OS:)/gi,
                /(?<!\n)(MUN \|)/gi,
                /(?<!\n)(CNPJ \d)/gi,
                /(?<!\n)(Código cliente:)/gi,
                /(?<!\n)(Endereço:)/gi,
                /(?<!\n)(Protocolo de Atendimento)/gi,
                /(?<!\n)(OUVIDORIA)/gi,
                /(?<!\n)(\[\d{2}\/\d{2}\/\d{4})/g
            ];

            patterns.forEach(pat => {
                str = str.replace(pat, '\n$1');
            });

            const lines = str.split('\n').map(l => l.trim()).filter(l => l.length > 0);
            return lines.join('\n\n');
        }

        function autoFormatObsTextarea() {
            const txt = document.getElementById('obsModalTextarea');
            if (txt) {
                txt.value = formatObsText(txt.value);
            }
        }

        function insertObsTimestamp() {
            const txt = document.getElementById('obsModalTextarea');
            if (!txt) return;

            const now = new Date();
            const dateStr = now.toLocaleDateString('pt-BR');
            const timeStr = now.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });
            const timestampHeader = `[${dateStr} ${timeStr}] - `;

            const currentVal = txt.value.trim();
            if (!currentVal) {
                txt.value = timestampHeader;
            } else {
                txt.value = currentVal +
