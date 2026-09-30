<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>GFLINKS - Gestão de Chamados e Rotas</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css" rel="stylesheet">
  <style>
    body { background-color: #f4f6f9; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
    .badge-pendente { background-color: #ffc107; color: #000; }
    .badge-agendado { background-color: #0dcaf0; color: #000; }
    .table-sm th, .table-sm td { font-size: 0.88rem; vertical-align: middle; }
    .form-section { background: #fff; padding: 25px; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.06); }
  </style>
</head>
<body>

<nav class="navbar navbar-expand-lg navbar-dark bg-primary shadow-sm mb-4">
  <div class="container-fluid px-4">
    <a class="navbar-brand fw-bold" href="#"><i class="bi bi-headset me-2"></i>GFLINKS - Chamados e Rotas</a>
    <button class="btn btn-outline-light btn-sm" onclick="carregarDados()"><i class="bi bi-arrow-repeat me-1"></i>Atualizar</button>
  </div>
</nav>

<div class="container-fluid px-4">
  <ul class="nav nav-tabs mb-4" id="mainTab">
    <li class="nav-item">
      <button class="nav-link active" id="abertos-tab" data-bs-toggle="tab" data-bs-target="#abertos">Chamados Abertos</button>
    </li>
    <li class="nav-item">
      <button class="nav-link" id="novo-chamado-tab" data-bs-toggle="tab" data-bs-target="#novo-chamado">+ Inserir Chamado</button>
    </li>
    <li class="nav-item">
      <button class="nav-link" id="rotas-tab" data-bs-toggle="tab" data-bs-target="#rotas">+ Inserir Rota</button>
    </li>
  </ul>

  <div class="tab-content">
    
    <!-- ABA 1: TABELA -->
    <div class="tab-pane fade show active" id="abertos">
      <div class="card shadow-sm mb-4">
        <div class="card-header bg-white py-3 d-flex justify-content-between align-items-center">
          <input type="text" id="inputBusca" class="form-control w-50" placeholder="Buscar por O.S, Loja, Serviço..." onkeyup="filtrarTabela()">
          <span id="totalRegistros" class="badge bg-secondary p-2">0 Registros</span>
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

    <!-- ABA 2: NOVO CHAMADO -->
    <div class="tab-pane fade" id="novo-chamado">
      <div class="form-section max-w-800 mx-auto">
        <h5 class="mb-3 text-primary"><i class="bi bi-file-earmark-plus me-2"></i>Cadastrar Novo Chamado</h5>
        <form id="formChamado" onsubmit="handleSalvarChamado(event)">
          <div class="row g-3">
            <div class="col-md-4">
              <label class="form-label fw-bold">Número O.S *</label>
              <input type="text" class="form-control" name="os" required>
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
              <input type="text" class="form-control text-uppercase" name="loja" id="inputLojaForm" list="datalistLojas">
              <datalist id="datalistLojas"></datalist>
            </div>
            <div class="col-md-6">
              <label class="form-label">Tipo de Loja</label>
              <input type="text" class="form-control" name="tipoLoja" list="datalistTiposLoja">
              <datalist id="datalistTiposLoja"></datalist>
            </div>
            <div class="col-md-6">
              <label class="form-label">Tipo de Serviço</label>
              <input type="text" class="form-control" name="tipo" list="datalistTiposServico">
              <datalist id="datalistTiposServico"></datalist>
            </div>
            <div class="col-md-6">
              <label class="form-label">Estado (UF)</label>
              <input type="text" class="form-control text-uppercase" name="estado" maxlength="2">
            </div>
            <div class="col-md-6">
              <label class="form-label">Status</label>
              <select class="form-select" name="status">
                <option value="PENDENTE">PENDENTE</option>
                <option value="AGENDADO">AGENDADO</option>
              </select>
            </div>
            <div class="col-md-6">
              <label class="form-label">Previsão</label>
              <input type="date" class="form-control" name="previsao">
            </div>
            <div class="col-12">
              <label class="form-label">Observações</label>
              <textarea class="form-control" name="observacoes" rows="2"></textarea>
            </div>
          </div>
          <button type="submit" class="btn btn-primary mt-4 w-100 fw-bold">Salvar Chamado</button>
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
              <input type="text" class="form-control text-uppercase" name="sigla" id="inputSiglaRota" list="datalistLojas" onchange="autoPreencherEndereco()" required>
            </div>
            <div class="col-12">
              <label class="form-label">Endereço Completo</label>
              <input type="text" class="form-control" name="endereco" id="inputEnderecoRota">
            </div>
            <div class="col-12">
              <label class="form-label">Serviço / O.S</label>
              <input type="text" class="form-control" name="servicoOs" id="inputServicoRota">
            </div>
            <div class="col-md-6">
              <label class="form-label">Prazo</label>
              <input type="date" class="form-control" name="prazo">
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
          <button type="submit" class="btn btn-success mt-4 w-100 fw-bold">Adicionar à Rota</button>
        </form>
      </div>
    </div>

  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script>
  const API_URL = "https://script.google.com/macros/s/AKfycbxQE_rHKKisLQwBrxCw8Nt2ucxBd118xIyxwzPycKx33E8Mrkz96Mw6SmK9-QToJ2LI/exec";
  let todosChamados = [];
  let mapaEnderecosGlobal = {};

  document.addEventListener('DOMContentLoaded', () => {
    const hoje = new Date().toISOString().split('T')[0];
    document.getElementById('campoAbertura').value = hoje;
    document.getElementById('campoDataRota').value = hoje;
    carregarDados();
  });

  async function carregarDados() {
    try {
      const resp = await fetch(`${API_URL}?action=getDados`);
      const data = await resp.json();
      
      todosChamados = data.chamadosAbertos || [];
      mapaEnderecosGlobal = data.opcoes.mapaEnderecos || {};

      renderizarTabela(todosChamados);
      preencherDatalist('datalistLojas', data.opcoes.lojas);
      preencherDatalist('datalistTiposLoja', data.opcoes.tiposLoja);
      preencherDatalist('datalistTiposServico', data.opcoes.tiposServico);
    } catch (err) {
      alert("Erro ao carregar dados: " + err.message);
    }
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
      const badgeClass = item.status === 'AGENDADO' ? 'badge-agendado' : 'badge-pendente';

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
          <button class="btn btn-sm btn-outline-success" onclick="handleFinalizar(${item.rowIndex})"><i class="bi bi-check-lg"></i></button>
        </td>
      `;
      tbody.appendChild(tr);
    });
  }

  function filtrarTabela() {
    const busca = document.getElementById('inputBusca').value.toLowerCase();
    const filtrados = todosChamados.filter(item => 
      item.os.toLowerCase().includes(busca) ||
      item.loja.toLowerCase().includes(busca) ||
      item.tipo.toLowerCase().includes(busca)
    );
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

  async function handleSalvarChamado(e) {
    e.preventDefault();
    const formData = Object.fromEntries(new FormData(e.target));
    
    await enviarDados('salvarChamado', formData);
    e.target.reset();
    carregarDados();
  }

  async function handleSalvarRota(e) {
    e.preventDefault();
    const formData = Object.fromEntries(new FormData(e.target));
    
    await enviarDados('salvarRota', formData);
    e.target.reset();
  }

  async function handleFinalizar(rowIndex) {
    if (confirm('Deseja mover este chamado para FINALIZADOS?')) {
      await enviarDados('finalizarChamado', { rowIndex: rowIndex });
      carregarDados();
    }
  }

  async function enviarDados(action, payload) {
    try {
      const resp = await fetch(API_URL, {
        method: 'POST',
        body: JSON.stringify({ action: action, payload: payload })
      });
      const res = await resp.json();
      alert(res.message);
    } catch (err) {
      alert("Erro ao enviar dados: " + err.message);
    }
  }
</script>

</body>
</html>
