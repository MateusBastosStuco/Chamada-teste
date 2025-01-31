<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerenciamento de Presença</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>

    <style>
        body {
            background: linear-gradient(135deg, #f8f9fa, #e9ecef);
            min-height: 100vh;
        }

        .auth-container {
            max-width: 400px;
            margin: 5rem auto;
            padding: 2rem;
            background: white;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        .auth-container.hidden {
            display: none;
        }

        .btn-action {
            width: 100%;
            margin-top: 1rem;
            padding: 0.5rem;
            transition: transform 0.2s;
        }

        .btn-action:hover {
            transform: translateY(-2px);
        }

        .toggle-link {
            cursor: pointer;
            color: #0d6efd;
            text-align: center;
            margin-top: 1rem;
        }

        .container-admin {
            max-width: 1200px;
            margin: 2rem auto;
            padding: 2rem;
            background: white;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        .calendar {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 1rem;
            margin-top: 2rem;
        }

        .calendar-header {
            background: #0d6efd;
            color: white;
            padding: 1rem;
            text-align: center;
            border-radius: 8px 8px 0 0;
        }

        .calendar-cell {
            background: #f8f9fa;
            border-radius: 8px;
            min-height: 200px;
            border: 1px solid #dee2e6;
        }

        .periodo {
            padding: 0.5rem;
            background: #e9ecef;
            font-weight: bold;
            text-align: center;
        }

        .alunos-periodo {
            padding: 0.5rem;
            min-height: 80px;
        }

        .aluno-item {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0.5rem;
            margin: 0.25rem 0;
            background: white;
            border-radius: 5px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
        }

        .table th {
            background: #0d6efd;
            color: white;
        }

        .modal-content {
            border-radius: 15px;
        }

        .hidden {
            display: none;
        }
    </style>
</head>

<body>
    <!-- Tela de Login -->
    <div id="auth-container-login" class="auth-container">
        <h2 class="text-center mb-4">Login</h2>
        <div id="login-form">
            <input type="text" id="login-nome" class="form-control mb-3" placeholder="Nome de usuário">
            <input type="password" id="login-senha" class="form-control mb-3" placeholder="Senha">
            <button class="btn btn-success btn-action" onclick="login()">Entrar</button>
            <p class="toggle-link" onclick="toggleAuth('signup')">Não tem uma conta? Cadastre-se</p>
        </div>
    </div>

    <!-- Tela de Cadastro de Usuário -->
    <div id="auth-container-signup" class="auth-container hidden">
        <h2 class="text-center mb-4">Cadastro</h2>
        <div id="signup-form">
            <input type="text" id="signup-nome" class="form-control mb-3" placeholder="Nome de usuário">
            <input type="password" id="signup-senha" class="form-control mb-3" placeholder="Senha">
            <input type="password" id="signup-confirmar-senha" class="form-control mb-3" placeholder="Confirmar senha">
            <button class="btn btn-info btn-action" onclick="signup()">Cadastrar</button>
            <p class="toggle-link" onclick="toggleAuth('login')">Já tem uma conta? Faça login</p>
        </div>
    </div>

    <!-- Tela do Administrador -->
    <div class="container-admin hidden" id="admin">
        <h2 class="text-center mb-4">Gerenciamento Escolar</h2>

        <!-- Formulário de Cadastro de Alunos -->
        <form id="formAluno" class="mb-4 bg-light p-4 rounded-3">
            <h4 class="mb-3">Cadastrar Novo Aluno</h4>
            <div class="row g-3">
                <div class="col-md-6">
                    <input type="text" id="nomeCompleto" class="form-control" placeholder="Nome Completo" required>
                </div>
                <div class="col-md-6">
                    <input type="text" id="endereco" class="form-control" placeholder="Endereço" required>
                </div>
                <div class="col-md-6">
                    <input type="number" id="idade" class="form-control" placeholder="Idade" required>
                </div>
                <div class="col-md-6">
                    <input type="text" id="nomeMae" class="form-control" placeholder="Nome da Mãe" required>
                </div>
                <div class="col-md-6">
                    <input type="text" id="nomePai" class="form-control" placeholder="Nome do Pai" required>
                </div>
                <div class="col-md-6">
                    <input type="text" id="bairro" class="form-control" placeholder="Bairro" required>
                </div>
                <div class="col-md-6">
                    <button type="submit" class="btn btn-primary w-100">Cadastrar Aluno</button>
                </div>
            </div>
        </form>

        <!-- Tabela de Alunos Cadastrados -->
        <h3 class="text-center mt-5 mb-3">Alunos Cadastrados</h3>
        <div class="table-responsive">
            <table class="table table-hover table-striped">
                <thead>
                    <tr>
                        <th>Nome Completo</th>
                        <th>Endereço</th>
                        <th>Idade</th>
                        <th>Nome da Mãe</th>
                        <th>Nome do Pai</th>
                        <th>Bairro</th>
                        <th>Ações</th>
                    </tr>
                </thead>
                <tbody id="listaAlunos">
                    <!-- Dados dos alunos serão inseridos aqui via JavaScript -->
                </tbody>
            </table>
        </div>

        <!-- Calendário de Presenças -->
        <h3 class="text-center mt-5 mb-3">Controle de Presenças</h3>
        <div class="calendar">
            <div class="calendar-header">Segunda</div>
            <div class="calendar-header">Terça</div>
            <div class="calendar-header">Quarta</div>
            <div class="calendar-header">Quinta</div>
            <div class="calendar-header">Sexta</div>

            <!-- Células do calendário (mantidas como no original) -->
        </div>

        <button class="btn btn-danger w-100 mt-5" onclick="logout()">Sair do Sistema</button>
    </div>

    <!-- Modais (mantidos como no original) -->

    <script>
        // Dados de autenticação
        let users = JSON.parse(localStorage.getItem('users')) || {
            'admin': { password: 'admin123', isAdmin: true }
        };

        // Dados dos alunos
        let alunos = JSON.parse(localStorage.getItem('alunos')) || {};

        // Usuário logado
        let currentUser = null;

        function salvarDados() {
            localStorage.setItem('alunos', JSON.stringify(alunos));
            localStorage.setItem('users', JSON.stringify(users));
        }

        function carregarAlunos() {
            const listaAlunos = document.getElementById('listaAlunos');
            listaAlunos.innerHTML = '';

            for (const nomeAluno in alunos) {
                const aluno = alunos[nomeAluno];
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${aluno.nome_completo}</td>
                    <td>${aluno.endereco}</td>
                    <td>${aluno.idade}</td>
                    <td>${aluno.nome_mae}</td>
                    <td>${aluno.nome_pai}</td>
                    <td>${aluno.bairro}</td>
                    <td>
                        <button class="btn btn-sm btn-info" onclick="mostrarInfoAluno('${aluno.nome_completo}')">ℹ️ Info</button>
                    </td>
                `;
                listaAlunos.appendChild(row);
            }
        }

        // Função para alternar entre login e cadastro
        function toggleAuth(formType) {
            if (formType === 'login') {
                document.getElementById('auth-container-login').classList.remove('hidden');
                document.getElementById('auth-container-signup').classList.add('hidden');
            } else {
                document.getElementById('auth-container-signup').classList.remove('hidden');
                document.getElementById('auth-container-login').classList.add('hidden');
            }
        }

        // Função de login
        function login() {
            const username = document.getElementById('login-nome').value.trim();
            const password = document.getElementById('login-senha').value.trim();

            if (!username || !password) {
                alert("Preencha todos os campos.");
                return;
            }

            const user = users[username];
            
            if (user && user.password === password) {
                if (user.isAdmin) {
                    currentUser = user;
                    document.getElementById('auth-container-login').classList.add('hidden');
                    document.getElementById('auth-container-signup').classList.add('hidden');
                    document.getElementById('admin').classList.remove('hidden');
                    carregarAlunos();
                } else {
                    alert("Acesso permitido apenas para administradores!");
                }
            } else {
                alert("Credenciais inválidas!");
            }
        }

        // Função de cadastro de usuário
        function signup() {
            const username = document.getElementById('signup-nome').value.trim();
            const password = document.getElementById('signup-senha').value.trim();
            const confirmPassword = document.getElementById('signup-confirmar-senha').value.trim();

            if (!username || !password || !confirmPassword) {
                alert("Preencha todos os campos.");
                return;
            }

            if (password !== confirmPassword) {
                alert("As senhas não coincidem.");
                return;
            }

            if (users[username]) {
                alert("Usuário já existe.");
                return;
            }

            users[username] = { password: password, isAdmin: false };
            salvarDados();
            alert("Cadastro realizado com sucesso!");
            toggleAuth('login');
        }

        // Função de logout
        function logout() {
            currentUser = null;
            document.getElementById('admin').classList.add('hidden');
            document.getElementById('auth-container-login').classList.remove('hidden');
            document.getElementById('login-form').reset();
        }

        // Restante do código mantido com verificações de admin
        document.getElementById('formAluno').addEventListener('submit', (e) => {
            e.preventDefault();

            if (!currentUser || !currentUser.isAdmin) {
                alert("Acesso não autorizado!");
                return;
            }

            const aluno = {
                nome_completo: document.getElementById('nomeCompleto').value,
                endereco: document.getElementById('endereco').value,
                idade: document.getElementById('idade').value,
                nome_mae: document.getElementById('nomeMae').value,
                nome_pai: document.getElementById('nomePai').value,
                nome_rua: document.getElementById('nomeRua').value,
                bairro: document.getElementById('bairro').value,
                presencas: 0,
                faltas: 0
            };

            alunos[aluno.nome_completo] = aluno;
            salvarDados();
            carregarAlunos();
            document.getElementById('formAluno').reset();
            alert('Aluno cadastrado com sucesso!');
        });

        // Verificação de autenticação ao carregar a página
        window.onload = function() {
            if (currentUser && currentUser.isAdmin) {
                document.getElementById('auth-container-login').classList.add('hidden');
                document.getElementById('auth-container-signup').classList.add('hidden');
                document.getElementById('admin').classList.remove('hidden');
            } else {
                logout();
            }
        };

        // Restante das funções mantidas com verificações de admin
    </script>
</body>

</html>
