<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerenciamento de Presença</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script> <!-- Inclusão da biblioteca jsPDF -->
    <style>
        body {
            background: linear-gradient(135deg, #6a11cb, #2575fc);
            color: rgb(2, 2, 2);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 20px;
        }

        .container-admin {
            background: rgba(255, 255, 255, 0.95);
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0px 10px 30px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 1200px;
            color: #333;
        }

        .calendar {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 15px;
            text-align: center;
            margin-top: 20px;
        }

        .calendar-cell {
            background: #fff;
            padding: 15px;
            border-radius: 10px;
            min-height: 180px;
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
        }

        .btn {
            border-radius: 8px;
            transition: all 0.3s ease;
            width: 100%;
            font-size: 14px;
            padding: 10px;
            margin-top: 5px;
        }

        .historico {
            font-size: 12px;
            margin-top: 5px;
            color: #666;
        }
    </style>
</head>
<body>

    <div class="container-admin">
        <h2 class="text-center">Gerenciar Alunos</h2>

        <input type="text" id="nomeAluno" class="form-control" placeholder="Nome do Aluno">
        
        <select id="diaSemana" class="form-select">
            <option value="">Dia da Semana</option>
            <option value="segunda">Segunda</option>
            <option value="terca">Terça</option>
            <option value="quarta">Quarta</option>
            <option value="quinta">Quinta</option>
            <option value="sexta">Sexta</option>
        </select>
        
        <select id="disciplina" class="form-select">
            <option value="">Disciplina</option>
            <option value="voleibol">Voleibol</option>
            <option value="futebol">Futebol</option>
            <option value="street">Street</option>
            <option value="teatro">Teatro</option>
            <option value="ludoteca">Ludoteca</option>
            <option value="eureca">Eureca</option>
            <option value="musica">Música</option>
        </select>

        <select id="periodo" class="form-select">
            <option value="">Período</option>
            <option value="manha">Manhã</option>
            <option value="tarde">Tarde</option>
        </select>

        <button class="btn btn-primary w-100" onclick="adicionarAluno()">Adicionar Aluno</button>

        <div class="calendar">
            <div id="segunda" class="calendar-cell"><strong>Segunda</strong></div>
            <div id="terca" class="calendar-cell"><strong>Terça</strong></div>
            <div id="quarta" class="calendar-cell"><strong>Quarta</strong></div>
            <div id="quinta" class="calendar-cell"><strong>Quinta</strong></div>
            <div id="sexta" class="calendar-cell"><strong>Sexta</strong></div>
        </div>

        <button class="btn btn-secondary w-100 mt-4" onclick="verHistoricoRemocoes()">Ver Histórico de Remoções</button>
        <button class="btn btn-success w-100 mt-2" onclick="gerarPDF()">Gerar PDF</button> <!-- Botão para gerar PDF -->
    </div>

    <!-- Modal de Histórico -->
    <div class="modal fade" id="historicoModal" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Histórico de Presença</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <p id="historicoConteudo">Nenhum registro encontrado.</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal de Histórico de Remoções -->
    <div class="modal fade" id="historicoRemocoesModal" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Histórico de Remoções</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <ul id="historicoRemocoesConteudo">
                        <li>Nenhuma remoção registrada.</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>

    <script>
        let alunos = JSON.parse(localStorage.getItem("alunos")) || [];
        let historicoRemocoes = JSON.parse(localStorage.getItem("historicoRemocoes")) || [];

        function adicionarAluno() {
            const nome = document.getElementById('nomeAluno').value;
            const diaSemana = document.getElementById('diaSemana').value;
            const disciplina = document.getElementById('disciplina').value;
            const periodo = document.getElementById('periodo').value;

            if (!nome || !diaSemana || !disciplina || !periodo) {
                alert("Preencha todos os campos.");
                return;
            }

            alunos.push({ nome, diaSemana, disciplina, periodo, historico: [] });

            localStorage.setItem("alunos", JSON.stringify(alunos));

            // Limpar os campos após adicionar
            document.getElementById('nomeAluno').value = "";
            document.getElementById('diaSemana').value = "";
            document.getElementById('disciplina').value = "";
            document.getElementById('periodo').value = "";

            renderizarCalendario();
        }

        function renderizarCalendario() {
            document.querySelectorAll('.calendar-cell').forEach(cell => cell.innerHTML = `<strong>${cell.id.charAt(0).toUpperCase() + cell.id.slice(1)}</strong>`);

            alunos.forEach(aluno => {
                let div = document.createElement('div');
                div.innerHTML = `
                    <strong>${aluno.nome}</strong> - ${aluno.disciplina} (${aluno.periodo})
                    <button class='btn btn-success btn-sm' onclick='marcarPresenca("${aluno.nome}")'>✔ Presente</button>
                    <button class='btn btn-danger btn-sm' onclick='marcarFalta("${aluno.nome}")'>❌ Falta</button>
                    <button class='btn btn-info btn-sm' onclick='verHistorico("${aluno.nome}")'>📜 Ver Histórico</button>
                    <button class='btn btn-warning btn-sm' onclick='removerAluno("${aluno.nome}")'>🗑 Remover</button>
                `;

                document.getElementById(aluno.diaSemana).appendChild(div);
            });
        }

        function marcarPresenca(nome) {
            let aluno = alunos.find(a => a.nome === nome);
            let data = new Date().toLocaleDateString();
            aluno.historico.push(`✅ Presente - ${data}`);
            localStorage.setItem("alunos", JSON.stringify(alunos));
            renderizarCalendario();
        }

        function marcarFalta(nome) {
            let aluno = alunos.find(a => a.nome === nome);
            let data = new Date().toLocaleDateString();
            aluno.historico.push(`❌ Falta - ${data}`);
            localStorage.setItem("alunos", JSON.stringify(alunos));
            renderizarCalendario();
        }

        function verHistorico(nome) {
            let aluno = alunos.find(a => a.nome === nome);
            let historicoTexto = aluno.historico.length ? aluno.historico.join("<br>") : "Nenhum registro encontrado.";
            
            document.getElementById("historicoConteudo").innerHTML = historicoTexto;
            new bootstrap.Modal(document.getElementById('historicoModal')).show();
        }

        function removerAluno(nome) {
            // Registro da remoção
            const dataRemocao = new Date().toLocaleDateString();
            historicoRemocoes.push(`Aluno: ${nome} removido em ${dataRemocao}`);
            localStorage.setItem("historicoRemocoes", JSON.stringify(historicoRemocoes));

            // Remover o aluno
            alunos = alunos.filter(a => a.nome !== nome);
            localStorage.setItem("alunos", JSON.stringify(alunos));

            renderizarCalendario();
        }

        function verHistoricoRemocoes() {
            const listaRemocoes = document.getElementById('historicoRemocoesConteudo');
            listaRemocoes.innerHTML = '';

            if (historicoRemocoes.length === 0) {
                listaRemocoes.innerHTML = '<li>Nenhuma remoção registrada.</li>';
            } else {
                historicoRemocoes.forEach(remocao => {
                    let item = document.createElement('li');
                    item.textContent = remocao;
                    listaRemocoes.appendChild(item);
                });
            }

            new bootstrap.Modal(document.getElementById('historicoRemocoesModal')).show();
        }

        // Função para gerar PDF
        function gerarPDF() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();

            doc.text("Gerenciamento de Presença", 20, 20);
            doc.text("Relatório de Presença e Remoções", 20, 30);
            doc.text("-----------------------------------------------------", 20, 40);

            // Adicionando alunos
            alunos.forEach((aluno, index) => {
                doc.text(`${index + 1}. ${aluno.nome} - ${aluno.disciplina} (${aluno.periodo})`, 20, 50 + index * 10);
            });

            doc.text("-----------------------------------------------------", 20, 50 + alunos.length * 10);

            // Adicionando histórico de remoções
            doc.text("Histórico de Remoções", 20, 50 + alunos.length * 10 + 10);
            if (historicoRemocoes.length === 0) {
                doc.text("Nenhuma remoção registrada.", 20, 60 + alunos.length * 10);
            } else {
                historicoRemocoes.forEach((remocao, index) => {
                    doc.text(remocao, 20, 70 + alunos.length * 10 + index * 10);
                });
            }

            doc.save("relatorio_presenca_remocoes.pdf");
        }

        renderizarCalendario();
    </script>

</body>
</html>
