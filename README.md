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
        /* ESTILOS DE ALTO CONTRASTE COM TEXTO PRETO NA TABELA */
        body {
            background-color: #0b0f19 !important;
            color: #f1f5f9 !important;
            font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
        }

        .card-panel {
            background-color: #111827 !important;
            border: 1px solid #1f2937 !important;
        }

        /* TABELA COM FUNDO CLARO E TEXTO PRETO FORÇADO */
        table.custom-table {
            background-color: #ffffff !important;
            width: 100%;
            border-collapse: collapse;
        }

        /* Cabeçalho Escuro com Letras Brancas */
        table.custom-table th {
            background-color: #1e293b !important;
            color: #ffffff !important;
            font-size: 0.75rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            padding: 0.875rem 1rem;
            border: 1px solid #334155 !important;
        }

        /* Células com Fundo Branco e Texto PRETO ABSOLUTO */
        table.custom-table td {
            background-color: #ffffff !important;
            color: #000000 !important; /* PRETO ABSOLUTO */
            font-weight: 600 !important;
            font-size: 0.875rem !important;
            padding: 0.75rem 1rem;
            border: 1px solid #e2e8f0 !important;
        }

        /* Efeito ao passar o mouse */
        table.custom-table tr:hover td {
            background-color: #f1f5f9 !important;
        }

        /* Texto das Células */
        .text-black-bold {
            color: #000000 !important;
            font-weight: 700 !important;
        }

        /* Barra de rolagem */
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #0b0f19; }
        ::-webkit-scrollbar-thumb { background: #374151; border-radius: 4px; }
    </style>
</head>
<body class="min-h-screen flex flex-col antialiased">

    <!-- TOAST DE NOTIFICAÇÃO DA SINCRONIZAÇÃO -->
    <div id="toastSync" class="fixed bottom-5 right-5 z-50 bg-emerald-600 text-white px-4 py-3 rounded-xl shadow-2xl flex items-center gap-3 transition-all duration-300 transform translate-y-20 opacity-0 pointer-events-none">
        <i class="fa-solid fa-circle-check text-lg"></i>
        <span class="text-xs font-semibold">Dados sincronizados com sucesso!</span>
    </div>

    <!-- CABEÇALHO CLEAN -->
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

            <!-- CONTROLES DO CABEÇALHO -->
            <div class="flex flex-wrap items-center justify-center lg:justify-end gap-3 w-full lg:w-auto">
                
                <!-- BADGE ATUALIZAÇÃO -->
                <div class="bg-slate-950 border border-slate-800 px-3 py-1.5 rounded-lg text-xs text-slate-400 flex items-center gap-2">
                    <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                    <span>Atualizado: <strong id="lastUpdateText" class="text-slate-200">22/09/2026 16:49</strong></span>
                </div>

                <!-- BOTÃO SINCRONIZAR -->
                <button onclick="syncData()" id="btnSync" class="flex items-center gap-2 bg-slate-800 hover:bg-slate-700 active:bg-slate-600 text-slate-200 border border-slate-700 px-3 py-1.5 rounded-lg text-xs font-semibold transition shadow-sm">
                    <i id="iconSync" class="fa-solid fa-rotate text-blue-400"></i>
                    <span>Sincronizar</span>
                </button>

                <!-- SELETOR DE ABAS -->
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

        <!-- CARDS DE MÉTRICAS (KPIs) -->
        <section class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-5 gap-4">
            
            <div class="card-panel p-4 rounded-xl shadow-sm">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400">
                    <span>Central</span>
                    <i class="fa-solid fa-warehouse text-blue-400"></i>
                </div>
                <div class="text-2xl font-bold text-white mt-2" id="kpiCentral">96</div>
                <span class="text-[11px] text-slate-500">Galpão principal</span>
            </div>

            <div class="card-panel p-4 rounded-xl shadow-sm">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400">
                    <span>Técnico Marcelo</span>
                    <i class="fa-solid fa-user-gear text-amber-400"></i>
                </div>
                <div class="text-2xl font-bold text-amber-400 mt-2" id="kpiTecnico">45</div>
                <span class="text-[11px] text-slate-500">Material em campo</span>
            </div>

            <div class="card-panel p-4 rounded-xl shadow-sm">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400">
                    <span>Estoque Op.</span>
                    <i class="fa-solid fa-truck-ramp-box text-emerald-400"></i>
                </div>
                <div class="text-2xl font-bold text-emerald-400 mt-2" id="kpiOperacional">15</div>
                <span class="text-[11px] text-slate-500">Pronta entrega</span>
            </div>

            <div class="card-panel p-4 rounded-xl shadow-sm">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-slate-400">
                    <span>Acervo</span>
                    <i class="fa-solid fa-laptop text-purple-400"></i>
                </div>
                <div class="text-2xl font-bold text-purple-400 mt-2" id="kpiAcervo">5</div>
                <span class="text-[11px] text-slate-500">Patrimônio interno</span>
            </div>

            <div class="col-span-2 sm:col-span-1 card-panel p-4 rounded-xl shadow-sm bg-gradient-to-br from-slate-900 to-blue-950 border-blue-900/50">
                <div class="flex items-center justify-between text-xs font-semibold uppercase text-blue-300">
                    <span>Total Físico</span>
                    <i class="fa-solid fa-cubes text-blue-400"></i>
                </div>
                <div class="text-2xl font-bold text-blue-300 mt-2" id="kpiTotal">161</div>
                <span class="text-[11px] text-blue-200/70">Itens monitorados</span>
            </div>
        </section>

        <!-- ABA 1: SALDO DE ESTOQUE -->
        <section id="secEstoque" class="card-panel rounded-xl shadow-lg overflow-hidden">
            <div class="p-5 border-b border-slate-800 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                <div>
                    <h2 class="text-base font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-list-check text-blue-500"></i> Disponibilidade por Equipamento
                    </h2>
                    <p class="text-xs text-slate-400">Saldo atual de cada item por local de alocação</p>
                </div>
                <div class="relative w-full sm:w-72">
                    <i class="fa-solid fa-magnifying-glass absolute left-3 top-2.5 text-slate-500 text-sm"></i>
                    <input type="text" id="searchEstoque" onkeyup="filterEstoque()" placeholder="Buscar equipamento..." class="w-full bg-slate-950 border border-slate-800 rounded-lg pl-9 pr-4 py-1.5 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 transition">
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="custom-table">
                    <thead>
                        <tr>
                            <th class="text-left">Equipamento</th>
                            <th class="text-center">Central</th>
                            <th class="text-center">Técnico Marcelo</th>
                            <th class="text-center">Estoque Op.</th>
                            <th class="text-center">Acervo Op.</th>
                            <th class="text-center">Total Geral</th>
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
                    <p class="text-xs text-slate-400">Registro auditável de transferências e atendimentos</p>
                </div>
                <div class="flex flex-col sm:flex-row gap-2 w-full sm:w-auto">
                    <select id="filterTipo" onchange="filterHistorico()" class="bg-slate-950 border border-slate-800 rounded-lg px-3 py-1.5 text-sm text-white focus:outline-none focus:border-blue-500">
                        <option value="todos">Todos os Tipos</option>
                        <option value="Entrada">Entradas</option>
                        <option value="Saída">Saídas</option>
                    </select>
                    <div class="relative w-full sm:w-64">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-2.5 text-slate-500 text-sm"></i>
                        <input type="text" id="searchHistorico" onkeyup="filterHistorico()" placeholder="Buscar projeto, origem..." class="w-full bg-slate-950 border border-slate-800 rounded-lg pl-9 pr-4 py-1.5 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 transition">
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="custom-table">
                    <thead>
                        <tr>
                            <th class="text-left">Data</th>
                            <th class="text-left">Equipamento</th>
                            <th class="text-center">Tipo</th>
                            <th class="text-center">Qtd</th>
                            <th class="text-left">Origem</th>
                            <th class="text-left">Destino</th>
                            <th class="text-left">Observações / Projeto</th>
                        </tr>
                    </thead>
                    <tbody id="tbodyHistorico">
                        <!-- Preenchido via JS -->
                    </tbody>
                </table>
            </div>
        </section>

    </main>

    <!-- RODAPÉ -->
    <footer class="bg-slate-900 border-t border-slate-800 py-3 text-center text-xs text-slate-500">
        Grupo Forte Protege &copy; 2026 — Controle Interno Operacional
    </footer>

    <!-- SCRIPT DE DADOS E LÓGICA -->
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

        function renderEstoque(data) {
            const tbody = document.getElementById('tbodyEstoque');
            tbody.innerHTML = '';
            
            if(data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="6" class="p-6 text-center text-slate-500">Nenhum equipamento encontrado.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="text-black-bold">${row.item}</td>
                    <td class="text-center text-black-bold">${row.central || '-'}</td>
                    <td class="text-center text-black-bold">${row.tecnico || '-'}</td>
                    <td class="text-center text-black-bold">${row.op || '-'}</td>
                    <td class="text-center text-black-bold">${row.acervo || '-'}</td>
                    <td class="text-center text-black-bold text-base" style="color: #2563eb !important;">${row.total}</td>
                `;
                tbody.appendChild(tr);
            });
        }

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
                    <td class="text-black-bold font-mono text-xs whitespace-nowrap">${row.data}</td>
                    <td class="text-black-bold">${row.item}</td>
                    <td class="text-center">
                        <span class="px-2 py-0.5 text-xs rounded border ${badgeClass} font-bold">${row.tipo}</span>
                    </td>
                    <td class="text-center text-black-bold">${row.qtd}</td>
                    <td class="text-black-bold">${row.origem}</td>
                    <td class="text-black-bold">${row.destino}</td>
                    <td class="text-black-bold italic">${row.obs || '-'}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function filterEstoque() {
            const query = document.getElementById('searchEstoque').value.toLowerCase();
            const filtered = estoqueData.filter(d => d.item.toLowerCase().includes(query));
            renderEstoque(filtered);
        }

        function filterHistorico() {
            const query = document.getElementById('searchHistorico').value.toLowerCase();
            const tipo = document.getElementById('filterTipo').value;

            const filtered = historicoData.filter(d => {
                const matchesSearch = d.item.toLowerCase().includes(query) || 
                                      d.origem.toLowerCase().includes(query) || 
                                      d.destino.toLowerCase().includes(query) ||
                                      d.obs.toLowerCase().includes(query);
                const matchesTipo = tipo === 'todos' || d.tipo === tipo;
                return matchesSearch && matchesTipo;
            });
            renderHistorico(filtered);
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
                renderEstoque(estoqueData);
                renderHistorico(historicoData);

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
            renderEstoque(estoqueData);
            renderHistorico(historicoData);
        });
    </script>
</body>
</html>
