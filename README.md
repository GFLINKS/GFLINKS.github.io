<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Sistema de Gestão - Chamados e Rotas</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css" rel="stylesheet">
  <style>
    body { background-color: #f4f6f9; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
    .navbar-brand { font-weight: 700; letter-spacing: 0.5px; }
    .card-header { font-weight: 600; background-color: #eef2f7; }
    .table-sm th, .table-sm td { font-size: 0.88rem; vertical-align: middle; }
    .badge-pendente { background-color: #ffc107; color: #000; }
    .badge-agendado { background-color: #0dcaf0; color: #000; }
    .nav-tabs .nav-link.active { font-weight: bold; border-bottom: 3px solid #0d6efd; background-color: #fff; }
    .form-section { background: #fff; padding: 25px; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.06); }
  </style>
</head>
<body>

<!-- BARRA DE NAVEGAÇÃO SUPERIOR -->
<nav class="navbar navbar-expand-lg navbar-dark bg-primary shadow-sm mb-4">
  <div class="container-fluid px-4">
    <a class="navbar-brand" href="<?= webAppUrl ?>" target="_top">
      <i class="bi bi-headset me-2"></i>Gestão de Chamados e Rotas
    </a>
    <div class="d-flex align-items-center">
      <span class="badge bg-light text-primary me-3 d-none d-md-inline">Sistema Online</span>
      <a href="<?= webAppUrl ?>" class="btn btn-outline-light btn-sm" title="Recarregar App">
        <i class="bi bi-arrow-clockwise me-1"></i>Atualizar
      </a>
    </div>
  </div>
</nav>

<div class="container-fluid px-4">

  <!-- NAVEGAÇÃO POR ABAS -->
  <ul class="nav nav-tabs mb-4" id="mainTab" role="tablist">
    <li class="nav-item">
      <button class="nav-link active" id="abertos-tab" data-bs-toggle="tab" data-bs-target="#abertos" type="button">
        <i class="bi bi-list-task me-1"></i>Chamados Abertos
      </button>
    </li>
    <li class="nav-item">
      <button class="nav-link" id="novo-chamado-tab" data-bs-toggle="tab" data-bs-target="#novo-chamado" type="button">
        <i class="bi bi-plus-circle me-1"></i>+ Inserir Chamado
      </button>
    </li>
    <li class="nav-item">
      <button class="nav-link" id="rotas-tab" data-bs-toggle="tab" data-bs-target="#rotas" type="button">
        <i class="bi bi-geo-alt me-1"></i>+ Inserir Rota
      </button>
    </li>
  </ul>

  <div class="tab-content" id="mainTabContent">
    
    <!-- ABA 1: LISTA E BUSCA DE CHAMADOS ABERTOS -->
    <div class="tab-pane fade show active" id="abertos">
      <div class="card shadow-sm mb-4">
        <div class="card-header bg-white py-3">
          <div class="row g-2 align-items-center">
            <div class="col-md-5">
              <div class="input-group">
                <span class="input-group-text"><i class="bi bi-search"></i></span>
                <input type="text" id="inputBusca" class="form-control" placeholder="Buscar por O.S, Loja, Serviço ou UF..." onkeyup="filtrarTabela()">
              </div>
            </div>
            <div class="col-md-3">
              <select id="filterStatus" class="form-select" onchange="filtrarTabela()">
                <option value="">Todos os Status</option>
                <option value="PENDENTE">PENDENTE</option>
                <option value="AGENDADO">AGENDADO</option>
              </select>
            </div>
            <div class="col-md-4 text-end">
              <span id="totalRegistros" class="badge bg-secondary p-2 me-2">0 Registros</span>
              <button class="btn btn-sm btn-outline-primary" onclick="carregarDados()">
                <i class="bi bi-arrow-repeat me-1"></i>Atualizar
              </button>
            </div>
          </div>
        </div>

        <div class="card-body p-0 table-responsive">
          <table class="table table-hover table-striped mb-0 table-sm align-middle">
            <thead class="table-dark">
              <tr>
                <th>O.S</th>
                <th>Abertura</th>
                <th>Prazo</th>
                <th>Loja</th>
                <th>Tipo Loja</th>
                <th>Tipo Serviço</th>
                <th>UF</th>
                <th>Status</th>
                <th>Previsão</th>
                <th>Observações</th>
                <th class="text-center">Ações</th>
              </tr>
            </thead>
            <tbody id="tblChamadosBody">
              <tr><td colspan="11" class="text-center py-4 text-muted"><i class="bi bi-hourglass-split me-2"></i>Carregando dados da planilha...</td></tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- ABA 2: INSERIR CHAMADO ABERTO -->
    <div class="tab-pane fade" id="novo-chamado">
      <div class="form-section max-w-800 mx-auto">
        <h5 class="mb-3 text-primary"><i class="bi bi-file-earmark-plus me-2"></i>Cadastrar Novo Chamado</h5>
        <form id="formChamado" onsubmit="handleSalvarChamado(event)">
          <div class="row g-3">
            <div class="col-md-4">
              <label class="form-label fw-bold">Número O.S *</label>
              <input type="text" class="form-control" name="os" required placeholder="Ex: 4100">
            </div>
            <div class="col-md-4">
              <label class="form-label">Data Abertura</label>
              <input type="date" class="form-control" name="abertura" id="campoAbertura">
            </div>
            <div class="col-md-4">
              <label class="form-label">Prazo</label>
              <input type="date" class="form-control" name="prazo">
            </div>
            <div class="col-md-6">
              <label class="form-label fw-bold">Loja (Sigla)</label>
              <input type="text" class="form-control text-uppercase" name="loja" id="inputLojaForm" list="datalistLojas" placeholder="Ex: OSA">
              <datalist id="datalistLojas"></datalist>
            </div>
            <div class="col-md-6">
              <label class="form-label">Tipo de Loja</label>
              <input type="text" class="form-control" name="tipoLoja" id="inputTipoLojaForm" list="datalistTiposLoja" placeholder="Ex: In Store">
              <datalist id="datalistTiposLoja"></datalist>
            </div>
            <div class="col-md-6">
              <label class="form-label">Tipo de Serviço</label>
              <input type="text" class="form-control" name="tipo" id="inputTipoServicoForm" list="datalistTiposServico" placeholder="Ex: Manutenção CFTV">
              <datalist id="datalistTiposServico"></datalist>
            </div>
            <div class="col-md-6">
              <label class="form-label">Estado (UF)</label>
              <input type="text" class="form-control text-uppercase" name="estado" maxlength="2" placeholder="Ex: SP">
            </div>
            <div class="col-md-6">
              <label class="form-label">Status</label>
              <select class="form-select" name="status">
                <option value="PENDENTE">PENDENTE</option>
                <option value="AGENDADO">AGENDADO</option>
              </select>
            </div>
            <div class="col-md-6">
              <label class="form-label">Previsão de Atendimento</label>
              <input type="date" class="form-control" name="previsao">
            </div>
            <div class="col-12">
              <label class="form-label">Observações / Detalhes</label>
              <textarea class="form-control" name="observacoes" rows="3" placeholder="Insira informações adicionais do chamado..."></textarea>
            </div>
          </div>
          <button type="submit" class="btn btn-primary mt-4 w-100 py-2 fw-bold">
            <i class="bi bi-check-circle me-1"></i>Salvar Chamado
          </button>
        </form>
      </div>
    </div>

    <!-- ABA 3: INSERIR ROTA -->
    <div class="tab-pane fade" id="rotas">
      <div class="form-section max-w-800 mx-auto">
        <h5 class="mb-3 text-success"><i class="bi bi-signpost-split me-2"></i>Inserir Nova Rota</h5>
        <form id="formRota" onsubmit="handleSalvarRota(event)">
          <div class="row g-3">
            <div class="col-md-6">
              <label class="form-label fw-bold">Data da Rota *</label>
              <input type="date" class="form-control" name="data" id="campoDataRota" required>
            </div>
            <div class="col-md-6">
              <label class="form-label fw-bold">Sigla da Loja *</label>
              <input type="text" class="form-control text-uppercase" name="sigla" id="inputSiglaRota" list="datalistLojas" onchange="autoPreencherEndereco()" required placeholder="Ex: SAM">
            </div>
            <div class="col-12">
              <label class="form-label fw-bold">Endereço Completo</label>
              <input type="text" class="form-control" name="endereco" id="inputEnderecoRota" placeholder="Ex: Rua Treze de Maio, 1947 - São Paulo - SP">
            </div>
            <div class="col-12">
              <label class="form-label fw-bold">Serviço / O.S</label>
              <input type="text" class="form-control" name="servicoOs" id="inputServicoRota" placeholder="Ex: Manutenção CFTV O.S 3824">
            </div>
            <div class="col-md-6">
              <label class="form-label">Prazo</label>
              <input type="date" class="form-control" name="prazo" id="inputPrazoRota">
            </div>
            <div class="col-md-6">
              <label class="form-label">Prioridade</label>
              <select class="form-select" name="prioridade">
                <option value="BAIXA">BAIXA</option>
                <option value="MÉDIA" selected>MÉDIA</option>
                <option value="ALTA">ALTA</option>
              </select>
            </div>
          </div>
          <button type="submit" class="btn btn-success mt-4 w-100 py-2 fw-bold">
            <i class="bi bi-geo-alt-fill me-1"></i>Adicionar à Rota
          </button>
        </form>
      </div>
    </div>

  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
  let todosChamados = [];
  let mapaEnderecosGlobal = {};

  document.addEventListener('DOMContentLoaded', () => {
    // Seta data de hoje nos inputs de data
    const hoje = new Date().toISOString().split('T')[0];
    document.getElementById('campoAbertura').value = hoje;
    document.getElementById('campoDataRota').value = hoje;
    carregarDados();
  });

  function carregarDados() {
    google.script.run.withSuccessHandler(renderizarTela).getDadosIniciais();
  }

  function renderizarTela(data) {
    todosChamados = data.chamadosAbertos || [];
    mapaEnderecosGlobal = data.opcoes.mapaEnderecos || {};

    renderizarTabela(todosChamados);

    // Preenche Datalists para autocomplete
    preencherDatalist('datalistLojas', data.opcoes.lojas);
    preencherDatalist('datalistTiposLoja', data.opcoes.tiposLoja);
    preencherDatalist('datalistTiposServico', data.opcoes.tiposServico);
  }

  function renderizarTabela(lista) {
    const tbody = document.getElementById('tblChamadosBody');
    tbody.innerHTML = '';

    document.getElementById('totalRegistros').innerText = `${lista.length} Registros`;

    if (lista.length === 0) {
      tbody.innerHTML = '<tr><td colspan="11" class="text-center py-4 text-muted">Nenhum chamado aberto encontrado.</td></tr>';
      return;
    }

    lista.forEach(item => {
      const tr = document.createElement('tr');
      const isAgendado = item.status === 'AGENDADO';
      const badgeClass = isAgendado ? 'badge-agendado' : 'badge-pendente';

      tr.innerHTML = `
        <td><strong>#${item.os}</strong></td>
        <td>${item.abertura}</td>
        <td>${item.prazo}</td>
        <td><span class="fw-bold">${item.loja}</span></td>
        <td>${item.tipoLoja}</td>
        <td>${item.tipo}</td>
        <td>${item.estado}</td>
        <td><span class="badge ${badgeClass} px-2 py-1">${item.status}</span></td>
        <td>${item.previsao || '-'}</td>
        <td class="text-truncate" style="max-width: 180px;" title="${item.observacoes}">${item.observacoes || '-'}</td>
        <td class="text-center">
          <div class="btn-group btn-group-sm">
            <button class="btn btn-outline-success" title="Mover para Finalizados" onclick="handleFinalizar(${item.rowIndex})">
              <i class="bi bi-check-lg"></i>
            </button>
            <button class="btn btn-outline-primary" title="Gerar Rota" onclick="gerarRotaDoChamado('${item.loja}', '${item.os}', '${item.tipo}', '${item.prazo}')">
              <i class="bi bi-geo-alt"></i>
            </button>
          </div>
        </td>
      `;
      tbody.appendChild(tr);
    });
  }

  function filtrarTabela() {
    const busca = document.getElementById('inputBusca').value.toLowerCase();
    const statusFiltro = document.getElementById('filterStatus').value;

    const filtrados = todosChamados.filter(item => {
      const matchBusca = (
        item.os.toLowerCase().includes(busca) ||
        item.loja.toLowerCase().includes(busca) ||
        item.tipo.toLowerCase().includes(busca) ||
        item.estado.toLowerCase().includes(busca) ||
        item.observacoes.toLowerCase().includes(busca)
      );
      const matchStatus = !statusFiltro || item.status === statusFiltro;

      return matchBusca && matchStatus;
    });

    renderizarTabela(filtrados);
  }

  function preencherDatalist(elementId, items) {
    const datalist = document.getElementById(elementId);
    datalist.innerHTML = '';
    (items || []).forEach(item => {
      const option = document.createElement('option');
      option.value = item;
      datalist.appendChild(option);
    });
  }

  function autoPreencherEndereco() {
    const sigla = document.getElementById('inputSiglaRota').value.toUpperCase().trim();
    if (mapaEnderecosGlobal[sigla]) {
      document.getElementById('inputEnderecoRota').value = mapaEnderecosGlobal[sigla];
    }
  }

  function gerarRotaDoChamado(loja, os, tipo, prazo) {
    document.getElementById('inputSiglaRota').value = loja;
    document.getElementById('inputServicoRota').value = `${tipo} - O.S ${os}`;
    autoPreencherEndereco();

    // Troca para a aba de Rotas
    const rotasTab = new bootstrap.Tab(document.getElementById('rotas-tab'));
    rotasTab.show();
  }

  function handleSalvarChamado(e) {
    e.preventDefault();
    const form = e.target;
    const formData = Object.fromEntries(new FormData(form));

    google.script.run.withSuccessHandler(res => {
      alert(res.message);
      form.reset();
      const abertosTab = new bootstrap.Tab(document.getElementById('abertos-tab'));
      abertosTab.show();
      carregarDados();
    }).salvarChamadoAberto(formData);
  }

  function handleSalvarRota(e) {
    e.preventDefault();
    const form = e.target;
    const formData = Object.fromEntries(new FormData(form));

    google.script.run.withSuccessHandler(res => {
      alert(res.message);
      form.reset();
    }).salvarNovaRota(formData);
  }

  function handleFinalizar(rowIndex) {
    if (confirm('Deseja mover este chamado para a aba FINALIZADOS?')) {
      google.script.run.withSuccessHandler(res => {
        alert(res.message);
        carregarDados();
      }).finalizarChamado(rowIndex);
    }
  }
</script>

</body>
</html>
