# Banco API — Testes de Performance

Repositório de testes de performance da **Banco API**, desenvolvido com JavaScript e [Grafana k6](https://grafana.com/docs/k6/latest/).

## Introdução

Este projeto reúne testes automatizados para avaliar o comportamento e o desempenho dos endpoints de autenticação e transferência da Banco API.

Os scripts simulam requisições HTTP, validam as respostas da aplicação e coletam métricas como tempo de resposta, taxa de falhas e quantidade de requisições processadas.

> [IMPORTANTE]
> Informe a URL da API por meio da variável de ambiente `BASE_URL` ao executar os testes. Exemplo: `BASE_URL=http://localhost:3000`.

## Tecnologias Utilizadas

- [JavaScript](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript): linguagem usada na implementação dos cenários, funções auxiliares e validações.
- [Grafana k6](https://grafana.com/docs/k6/latest/): ferramenta de testes de carga e performance responsável pela execução dos scripts, geração das métricas e relatórios.
- [k6 Web Dashboard](https://grafana.com/docs/k6/latest/results-output/web-dashboard/): dashboard nativo para acompanhar os resultados em tempo real e exportar relatórios em HTML.

## Estrutura do Repositório

```text
banco-api-performance/
├── config/
│   └── config.local.json
├── fixtures/
│   └── postLogin.json
├── helpers/
│   └── autenticacao.js
├── tests/
│   ├── login.test.js
│   └── transferencias.test.js
├── utils/
│   └── variaveis.js
├── .gitignore
└── README.md
```

## Objetivo de cada grupo de arquivos

| Grupo | Objetivo |
| --- | --- |
| `config/` | Armazena configurações locais. O arquivo `config.local.json` define `http://localhost:3000` como URL padrão quando `BASE_URL` não é informada. |
| `fixtures/` | Contém os dados reutilizados nas requisições. `postLogin.json` fornece as credenciais usadas no login. |
| `helpers/` | Reúne funções auxiliares compartilhadas pelos testes. `autenticacao.js` realiza o login e devolve o token usado nas requisições autenticadas. |
| `tests/` | Contém os cenários executáveis pelo k6. `login.test.js` testa autenticação sob carga; `transferencias.test.js` testa a criação de uma transferência autenticada. |
| `utils/` | Centraliza utilitários do projeto. `variaveis.js` obtém a URL da API a partir de `BASE_URL` e usa a configuração local como fallback. |
| `.gitignore` | Evita o versionamento do relatório HTML gerado localmente (`html-report.html`). |

## Modo de Instalação

### Pré-requisitos

- [Git](https://git-scm.com/downloads);
- Banco API em execução e acessível pela máquina que executará os testes;
- [k6 instalado](https://grafana.com/docs/k6/latest/set-up/install-k6/).

### 1. Clone o repositório

```bash
git clone https://github.com/thiagofolly/banco-api-performance.git
cd banco-api-performance
```

### 2. Instale o k6

macOS, usando Homebrew:

```bash
brew install k6
```

Windows, usando Winget:

```powershell
winget install k6 --source winget
```

Windows, usando Chocolatey:

```powershell
choco install k6
```

Para Linux e outras opções de instalação, consulte a [documentação oficial do k6](https://grafana.com/docs/k6/latest/set-up/install-k6/).

Confirme a instalação:

```bash
k6 version
```

O projeto não possui dependências npm: os módulos `k6` e `k6/http` são fornecidos pela própria instalação do k6.

## Modo de execução do projeto

Execute os comandos a partir da raiz do repositório. Substitua `http://localhost:3000` pela URL do ambiente que será testado.

### Teste de login

```bash
k6 run tests/login.test.js -e BASE_URL=http://localhost:3000
```

### Teste de transferências

```bash
k6 run tests/transferencias.test.js -e BASE_URL=http://localhost:3000
```

### Acompanhamento do relatório em tempo real

Ative o dashboard web nativo do k6 por meio de `K6_WEB_DASHBOARD`:

```bash
K6_WEB_DASHBOARD=true k6 run tests/login.test.js -e BASE_URL=http://localhost:3000 
```

Durante a execução, abra [http://localhost:5665](http://localhost:5665) no navegador para acompanhar as métricas em tempo real.

### Acompanhamento em tempo real e exportação do relatório HTML

Use `K6_WEB_DASHBOARD_EXPORT` para salvar automaticamente o relatório após a conclusão do teste:

```bash
K6_WEB_DASHBOARD=true K6_WEB_DASHBOARD_EXPORT=html-report.html k6 run tests/login.test.js -e BASE_URL=http://localhost:3000 
```

O arquivo `html-report.html` será criado na pasta em que o comando foi executado. Ele já está incluído no `.gitignore` do projeto.

Para gerar o relatório do teste de transferências, troque o caminho por `tests/transferencias.test.js`.

> [!NOTE]
> O relatório exportado inclui gráficos quando a duração do teste é maior que três vezes o período de agregação do dashboard. O período padrão é de 10 segundos e pode ser alterado com `K6_WEB_DASHBOARD_PERIOD`.
