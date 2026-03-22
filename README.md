# Bug-Bounty-Methodology
Minha metodologia, comandos de terminal e anotações práticas para Bug Bounty




# 🐛 Minha Metodologia de Bug Bounty (Testes Manuais)

Bem-vindo ao meu repositório de Bug Bounty! [cite_start]Este espaço documenta o meu progresso e o fluxo estruturado de reconhecimento e exploração manual[cite: 4]. Meu foco principal é a utilização do terminal Linux e ferramentas de linha de comando para descobrir falhas de segurança em aplicações web.

> [cite_start]⚠️ **Aviso:** Todo o conhecimento e ferramentas listadas aqui são utilizados estritamente em plataformas autorizadas de Bug Bounty (como Bugcrowd) ou em laboratórios de teste como o PortSwigger[cite: 45].

---

## 🗺️ Fluxo de Trabalho (Pipeline Profissional)
[cite_start]Para transformar o reconhecimento (Recon) em um processo organizado, sigo este fluxo estruturado[cite: 42]: 
[cite_start]`subfinder → httpx → gau → grep → nuclei`[cite: 42].

---

## 🛠️ Passo a Passo do Reconhecimento

### 1. Descoberta de Hosts Ativos (HTTPX)
[cite_start]Uso a ferramenta `httpx` para verificar quais hosts de uma lista estão online e respondendo[cite: 6]. 
* [cite_start]O comando básico lê um arquivo de domínios e salva os ativos silenciosamente[cite: 7]:
  [cite_start]`cat final.txt | httpx -silent > alive.txt` [cite: 7, 8]
* [cite_start]**Controle de Performance:** Se o terminal travar, é porque há muitos hosts[cite: 9]. [cite_start]Para evitar isso e não sobrecarregar o alvo, controlo as threads (requisições simultâneas) e o rate-limit (requisições por segundo)[cite: 11, 12]:
  [cite_start]`httpx -threads 20 -rate-limit 50` [cite: 11]
* [cite_start]**Filtrando Status Codes:** O foco principal são os alvos interessantes com status `200 = OK`[cite: 14]. Filtro diretamente usando:
  [cite_start]`httpx -mc 200` [cite: 16, 17]

[cite_start]*(Nota: Sites que retornam `403 = Forbidden` em tudo geralmente possuem um WAF forte, indicando que não são alvos fáceis[cite: 14, 38, 44]. Também fico atento ao `401` para autenticação exigida e `404` para páginas não encontradas [cite: 14]).*

### 2. Coleta de URLs Históricas (GAU)
[cite_start]Com os domínios ativos em mãos, utilizo o `gau` (Get All URLs) para coletar URLs antigas e atuais de várias fontes, como a Wayback Machine[cite: 18, 20].
* [cite_start]`gau dominio.com > urls.txt` [cite: 19]

### 3. Filtragem e Extração de Parâmetros
Essa é a fase de preparação para o teste manual. [cite_start]Preciso encontrar os parâmetros escondidos na aplicação[cite: 21].
1. **Extrair URLs com parâmetros (`=`):**
   [cite_start]`cat urls.txt | grep "=" > params.txt` [cite: 2, 22]
2. **Remover duplicatas:**
   [cite_start]`sort -u params.txt > clean_params.txt` [cite: 2, 23, 40]
3. [cite_start]**Isolar os parâmetros mais críticos (Juicy Targets):** Busco especificamente por variáveis que interagem com banco de dados ou redirecionamentos [cite: 26][cite_start]: `id=, user=, token=, redirect=, url=`[cite: 28].
   [cite_start]`grep -E "id=|user=|token=|redirect=|url=" clean_params.txt > juicy.txt` [cite: 29]

---

## 🎯 Testes Manuais de Vulnerabilidade

[cite_start]Ao invés de depender totalmente de automação para a exploração, utilizo minha lista refinada para testar vulnerabilidades na mão[cite: 30]:

* [cite_start]**🔓 IDOR (Insecure Direct Object Reference):** Teste de alteração de identificadores de usuários ou objetos[cite: 31]. [cite_start]Exemplo: mudar de `?id=123` para `?id=124`[cite: 32].
* [cite_start]**💉 XSS (Cross-Site Scripting):** Teste de injeção de código malicioso nos parâmetros[cite: 33]. [cite_start]Exemplo: injetar `?q=<script>alert(1)</script>`[cite: 34].
* [cite_start]**🔀 Open Redirect:** Teste de manipulação de rotas de redirecionamento para URLs externas[cite: 35]. [cite_start]Exemplo: `?redirect=https://google.com`[cite: 36].

---

## 🐧 Comandos Essenciais (Linux)
[cite_start]Como meu fluxo é via CLI, dependo de comandos fundamentais para análise e troubleshooting[cite: 39]:
* [cite_start]`ps`: Para ver processos em execução no sistema[cite: 3, 40].
* [cite_start]`less`: Para visualizar e navegar com segurança em arquivos grandes, como as listas de URLs (`less clean_params.txt`)[cite: 3, 24, 25, 40].
* [cite_start]`grep`: Para filtrar os dados essenciais[cite: 3, 40].
* [cite_start]`sort -u`: Para remover duplicatas e limpar as wordlists[cite: 3, 40].

[cite_start]*(Troubleshooting: Se alguma ferramenta não for encontrada ao rodar os comandos, é necessário ajustar o PATH do Linux [cite: 38])*
