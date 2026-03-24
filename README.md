# Bug-Bounty-Methodology
Minha metodologia, comandos de terminal e anotações práticas para Bug Bounty

# 🐛 Minha Metodologia de Bug Bounty (Testes Manuais)

Bem-vindo ao meu repositório de Bug Bounty! Este espaço documenta o meu progresso e o fluxo estruturado de reconhecimento e exploração manual. Meu foco principal é a utilização do terminal Linux e ferramentas de linha de comando para descobrir falhas de segurança em aplicações web.

> ⚠️ **Aviso:** Todo o conhecimento e ferramentas listadas aqui são utilizados estritamente em plataformas autorizadas de Bug Bounty (como Bugcrowd) ou em laboratórios de teste como o PortSwigger.

---

## 🗺️ Fluxo de Trabalho (Pipeline Profissional)
Para transformar o reconhecimento (Recon) em um processo organizado, sigo este fluxo estruturado: 
`subfinder → httpx → gau → grep → nuclei`.

---

## 🛠️ Passo a Passo do Reconhecimento

### 1. Descoberta de Hosts Ativos (HTTPX)
Uso a ferramenta `httpx` para verificar quais hosts de uma lista estão online e respondendo. 

* Comando básico para ler um arquivo de domínios e salvar os ativos silenciosamente: `cat final.txt | httpx -silent > alive.txt`
* Controle de Performance (evitar travamentos e sobrecarga no alvo limitando threads e rate-limit): `httpx -threads 20 -rate-limit 50`
* Filtragem de Status Codes (foco direto nos alvos interessantes com status 200 = OK): `httpx -mc 200`

*(Nota: Sites que retornam `403 = Forbidden` em tudo geralmente possuem um WAF forte, indicando que não são alvos fáceis. Também fico atento ao `401` para autenticação exigida e `404` para páginas não encontradas).*

### 2. Coleta de URLs Históricas (GAU)
Com os domínios ativos em mãos, utilizo o `gau` (Get All URLs) para coletar URLs antigas e atuais de várias fontes, como a Wayback Machine.

* Comando para extração: `gau dominio.com > urls.txt`

### 3. Filtragem e Extração de Parâmetros
Essa é a fase de preparação para o teste manual. Preciso encontrar os parâmetros escondidos na aplicação.

* Comando para extrair apenas URLs com parâmetros (=): `cat urls.txt | grep "=" > params.txt`
* Comando para remover linhas duplicadas: `sort -u params.txt > clean_params.txt`
* Comando para isolar os parâmetros mais críticos (Juicy Targets como id=, user=, token=, redirect=, url=): `grep -E "id=|user=|token=|redirect=|url=" clean_params.txt > juicy.txt`

---

## 🎯 Testes Manuais de Vulnerabilidade

Ao invés de depender totalmente de automação para a exploração, utilizo minha lista refinada para testar vulnerabilidades na mão:

* **🔓 IDOR (Insecure Direct Object Reference):** Teste de alteração de identificadores de usuários ou objetos. Exemplo: mudar de `?id=123` para `?id=124`.
* **💉 XSS (Cross-Site Scripting):** Teste de injeção de código malicioso nos parâmetros. Exemplo: injetar `?q=<script>alert(1)</script>`.
* **🔀 Open Redirect:** Teste de manipulação de rotas de redirecionamento para URLs externas. Exemplo: `?redirect=https://google.com`.

---

## 🐧 Comandos Essenciais (Linux)
Como meu fluxo é via CLI, dependo de comandos fundamentais para análise e troubleshooting:

* `ps`: Para ver processos em execução no sistema.
* `less`: Para visualizar e navegar com segurança em arquivos grandes, como as listas de URLs.
* `grep`: Para filtrar os dados essenciais.
* `sort -u`: Para remover duplicatas e limpar as wordlists.

*(Troubleshooting: Se alguma ferramenta não for encontrada ao rodar os comandos, é necessário ajustar o PATH do Linux)* 
