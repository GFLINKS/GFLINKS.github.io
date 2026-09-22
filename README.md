<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Estoque - Grupo Forte Protege</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Ícones -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    
    <style>
        body {
            background-color: #0b0f19 !important;
            color: #f1f5f9 !important;
            font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
        }

        .card-panel {
            background-color: #111827 !important;
            border: 1px solid #1f2937 !important;
        }

        /* TABELA COM ALTO CONTRASTE - TEXTO PRETO */
        table.custom-table {
            background-color: #ffffff !important;
            width: 100%;
            border-collapse: collapse;
        }

        table.custom-table th {
            background-color: #1e293b !important;
            color: #ffffff !important;
            font-size: 0.75rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            padding: 0.75rem 0.5rem;
            border: 1px solid #334155 !important;
            user-select: none;
        }

        table.custom-table td {
            background-color: #ffffff !important;
            color: #000000 !important; /* PRETO ABSOLUTO */
            font-weight: 600 !important;
            font-size: 0.875rem !important;
            padding: 0.65rem 0.85rem;
            border: 1px solid #e2e8f0 !important;
        }

        table.custom-table tr:hover td {
            background-color: #f1f5f9 !important;
        }

        /* MENU DE FILTRO ESTILO EXCEL */
        .excel-filter-menu {
            position: absolute;
            z-index: 100;
            background-color: #ffffff;
            color: #1e293b;
            border: 1px solid #cbd5e1;
            border-radius: 8px;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.5);
            width: 250px;
            font-size: 0.8rem;
            display: none;
        }

        .excel-filter-menu .filter-option {
            padding: 8px 12px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            font-weight: 600;
            color: #334155;
        }

        .excel-filter-menu .filter-option:hover {
            background-color: #f1f5f9;
            color: #2563eb;
        }

        .excel-filter-list {
            max-height: 160px;
            overflow-y: auto;
            border: 1px solid #e2e8f0;
            border-radius: 4px;
            background-color: #f8fafc;
            padding: 4px;
        }

        .excel-filter-item {
            display: flex;
            align-items: center;
            gap: 6px;
            padding: 3px 6px;
            cursor: pointer;
            border-radius: 3px;
            font-weight: 500;
        }

        .excel-filter-item:hover {
            background-color: #e2e8f0;
        }

        .th-container {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 4px;
        }

        .filter-btn {
            background-color: #334155;
            color: #cbd5e1;
            border-radius: 4px;
            padding: 2px 6px;
            cursor: pointer;
            font-size: 0.65rem;
            transition: all 0.2s;
        }

        .filter-btn:hover {
            background-color: #2563eb;
            color: #ffffff;
        }

        .filter-btn.active-filter {
            background-color: #10b981 !important;
            color: #ffffff !important;
        }

        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #f1f5f9; }
        ::-webkit-scrollbar-thumb { background: #94a3b8; border-radius: 3px; }
    </style>
</head>
<body class="min-h-screen flex flex-col antialiased relative" onclick="closeAllFilterMenus(event)">

    <!-- TOAST DE NOTIFICAÇÃO DA SINCRONIZAÇÃO -->
    <div id="toastSync" class="fixed bottom-5 right-5 z-50 bg-emerald-600 text-white px-4 py-3 rounded-xl shadow-2xl flex items-center gap-3 transition-all duration-300 transform translate-y-20 opacity-0 pointer-events-none">
        <i class="fa-solid fa-circle-check text-lg"></i>
        <span class="text-xs font-semibold">Dados sincronizados com sucesso!</span>
    </div>

    <!-- CABEÇALHO -->
    <header class="bg-slate-900 border-b border-slate-800 sticky top-0 z-40 shadow-xl">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex flex-col lg:flex-row items-center justify-between gap-4">
            
            <!-- LOGO E TÍTULO -->
            <div class="flex items-center gap-4 w-full lg:w-auto justify-between lg:justify-start">
                <div class="flex items-center gap-3">
                    <img src="logo.png" alt="Grupo Forte Protege" class="h-10 w-auto object-contain max-w-[150px]" onerror="this.onerror=null; this.src='https://via.placeholder.com/150x40/111827/38bdf8?text=GF+PROTEGE'">
                    <div class="h-7 w-px bg-slate-700 hidden sm:block"></div>
                    <div>
                        <h1 class="text-lg font-bold text-white tracking-tight">Estoque & Operações</h1>
                        <p class="text-[11px] text-slate-400 font-medium">TI • CFTV • Conectividade</p>
                    </div>
                </div>
            </div>

            <!-- CONTROLES -->
            <div class="flex flex-wrap items-center justify-center lg:justify-end gap-3 w-full lg:w-auto">
                <div class="bg-slate-950 border border-slate-800 px-3 py-1.5 rounded-lg text-xs text-slate-400 flex items-center gap-2">
                    <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                    <span>Atualizado: <strong id="lastUpdateText" class="text-slate-200">22/09/2026 16:49</strong></span>
                </div>

                <button onclick="syncData()" id="btnSync" class="flex items-center gap-2 bg-slate-800 hover:bg-slate-700 active:bg-slate-600 text-slate-200 border border-slate-700 px-3 py-1.5 rounded-lg text-xs font-semibold transition shadow-sm">
                    <i id="iconSync" class="fa-solid fa-rotate text-blue-400"></i>
                    <span>Sincronizar</span>
                </button>

                <div class="flex items-center bg-slate-950 p-1 rounded-lg border border-slate-800">
                    <button onclick="switchTab('estoque')" id="btnTabEstoque" class="px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2">
                        <i class="fa-solid fa-boxes-stacked"></i> Estoque
                    </button>
                    <button onclick="switchTab('historico')" id="btnTabHistorico" class="px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2">
                        <i class="fa-solid fa-clock-rotate-left"></i> Histórico
                    </button>
                </div>
            </div>
        </div>
    </header>

    <!-- CONTEÚDO PRINCIPAL -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-1 w-full space-y-6">

        <!-- CARDS DE MÉTRICAS CLICÁVEIS (KPIs) -->
        <section class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-5 gap-4">
            
            <!-- CARD CENTRAL -->
            <div onclick="filterByMetric('central')" id="kpi-card-central" class="card-panel p-4 rounded-xl shadow-sm cursor-pointer hover:border-blue-500 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400 group-hover:text-blue-400">
                    <span>Central</span>
                    <i class="fa-solid fa-warehouse text-blue-400"></i>
                </div>
                <div class="text-2xl font-bold text-white mt-2" id="kpiCentral">96</div>
                <span class="text-[11px] text-slate-500 group-hover:text-slate-400">Clique para isolar</span>
            </div>

            <!-- CARD TÉCNICO MARCELO -->
            <div onclick="filterByMetric('tecnico')" id="kpi-card-tecnico" class="card-panel p-4 rounded-xl shadow-sm cursor-pointer hover:border-amber-500 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400 group-hover:text-amber-400">
                    <span>Técnico Marcelo</span>
                    <i class="fa-solid fa-user-gear text-amber-400"></i>
                </div>
                <div class="text-2xl font-bold text-amber-400 mt-2" id="kpiTecnico">45</div>
                <span class="text-[11px] text-slate-500 group-hover:text-slate-400">Clique para isolar</span>
            </div>

            <!-- CARD ESTOQUE OPERACIONAL -->
            <div onclick="filterByMetric('op')" id="kpi-card-op" class="card-panel p-4 rounded-xl shadow-sm cursor-pointer hover:border-emerald-500 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400 group-hover:text-emerald-400">
                    <span>Estoque Op.</span>
                    <i class="fa-solid fa-truck-ramp-box text-emerald-400"></i>
                </div>
                <div class="text-2xl font-bold text-emerald-400 mt-2" id="kpiOperacional">15</div>
                <span class="text-[11px] text-slate-500 group-hover:text-slate-400">Clique para isolar</span>
            </div>

            <!-- CARD ACERVO -->
            <div onclick="filterByMetric('acervo')" id="kpi-card-acervo" class="card-panel p-4 rounded-xl shadow-sm cursor-pointer hover:border-purple-500 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400 group-hover:text-purple-400">
                    <span>Acervo</span>
                    <i class="fa-solid fa-laptop text-purple-400"></i>
                </div>
                <div class="text-2xl font-bold text-purple-400 mt-2" id="kpiAcervo">5</div>
                <span class="text-[11px] text-slate-500 group-hover:text-slate-400">Clique para isolar</span>
            </div>

            <!-- CARD TOTAL FÍSICO -->
            <div onclick="filterByMetric('total')" id="kpi-card-total" class="col-span-2 sm:col-span-1 card-panel p-4 rounded-xl shadow-sm bg-gradient-to-br from-slate-900 to-blue-950 border-blue-900/50 cursor-pointer hover:border-blue-400 hover:scale-[1.02] transition-all duration-200 group">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-blue-300">
                    <span>Total Físico</span>
                    <i class="fa-solid fa-cubes text-blue-400"></i>
                </div>
                <div class="text-2xl font-bold text-blue-300 mt-2" id="kpiTotal">161</div>
                <span class="text-[11px] text-blue-200/70">Exibir todas as colunas</span>
            </div>
        </section>

        <!-- ABA 1: SALDO DE ESTOQUE -->
        <section id="secEstoque" class="card-panel rounded-xl shadow-lg overflow-hidden">
            <div class="p-5 border-b border-slate-800 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                <div>
                    <h2 class="text-base font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-list-check text-blue-500"></i> Disponibilidade por Equipamento
                    </h2>
                    <p class="text-xs text-slate-400" id="activeFilterBadge">Mostrando todas as localizações</p>
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <button onclick="clearAllFilters('estoque')" class="px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-xs font-semibold text-slate-300 rounded-lg border border-slate-700 transition">
                        <i class="fa-solid fa-filter-circle-xmark text-rose-400 mr-1"></i> Limpar Filtros
                    </button>
                    <div class="relative w-full sm:w-64">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-2.5 text-slate-500 text-sm"></i>
                        <input type="text" id="searchEstoque" onkeyup="filterEstoque()" placeholder="Pesquisa rápida..." class="w-full bg-slate-950 border border-slate-800 rounded-lg pl-9 pr-4 py-1.5 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 transition">
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto relative">
                <table class="custom-table" id="tblEstoque">
                    <thead>
                        <tr id="theadEstoqueTr">
                            <!-- Preenchido dinamicamente via JS -->
                        </tr>
                    </thead>
                    <tbody id="tbodyEstoque">
                        <!-- Preenchido via JS -->
                    </tbody>
                </table>
            </div>
        </section>

        <!-- ABA 2: HISTÓRICO DE MOVIMENTAÇÕES -->
        <section id="secHistorico" class="card-panel rounded-xl shadow-lg overflow-hidden hidden">
            <div class="p-5 border-b border-slate-800 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                <div>
                    <h2 class="text-base font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-arrow-right-arrow-left text-blue-500"></i> Histórico de Entradas e Saídas
                    </h2>
                    <p class="text-xs text-slate-400">Filtre por datas, origem, destino ou projetos diretamente nas colunas</p>
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <button onclick="clearAllFilters('historico')" class="px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-xs font-semibold text-slate-300 rounded-lg border border-slate-700 transition">
                        <i class="fa-solid fa-filter-circle-xmark text-rose-400 mr-1"></i> Limpar Filtros
                    </button>
                    <div class="relative w-full sm:w-64">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-2.5 text-slate-500 text-sm"></i>
                        <input type="text" id="searchHistorico" onkeyup="filterHistorico()" placeholder="Pesquisa rápida..." class="w-full bg-slate-950 border border-slate-800 rounded-lg pl-9 pr-4 py-1.5 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 transition">
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto relative">
                <table class="custom-table" id="tblHistorico">
                    <thead>
                        <tr>
                            <th>
                                <div class="th-container">
                                    <span>Data</span>
                                    <button class="filter-btn" id="fbtn-historico-data" onclick="toggleFilterDropdown(event, 'historico', 'data')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Equipamento</span>
                                    <button class="filter-btn" id="fbtn-historico-item" onclick="toggleFilterDropdown(event, 'historico', 'item')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th class="text-center">
                                <div class="th-container justify-center">
                                    <span>Tipo</span>
                                    <button class="filter-btn" id="fbtn-historico-tipo" onclick="toggleFilterDropdown(event, 'historico', 'tipo')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th class="text-center">
                                <div class="th-container justify-center">
                                    <span>Qtd</span>
                                    <button class="filter-btn" id="fbtn-historico-qtd" onclick="toggleFilterDropdown(event, 'historico', 'qtd')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Origem</span>
                                    <button class="filter-btn" id="fbtn-historico-origem" onclick="toggleFilterDropdown(event, 'historico', 'origem')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Destino</span>
                                    <button class="filter-btn" id="fbtn-historico-destino" onclick="toggleFilterDropdown(event, 'historico', 'destino')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                            <th>
                                <div class="th-container">
                                    <span>Observações / Projeto</span>
                                    <button class="filter-btn" id="fbtn-historico-obs" onclick="toggleFilterDropdown(event, 'historico', 'obs')"><i class="fa-solid fa-filter"></i></button>
                                </div>
                            </th>
                        </tr>
                    </thead>
                    <tbody id="tbodyHistorico">
                        <!-- Preenchido via JS -->
                    </tbody>
                </table>
            </div>
        </section>

    </main>

    <!-- CONTAINER DINÂMICO PARA O MENU ESTILO EXCEL -->
    <div id="excelFilterDropdown" class="excel-filter-menu" onclick="event.stopPropagation()">
        <div class="filter-option border-b border-slate-100" onclick="applySort('asc')">
            <i class="fa-solid fa-arrow-down-a-z text-blue-600"></i> Classificar de A a Z
        </div>
        <div class="filter-option border-b border-slate-200" onclick="applySort('desc')">
            <i class="fa-solid fa-arrow-up-z-a text-blue-600"></i> Classificar de Z a A
        </div>

        <div class="p-2 border-b border-slate-200">
            <input type="text" id="excelSearchBox" oninput="filterExcelCheckboxList()" placeholder="Pesquisar itens..." class="w-full bg-slate-100 border border-slate-300 rounded px-2 py-1 text-xs focus:outline-none focus:border-blue-500">
        </div>

        <div class="p-2">
            <div class="excel-filter-item font-bold border-b border-slate-200 pb-1 mb-1">
                <input type="checkbox" id="chkSelectAll" onchange="toggleSelectAllCheckboxes(this.checked)" checked>
                <label for="chkSelectAll">(Selecionar Tudo)</label>
            </div>
            <div id="excelCheckboxList" class="excel-filter-list">
                <!-- Checkboxes gerados dinamicamente -->
            </div>
        </div>

        <div class="p-2 bg-slate-50 border-t border-slate-200 flex justify-end gap-2 rounded-b-8 shadow-inner">
            <button onclick="confirmColumnFilter()" class="px-3 py-1 bg-blue-600 text-white rounded font-semibold hover:bg-blue-700 text-xs">OK</button>
            <button onclick="closeExcelFilterMenu()" class="px-3 py-1 bg-slate-200 text-slate-700 rounded font-semibold hover:bg-slate-300 text-xs">Cancelar</button>
        </div>
    </div>

    <!-- RODAPÉ -->
    <footer class="bg-slate-900 border-t border-slate-800 py-3 text-center text-xs text-slate-500">
        Grupo Forte Protege &copy; 2026 — Controle Interno Operacional
    </footer>

    <!-- SCRIPT COMPLETO -->
    <script>
        const estoqueData = [
            { item: "Mikrotik Hap", central: 0, tecnico: 17, op: 5, acervo: 0, total: 22 },
            { item: "Mikrotik LTE6", central: 0, tecnico: 0, op: 0, acervo: 1, total: 1 },
            { item: "Antena Elsys", central: 0, tecnico: 2, op: 1, acervo: 0, total: 3 },
            { item: "Roteador Intelbras 5G", central: 0, tecnico: 8, op: 8, acervo: 0, total: 16 },
            { item: "Suporte Monitor", central: 0, tecnico: 0, op: 1, acervo: 0, total: 1 },
            { item: "Monitor Mnbox", central: 8, tecnico: 0, op: 0, acervo: 1, total: 9 },
            { item: "HD 4TB", central: 0, tecnico: 2, op: 0, acervo: 0, total: 2 },
            { item: "Notebook Dell i5 11th Gen", central: 0, tecnico: 0, op: 0, acervo: 1, total: 1 },
            { item: "Celular Realme Note 50", central: 0, tecnico: 0, op: 0, acervo: 1, total: 1 },
            { item: "Nobreak 1200VA", central: 0, tecnico: 1, op: 0, acervo: 0, total: 1 },
            { item: "Régua Intelbras", central: 0, tecnico: 0, op: 0, acervo: 1, total: 1 },
            { item: "Antena Hikvision", central: 3, tecnico: 0, op: 0, acervo: 0, total: 3 },
            { item: "Switch Intelbras 8 Portas (SF 1008 F)", central: 6, tecnico: 0, op: 0, acervo: 0, total: 6 },
            { item: "Switch Intelbras 8P PoE (SF 1108 F-P)", central: 1, tecnico: 0, op: 0, acervo: 0, total: 1 },
            { item: "Telefone IP Intelbras (TIP 125 I)", central: 2, tecnico: 0, op: 0, acervo: 0, total: 2 },
            { item: "Terminal Interfonia IP (TDMI 400 IP)", central: 8, tecnico: 0, op: 0, acervo: 0, total: 8 },
            { item: "Cabo HDMI High Speed (1m)", central: 3, tecnico: 0, op: 0, acervo: 0, total: 3 },
            { item: "Câmera IP Dome Full HD (VIP 1230 D G2)", central: 10, tecnico: 2, op: 0, acervo: 0, total: 12 },
            { item: "Câmera IP Bullet Full HD (VIP 1230 B G2)", central: 0, tecnico: 2, op: 0, acervo: 0, total: 2 },
            { item: "Câmera IP Bullet Full HD (VIPC 1230 B)", central: 3, tecnico: 0, op: 0, acervo: 0, total: 3 },
            { item: "Câmera Multi HD Bullet (VHL 1120 B G2)", central: 4, tecnico: 3, op: 0, acervo: 0, total: 7 },
            { item: "Câmera Multi HD Dome (VHL 1120 D G2)", central: 0, tecnico: 2, op: 0, acervo: 0, total: 2 },
            { item: "Fonte Chaveada 12.8V/5A (EFM 1205 G2)", central: 7, tecnico: 1, op: 0, acervo: 0, total: 8 },
            { item: "Fonte Chaveada 12.8V/2A (EF 1202)", central: 1, tecnico: 2, op: 0, acervo: 0, total: 3 },
            { item: "Mouse Óptico USB", central: 4, tecnico: 1, op: 0, acervo: 0, total: 5 },
            { item: "Protetor Eletrônico / Filtro de Linha (EPE 205)", central: 3, tecnico: 1, op: 0, acervo: 0, total: 4 },
            { item: "Gravador NVR 32 Canais (NVD 1432)", central: 1, tecnico: 0, op: 0, acervo: 0, total: 1 },
            { item: "Caixa Cabo de Rede UTP CAT 5e (305m)", central: 1, tecnico: 0, op: 0, acervo: 0, total: 1 },
            { item: "Caixa de Passagem VBOX 1100", central: 23, tecnico: 0, op: 0, acervo: 0, total: 23 },
            { item: "Rolo Cabo Coaxial / Par Trançado (100m)", central: 2, tecnico: 1, op: 0, acervo: 0, total: 3 },
            { item: "Bandeja para Rack 19\"", central: 6, tecnico: 0, op: 0, acervo: 0, total: 6 }
        ];

        const historicoData = [
            { data: "23/09/2026", item: "HD 4TB", tipo: "Entrada", qtd: 2, origem: "Fornecedor Voice", destino: "Técnico Marcelo", obs: "SERA RETIRADO EM 23/09/2026" },
            { data: "21/09/2026", item: "Mikrotik Hap", tipo: "Entrada", qtd: 7, origem: "Cliente", destino: "Técnico Marcelo", obs: "RETIRADA COLDS OSU" },
            { data: "21/09/2026", item: "Antena Elsys", tipo: "Entrada", qtd: 1, origem: "Cliente", destino: "Técnico Marcelo", obs: "RETIRADA COLDS OSU" },
            { data: "21/09/2026", item: "Roteador Intelbras 5G", tipo: "Entrada", qtd: 6, origem: "Cliente", destino: "Técnico Marcelo", obs: "RETIRADA COLDS OSU" },
            { data: "21/09/2026", item: "Rolo Cabo Coaxial 100m", tipo: "Saída", qtd: 1, origem: "Central", destino: "Técnico Marcelo", obs: "INSTALAÇÃO OSA" },
            { data: "21/09/2026", item: "Câmera Multi HD Dome", tipo: "Saída", qtd: 2, origem: "Central", destino: "Técnico Marcelo", obs: "INSTALAÇÃO OSA" },
            { data: "21/09/2026", item: "Câmera Multi HD Bullet", tipo: "Saída", qtd: 2, origem: "Central", destino: "Técnico Marcelo", obs: "INSTALAÇÃO OSA" },
            { data: "21/09/2026", item: "Fonte Chaveada 12V 2A", tipo: "Saída", qtd: 1, origem: "Central", destino: "Técnico Marcelo", obs: "INSTALAÇÃO OSA" },
            { data: "18/09/2026", item: "Fonte Chaveada 12V 5A", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Instalação Cold Pac" },
            { data: "18/09/2026", item: "Nobreak 1200VA", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Substituição Cold da COS" },
            { data: "18/09/2026", item: "Nobreak 1200VA", tipo: "Entrada", qtd: 2, origem: "Fornecedor Voice", destino: "Técnico Marcelo", obs: "Acervo técnico" },
            { data: "15/09/2026", item: "Nobreak 1200VA", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Troca de nobreak ADS" },
            { data: "15/09/2026", item: "Câmera Multi HD Dome", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Troca de camera ROM" },
            { data: "15/09/2026", item: "Monitor Mnbox", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Troca de monitor ROM" },
            { data: "15/09/2026", item: "Monitor Mnbox", tipo: "Saída", qtd: 1, origem: "Central", destino: "Técnico Marcelo", obs: "Atendimentos 15/09" },
            { data: "14/09/2026", item: "Antena Hikvision", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Instalação na JUK" },
            { data: "10/09/2026", item: "DVR INTELBRAS 4 Ch", tipo: "Saída", qtd: 1, origem: "Técnico Marcelo", destino: "Cliente", obs: "Instalação GRV Projeto KVS" },
            { data: "09/09/2026", item: "Câmera IP Bullet Full HD", tipo: "Saída", qtd: 2, origem: "Central", destino: "Técnico Marcelo", obs: "Acervo para o técnico" },
            { data: "03/09/2026", item: "Antena Elsys", tipo: "Saída", qtd: 2, origem: "Estoque Operacional", destino: "Técnico Marcelo", obs: "Instalação MKC e Reserva técnica" },
            { data: "02/09/2026", item: "Caixa de Passagem VBOX 1100", tipo: "Entrada", qtd: 23, origem: "Levantamento", destino: "Central", obs: "Levantamento Ian" },
            { data: "02/09/2026", item: "Câmera IP Dome Full HD", tipo: "Entrada", qtd: 12, origem: "Levantamento", destino: "Central", obs: "Levantamento Ian" },
            { data: "03/08/2026", item: "Mikrotik Hap", tipo: "Saída", qtd: 7, origem: "Estoque Operacional", destino: "Técnico Marcelo", obs: "Substituição INT e Instalação OSA" },
            { data: "30/07/2026", item: "Mikrotik Hap", tipo: "Entrada", qtd: 9, origem: "Central", destino: "Estoque Operacional", obs: "Alocação inicial" }
        ];

        // Estado dos Filtros Avançados
        const tableState = {
            estoque: { filters: {}, sortCol: null, sortDir: null, metricFilter: null },
            historico: { filters: {}, sortCol: null, sortDir: null }
        };

        let currentActiveContext = { tableId: null, colKey: null };

        // Definindo Colunas da Tabela de Estoque
        const estoqueColsDef = [
            { key: 'item', label: 'Equipamento', align: 'text-left', class: 'font-bold text-black' },
            { key: 'central', label: 'Central', align: 'text-center', class: 'text-center font-bold text-black' },
            { key: 'tecnico', label: 'Técnico Marcelo', align: 'text-center', class: 'text-center font-bold text-black' },
            { key: 'op', label: 'Estoque Op.', align: 'text-center', class: 'text-center font-bold text-black' },
            { key: 'acervo', label: 'Acervo Op.', align: 'text-center', class: 'text-center font-bold text-black' },
            { key: 'total', label: 'Total Geral', align: 'text-center', class: 'text-center font-bold text-blue-600 text-base' }
        ];

        // Renderização Dinâmica de Colunas e Dados do Estoque
        function renderEstoque(data) {
            const theadTr = document.getElementById('theadEstoqueTr');
            const tbody = document.getElementById('tbodyEstoque');
            const metricFilter = tableState.estoque.metricFilter;

            // Filtra colunas exibidas: se houver card selecionado, exibe apenas Equipamento, a coluna da Métrica e Total Geral
            const activeCols = estoqueColsDef.filter(col => {
                if (col.key === 'item' || col.key === 'total') return true;
                if (!metricFilter) return true; 
                return col.key === metricFilter; 
            });

            // Reconstrói o Cabeçalho (THEAD)
            theadTr.innerHTML = activeCols.map(col => `
                <th class="${col.align}">
                    <div class="th-container ${col.align === 'text-center' ? 'justify-center' : ''}">
                        <span>${col.label}</span>
                        <button class="filter-btn" id="fbtn-estoque-${col.key}" onclick="toggleFilterDropdown(event, 'estoque', '${col.key}')"><i class="fa-solid fa-filter"></i></button>
                    </div>
                </th>
            `).join('');

            // Reconstrói o Corpo (TBODY)
            tbody.innerHTML = '';
            
            if(data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="${activeCols.length}" class="p-6 text-center text-slate-500">Nenhum equipamento encontrado.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const tr = document.createElement('tr');
                tr.innerHTML = activeCols.map(col => {
                    let val = row[col.key];
                    if (val === 0 || val === null || val === undefined) val = '-';
                    return `<td class="${col.class}">${val}</td>`;
                }).join('');
                tbody.appendChild(tr);
            });
        }

        // Renderização da Tabela Histórico
        function renderHistorico(data) {
            const tbody = document.getElementById('tbodyHistorico');
            tbody.innerHTML = '';

            if(data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="7" class="p-6 text-center text-slate-500">Nenhum registro encontrado.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const badgeClass = row.tipo === 'Entrada' 
                    ? 'bg-emerald-100 text-emerald-800 border-emerald-300' 
                    : 'bg-rose-100 text-rose-800 border-rose-300';
                
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="font-bold text-black font-mono text-xs whitespace-nowrap">${row.data}</td>
                    <td class="font-bold text-black">${row.item}</td>
                    <td class="text-center">
                        <span class="px-2 py-0.5 text-xs rounded border ${badgeClass} font-bold">${row.tipo}</span>
                    </td>
                    <td class="text-center font-bold text-black">${row.qtd}</td>
                    <td class="font-bold text-black">${row.origem}</td>
                    <td class="font-bold text-black">${row.destino}</td>
                    <td class="font-bold text-black italic">${row.obs || '-'}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        // Processamento Central de Filtros
        function processData(tableId) {
            const isEstoque = tableId === 'estoque';
            let dataset = isEstoque ? [...estoqueData] : [...historicoData];
            const state = tableState[tableId];

            // 1. Filtro por Card de Métrica
            if (isEstoque && state.metricFilter) {
                dataset = dataset.filter(row => row[state.metricFilter] > 0);
            }

            // 2. Pesquisa rápida
            const searchInput = document.getElementById(isEstoque ? 'searchEstoque' : 'searchHistorico').value.toLowerCase();
            if (searchInput) {
                dataset = dataset.filter(row => Object.values(row).some(v => String(v).toLowerCase().includes(searchInput)));
            }

            // 3. Filtros por Coluna (Estilo Excel)
            Object.keys(state.filters).forEach(col => {
                const allowedValues = state.filters[col];
                if (allowedValues && allowedValues.length > 0) {
                    dataset = dataset.filter(row => allowedValues.includes(String(row[col])));
                }
            });

            // 4. Ordenação
            if (state.sortCol) {
                const col = state.sortCol;
                const dir = state.sortDir === 'asc' ? 1 : -1;
                dataset.sort((a, b) => {
                    let valA = a[col];
                    let valB = b[col];

                    if (typeof valA === 'number' && typeof valB === 'number') {
                        return (valA - valB) * dir;
                    }
                    return String(valA).localeCompare(String(valB), 'pt-BR', { numeric: true }) * dir;
                });
            }

            if (isEstoque) renderEstoque(dataset);
            else renderHistorico(dataset);

            updateFilterButtonStates(tableId);
        }

        function filterEstoque() { processData('estoque'); }
        function filterHistorico() { processData('historico'); }

        // FILTRO POR CARD DE MÉTRICAS (KPIs)
        function filterByMetric(metricKey) {
            switchTab('estoque');
            const state = tableState.estoque;

            if (state.metricFilter === metricKey || metricKey === 'total') {
                state.metricFilter = null;
            } else {
                state.metricFilter = metricKey;
            }

            const badge = document.getElementById('activeFilterBadge');
            const metricNames = {
                central: 'Central',
                tecnico: 'Técnico Marcelo',
                op: 'Estoque Operacional',
                acervo: 'Acervo Operacional'
            };

            if (state.metricFilter) {
                badge.innerHTML = `<span class="text-blue-400 font-bold">Filtro ativo:</span> Exibindo apenas a coluna da <strong class="text-white">${metricNames[state.metricFilter]}</strong>`;
            } else {
                badge.innerText = 'Mostrando todas as localizações';
            }

            highlightActiveMetricCard(state.metricFilter);
            processData('estoque');
        }

        function highlightActiveMetricCard(activeMetric) {
            const cards = ['central', 'tecnico', 'op', 'acervo', 'total'];
            cards.forEach(key => {
                const card = document.getElementById(`kpi-card-${key}`);
                if (!card) return;

                if (activeMetric === key) {
                    card.classList.add('border-blue-500', 'bg-slate-800/90', 'ring-2', 'ring-blue-500/50');
                } else {
                    card.classList.remove('border-blue-500', 'bg-slate-800/90', 'ring-2', 'ring-blue-500/50');
                }
            });
        }

        // DROPDOWN EXCEL
        function toggleFilterDropdown(event, tableId, colKey) {
            event.stopPropagation();
            currentActiveContext = { tableId, colKey };

            const dropdown = document.getElementById('excelFilterDropdown');
            const btn = event.currentTarget;
            const rect = btn.getBoundingClientRect();

            dropdown.style.top = `${rect.bottom + window.scrollY + 4}px`;
            dropdown.style.left = `${Math.min(rect.left + window.scrollX, window.innerWidth - 260)}px`;
            dropdown.style.display = 'block';

            const isEstoque = tableId === 'estoque';
            const rawData = isEstoque ? estoqueData : historicoData;
            const uniqueValues = [...new Set(rawData.map(r => String(r[colKey])))].sort((a, b) => a.localeCompare(b, 'pt-BR', { numeric: true }));

            const selectedFilter = tableState[tableId].filters[colKey];
            const checkboxContainer = document.getElementById('excelCheckboxList');
            checkboxContainer.innerHTML = '';

            document.getElementById('excelSearchBox').value = '';
            document.getElementById('chkSelectAll').checked = !selectedFilter || selectedFilter.length === uniqueValues.length;

            uniqueValues.forEach(val => {
                const isChecked = !selectedFilter || selectedFilter.includes(val);
                const itemDiv = document.createElement('div');
                itemDiv.className = 'excel-filter-item';
                itemDiv.innerHTML = `
                    <input type="checkbox" value="${val}" class="excel-chk-item" ${isChecked ? 'checked' : ''} onchange="updateSelectAllState()">
                    <span class="truncate">${val === '' ? '(Vazio)' : val}</span>
                `;
                checkboxContainer.appendChild(itemDiv);
            });
        }

        function closeExcelFilterMenu() {
            document.getElementById('excelFilterDropdown').style.display = 'none';
        }

        function closeAllFilterMenus(event) {
            closeExcelFilterMenu();
        }

        function filterExcelCheckboxList() {
            const q = document.getElementById('excelSearchBox').value.toLowerCase();
            const items = document.querySelectorAll('#excelCheckboxList .excel-filter-item');
            items.forEach(it => {
                const txt = it.textContent.toLowerCase();
                it.style.display = txt.includes(q) ? 'flex' : 'none';
            });
        }

        function toggleSelectAllCheckboxes(checked) {
            const chks = document.querySelectorAll('.excel-chk-item');
            chks.forEach(c => {
                if (c.offsetParent !== null) c.checked = checked;
            });
        }

        function updateSelectAllState() {
            const chks = Array.from(document.querySelectorAll('.excel-chk-item'));
            const allChecked = chks.every(c => c.checked);
            document.getElementById('chkSelectAll').checked = allChecked;
        }

        function applySort(dir) {
            const { tableId, colKey } = currentActiveContext;
            tableState[tableId].sortCol = colKey;
            tableState[tableId].sortDir = dir;
            processData(tableId);
            closeExcelFilterMenu();
        }

        function confirmColumnFilter() {
            const { tableId, colKey } = currentActiveContext;
            const chks = document.querySelectorAll('.excel-chk-item');
            const selected = [];

            chks.forEach(c => {
                if (c.checked) selected.push(c.value);
            });

            tableState[tableId].filters[colKey] = selected;
            processData(tableId);
            closeExcelFilterMenu();
        }

        function clearAllFilters(tableId) {
            tableState[tableId].filters = {};
            tableState[tableId].sortCol = null;
            tableState[tableId].sortDir = null;
            if (tableId === 'estoque') {
                tableState.estoque.metricFilter = null;
                highlightActiveMetricCard(null);
                document.getElementById('activeFilterBadge').innerText = 'Mostrando todas as localizações';
            }
            document.getElementById(tableId === 'estoque' ? 'searchEstoque' : 'searchHistorico').value = '';
            processData(tableId);
        }

        function updateFilterButtonStates(tableId) {
            const state = tableState[tableId];
            const isEstoque = tableId === 'estoque';
            const keys = isEstoque 
                ? ['item', 'central', 'tecnico', 'op', 'acervo', 'total'] 
                : ['data', 'item', 'tipo', 'qtd', 'origem', 'destino', 'obs'];

            keys.forEach(k => {
                const btn = document.getElementById(`fbtn-${tableId}-${k}`);
                if (btn) {
                    const hasFilter = state.filters[k] && state.filters[k].length > 0;
                    const hasSort = state.sortCol === k;
                    if (hasFilter || hasSort) {
                        btn.classList.add('active-filter');
                    } else {
                        btn.classList.remove('active-filter');
                    }
                }
            });
        }

        function switchTab(tab) {
            const secEstoque = document.getElementById('secEstoque');
            const secHistorico = document.getElementById('secHistorico');
            const btnEstoque = document.getElementById('btnTabEstoque');
            const btnHistorico = document.getElementById('btnTabHistorico');

            if (tab === 'estoque') {
                secEstoque.classList.remove('hidden');
                secHistorico.classList.add('hidden');
                btnEstoque.className = 'px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2';
                btnHistorico.className = 'px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2';
            } else {
                secEstoque.classList.add('hidden');
                secHistorico.classList.remove('hidden');
                btnHistorico.className = 'px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2';
                btnEstoque.className = 'px-4 py-1 rounded-md text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2';
            }
        }

        function syncData() {
            const btn = document.getElementById('btnSync');
            const icon = document.getElementById('iconSync');
            const lastUpdateText = document.getElementById('lastUpdateText');
            const toast = document.getElementById('toastSync');

            icon.classList.add('fa-spin');
            btn.disabled = true;
            btn.classList.add('opacity-75');

            setTimeout(() => {
                processData('estoque');
                processData('historico');

                const now = new Date();
                const dia = String(now.getDate()).padStart(2, '0');
                const mes = String(now.getMonth() + 1).padStart(2, '0');
                const ano = now.getFullYear();
                const horas = String(now.getHours()).padStart(2, '0');
                const minutos = String(now.getMinutes()).padStart(2, '0');

                lastUpdateText.innerText = `${dia}/${mes}/${ano} ${horas}:${minutos}`;

                icon.classList.remove('fa-spin');
                btn.disabled = false;
                btn.classList.remove('opacity-75');

                toast.classList.remove('translate-y-20', 'opacity-0', 'pointer-events-none');
                setTimeout(() => {
                    toast.classList.add('translate-y-20', 'opacity-0', 'pointer-events-none');
                }, 3000);

            }, 600);
        }

        document.addEventListener('DOMContentLoaded', () => {
            processData('estoque');
            processData('historico');
        });
    </script>
</body>
</html>
