<html lang="pt-BR" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Estoque - Grupo Forte Protege</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Ícones -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#eff6ff',
                            500: '#3b82f6',
                            600: '#2563eb',
                            900: '#1e3a8a',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #0f172a;
        }
        ::-webkit-scrollbar-thumb {
            background: #334155;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #475569;
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 font-sans min-h-screen flex flex-col antialiased relative">

    <!-- TOAST DE NOTIFICAÇÃO DA SINCRONIZAÇÃO -->
    <div id="toastSync" class="fixed bottom-5 right-5 z-50 bg-emerald-600 text-white px-4 py-3 rounded-xl shadow-2xl flex items-center gap-3 transition-all duration-300 transform translate-y-20 opacity-0 pointer-events-none">
        <i class="fa-solid fa-circle-check text-lg"></i>
        <span class="text-xs font-semibold">Dados sincronizados com sucesso!</span>
    </div>

    <!-- CABEÇALHO -->
    <header class="bg-slate-900 border-b border-slate-800 sticky top-0 z-40 shadow-2xl">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex flex-col lg:flex-row items-center justify-between gap-4">
            
            <!-- LOGO E TÍTULO -->
            <div class="flex items-center gap-4 w-full lg:w-auto justify-between lg:justify-start">
                <div class="flex items-center gap-3">
                    <!-- Logo na mesma pasta: logo.png -->
                    <img src="logo.png" alt="Logo Grupo Forte Protege" class="h-12 w-auto object-contain max-w-[160px]" onerror="this.onerror=null; this.src='https://via.placeholder.com/150x50/1e293b/38bdf8?text=GF+PROTEGE'">
                    <div class="h-8 w-px bg-slate-700 hidden sm:block"></div>
                    <div>
                        <h1 class="text-xl font-bold text-white tracking-tight flex items-center gap-2">
                            Estoque & Operações
                        </h1>
                        <p class="text-xs text-slate-400 font-medium">TI • CFTV • Conectividade</p>
                    </div>
                </div>
            </div>

            <!-- ORIGEM DOS DADOS & BOTÃO SINCRONIZAR -->
            <div class="flex flex-wrap items-center justify-center lg:justify-end gap-3 w-full lg:w-auto">
                
                <!-- LINK PLANILHA ORIGEM -->
                <a href="https://docs.google.com/spreadsheets/d/1v-MZ_ga3DtOk2UfDxRZNV0awVWd3jdo1hSzCwUyvARE/edit?usp=sharing" target="_blank" rel="noopener noreferrer" class="flex items-center gap-2 bg-slate-950 hover:bg-slate-800 border border-slate-800 text-emerald-400 px-3 py-2 rounded-xl text-xs font-medium transition shadow-sm group">
                    <i class="fa-solid fa-file-excel text-emerald-500 group-hover:scale-110 transition-transform"></i>
                    <span>Planilha Google Sheets</span>
                    <i class="fa-solid fa-arrow-up-right-from-square text-[10px] text-slate-500 ml-1"></i>
                </a>

                <!-- BADGE DA ÚLTIMA ATUALIZAÇÃO -->
                <div class="bg-slate-950 border border-slate-800 px-3 py-2 rounded-xl text-xs text-slate-400 flex items-center gap-2">
                    <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                    <span>Atualizado: <strong id="lastUpdateText" class="text-slate-200">22/09/2026 16:49</strong></span>
                </div>

                <!-- BOTÃO SINCRONIZAR MANUAL -->
                <button onclick="syncData()" id="btnSync" class="flex items-center gap-2 bg-slate-800 hover:bg-slate-700 active:bg-slate-600 text-slate-200 border border-slate-700 px-3 py-2 rounded-xl text-xs font-semibold transition shadow-sm">
                    <i id="iconSync" class="fa-solid fa-rotate text-blue-400"></i>
                    <span>Sincronizar</span>
                </button>

                <!-- BOTAO ALTERNAR ABAS -->
                <div class="flex items-center bg-slate-950 p-1 rounded-xl border border-slate-800 ml-0 lg:ml-2">
                    <button onclick="switchTab('estoque')" id="btnTabEstoque" class="px-4 py-1.5 rounded-lg text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2">
                        <i class="fa-solid fa-boxes-stacked"></i> Estoque
                    </button>
                    <button onclick="switchTab('historico')" id="btnTabHistorico" class="px-4 py-1.5 rounded-lg text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2">
                        <i class="fa-solid fa-clock-rotate-left"></i> Histórico
                    </button>
                </div>

            </div>
        </div>
    </header>

    <!-- CONTEÚDO PRINCIPAL -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 flex-1 w-full space-y-8">

        <!-- METRICAS / CARDS (KPIs) -->
        <section class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-5 gap-4">
            
            <!-- CENTRAL -->
            <div class="bg-slate-900 border border-slate-800 p-4 rounded-xl shadow-lg relative overflow-hidden group hover:border-slate-700 transition">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Central</span>
                    <div class="w-8 h-8 rounded-lg bg-blue-500/10 text-blue-400 flex items-center justify-center">
                        <i class="fa-solid fa-warehouse text-sm"></i>
                    </div>
                </div>
                <div class="text-3xl font-extrabold text-white mt-3" id="kpiCentral">96</div>
                <span class="text-[11px] text-slate-400">Galpão principal</span>
            </div>

            <!-- TÉCNICO MARCELO -->
            <div class="bg-slate-900 border border-slate-800 p-4 rounded-xl shadow-lg relative overflow-hidden group hover:border-slate-700 transition">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Técnico Marcelo</span>
                    <div class="w-8 h-8 rounded-lg bg-amber-500/10 text-amber-400 flex items-center justify-center">
                        <i class="fa-solid fa-user-gear text-sm"></i>
                    </div>
                </div>
                <div class="text-3xl font-extrabold text-amber-400 mt-3" id="kpiTecnico">45</div>
                <span class="text-[11px] text-slate-400">Material em campo</span>
            </div>

            <!-- ESTOQUE OPERACIONAL -->
            <div class="bg-slate-900 border border-slate-800 p-4 rounded-xl shadow-lg relative overflow-hidden group hover:border-slate-700 transition">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Estoque Operacional</span>
                    <div class="w-8 h-8 rounded-lg bg-emerald-500/10 text-emerald-400 flex items-center justify-center">
                        <i class="fa-solid fa-truck-ramp-box text-sm"></i>
                    </div>
                </div>
                <div class="text-3xl font-extrabold text-emerald-400 mt-3" id="kpiOperacional">15</div>
                <span class="text-[11px] text-slate-400">Pronta entrega</span>
            </div>

            <!-- ACERVO OPERACIONAL -->
            <div class="bg-slate-900 border border-slate-800 p-4 rounded-xl shadow-lg relative overflow-hidden group hover:border-slate-700 transition">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-slate-400">Acervo</span>
                    <div class="w-8 h-8 rounded-lg bg-purple-500/10 text-purple-400 flex items-center justify-center">
                        <i class="fa-solid fa-laptop text-sm"></i>
                    </div>
                </div>
                <div class="text-3xl font-extrabold text-purple-400 mt-3" id="kpiAcervo">5</div>
                <span class="text-[11px] text-slate-400">Patrimônio interno</span>
            </div>

            <!-- TOTAL CONSOLIDADO -->
            <div class="col-span-2 sm:col-span-1 bg-gradient-to-br from-slate-900 to-blue-950 border border-blue-800/50 p-4 rounded-xl shadow-lg relative overflow-hidden">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-semibold uppercase text-blue-300">Total Físico</span>
                    <div class="w-8 h-8 rounded-lg bg-blue-500/20 text-blue-300 flex items-center justify-center">
                        <i class="fa-solid fa-cubes text-sm"></i>
                    </div>
                </div>
                <div class="text-3xl font-extrabold text-blue-300 mt-3" id="kpiTotal">161</div>
                <span class="text-[11px] text-blue-200/70">Itens rastreados</span>
            </div>
        </section>

        <!-- ABA 1: SALDO DE ESTOQUE -->
        <section id="secEstoque" class="bg-slate-900 border border-slate-800 rounded-2xl shadow-xl overflow-hidden">
            <div class="p-6 border-b border-slate-800 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-slate-900/50">
                <div>
                    <h2 class="text-lg font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-table-list text-blue-500"></i> Disponibilidade por Equipamento
                    </h2>
                    <p class="text-xs text-slate-400 mt-0.5">Visão consolidada da distribuição dos ativos</p>
                </div>
                <div class="relative w-full sm:w-80">
                    <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-3 text-slate-500 text-sm"></i>
                    <input type="text" id="searchEstoque" onkeyup="filterEstoque()" placeholder="Buscar equipamento..." class="w-full bg-slate-950 border border-slate-800 rounded-xl pl-10 pr-4 py-2 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition">
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full text-left text-sm">
                    <thead>
                        <tr class="bg-slate-950 text-slate-400 uppercase text-[11px] font-bold tracking-wider border-b border-slate-800">
                            <th class="py-4 px-6">Equipamento</th>
                            <th class="py-4 px-4 text-center">Central</th>
                            <th class="py-4 px-4 text-center">Técnico Marcelo</th>
                            <th class="py-4 px-4 text-center">Estoque Op.</th>
                            <th class="py-4 px-4 text-center">Acervo Op.</th>
                            <th class="py-4 px-6 text-center">Total Geral</th>
                        </tr>
                    </thead>
                    <tbody id="tbodyEstoque" class="divide-y divide-slate-800/60 bg-slate-900">
                        <!-- Preenchido via JS -->
                    </tbody>
                </table>
            </div>
        </section>

        <!-- ABA 2: HISTÓRICO DE MOVIMENTAÇÕES -->
        <section id="secHistorico" class="bg-slate-900 border border-slate-800 rounded-2xl shadow-xl overflow-hidden hidden">
            <div class="p-6 border-b border-slate-800 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-slate-900/50">
                <div>
                    <h2 class="text-lg font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-arrow-right-arrow-left text-blue-500"></i> Histórico de Entradas e Saídas
                    </h2>
                    <p class="text-xs text-slate-400 mt-0.5">Registro auditável de transferências e instalações</p>
                </div>
                <div class="flex flex-col sm:flex-row gap-3 w-full sm:w-auto">
                    <select id="filterTipo" onchange="filterHistorico()" class="bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-sm text-white focus:outline-none focus:border-blue-500">
                        <option value="todos">Todos os Tipos</option>
                        <option value="Entrada">Apenas Entradas</option>
                        <option value="Saída">Apenas Saídas</option>
                    </select>
                    <div class="relative w-full sm:w-72">
                        <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-3 text-slate-500 text-sm"></i>
                        <input type="text" id="searchHistorico" onkeyup="filterHistorico()" placeholder="Buscar projeto, origem, OS..." class="w-full bg-slate-950 border border-slate-800 rounded-xl pl-10 pr-4 py-2 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition">
                    </div>
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full text-left text-sm">
                    <thead>
                        <tr class="bg-slate-950 text-slate-400 uppercase text-[11px] font-bold tracking-wider border-b border-slate-800">
                            <th class="py-4 px-6">Data</th>
                            <th class="py-4 px-6">Equipamento</th>
                            <th class="py-4 px-4 text-center">Tipo</th>
                            <th class="py-4 px-4 text-center">Qtd</th>
                            <th class="py-4 px-4">Origem</th>
                            <th class="py-4 px-4">Destino</th>
                            <th class="py-4 px-6">Observações / Projeto</th>
                        </tr>
                    </thead>
                    <tbody id="tbodyHistorico" class="divide-y divide-slate-800/60 bg-slate-900">
                        <!-- Preenchido via JS -->
                    </tbody>
                </table>
            </div>
        </section>

    </main>

    <!-- RODAPÉ -->
    <footer class="bg-slate-900 border-t border-slate-800 py-4 text-center text-xs text-slate-500">
        Grupo Forte Protege &copy; 2026 — Todos os direitos reservados.
    </footer>

    <!-- SCRIPT DE DADOS E COMPORTAMENTO -->
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
                tbody.innerHTML = `<tr><td colspan="6" class="p-8 text-center text-slate-500">Nenhum equipamento encontrado.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-800/80 transition duration-150 border-b border-slate-800/40';
                tr.innerHTML = `
                    <td class="py-3.5 px-6 font-semibold text-slate-100">${row.item}</td>
                    <td class="py-3.5 px-4 text-center text-slate-300">${row.central || '-'}</td>
                    <td class="py-3.5 px-4 text-center text-amber-300 font-medium">${row.tecnico || '-'}</td>
                    <td class="py-3.5 px-4 text-center text-emerald-300 font-medium">${row.op || '-'}</td>
                    <td class="py-3.5 px-4 text-center text-purple-300 font-medium">${row.acervo || '-'}</td>
                    <td class="py-3.5 px-6 text-center font-bold text-blue-400 text-base">${row.total}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function renderHistorico(data) {
            const tbody = document.getElementById('tbodyHistorico');
            tbody.innerHTML = '';

            if(data.length === 0) {
                tbody.innerHTML = `<tr><td colspan="7" class="p-8 text-center text-slate-500">Nenhum registro encontrado.</td></tr>`;
                return;
            }

            data.forEach(row => {
                const badgeClass = row.tipo === 'Entrada' 
                    ? 'bg-emerald-500/10 text-emerald-400 border-emerald-500/20' 
                    : 'bg-rose-500/10 text-rose-400 border-rose-500/20';
                
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-800/80 transition duration-150 border-b border-slate-800/40';
                tr.innerHTML = `
                    <td class="py-3.5 px-6 text-slate-400 font-mono text-xs whitespace-nowrap">${row.data}</td>
                    <td class="py-3.5 px-6 font-medium text-slate-100">${row.item}</td>
                    <td class="py-3.5 px-4 text-center">
                        <span class="px-2.5 py-1 text-xs rounded-md border ${badgeClass} font-semibold">${row.tipo}</span>
                    </td>
                    <td class="py-3.5 px-4 text-center font-bold text-white">${row.qtd}</td>
                    <td class="py-3.5 px-4 text-slate-300">${row.origem}</td>
                    <td class="py-3.5 px-4 text-slate-300">${row.destino}</td>
                    <td class="py-3.5 px-6 text-slate-400 italic">${row.obs || '-'}</td>
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
                btnEstoque.className = 'px-4 py-1.5 rounded-lg text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2';
                btnHistorico.className = 'px-4 py-1.5 rounded-lg text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2';
            } else {
                secEstoque.classList.add('hidden');
                secHistorico.classList.remove('hidden');
                btnHistorico.className = 'px-4 py-1.5 rounded-lg text-xs font-semibold transition-all duration-200 bg-blue-600 text-white shadow-md flex items-center gap-2';
                btnEstoque.className = 'px-4 py-1.5 rounded-lg text-xs font-semibold transition-all duration-200 text-slate-400 hover:text-white flex items-center gap-2';
            }
        }

        // FUNÇÃO DE SINCRONIZAÇÃO MANUAL
        function syncData() {
            const btn = document.getElementById('btnSync');
            const icon = document.getElementById('iconSync');
            const lastUpdateText = document.getElementById('lastUpdateText');
            const toast = document.getElementById('toastSync');

            // Feedback visual de carregamento
            icon.classList.add('fa-spin');
            btn.disabled = true;
            btn.classList.add('opacity-75');

            setTimeout(() => {
                // Atualiza visualização das tabelas
                renderEstoque(estoqueData);
                renderHistorico(historicoData);

                // Atualiza timestamp para o momento atual
                const now = new Date();
                const dia = String(now.getDate()).padStart(2, '0');
                const mes = String(now.getMonth() + 1).padStart(2, '0');
                const ano = now.getFullYear();
                const horas = String(now.getHours()).padStart(2, '0');
                const minutos = String(now.getMinutes()).padStart(2, '0');

                lastUpdateText.innerText = `${dia}/${mes}/${ano} ${horas}:${minutos}`;

                // Restaura o botão
                icon.classList.remove('fa-spin');
                btn.disabled = false;
                btn.classList.remove('opacity-75');

                // Exibe toast de notificação
                toast.classList.remove('translate-y-20', 'opacity-0', 'pointer-events-none');
                setTimeout(() => {
                    toast.classList.add('translate-y-20', 'opacity-0', 'pointer-events-none');
                }, 3000);

            }, 800);
        }

        document.addEventListener('DOMContentLoaded', () => {
            renderEstoque(estoqueData);
            renderHistorico(historicoData);
        });
    </script>
</body>
</html>
