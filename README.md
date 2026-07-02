# 🏗️ Arquiteto de Soluções Sênior em IA: Orquestração e Resiliência no Varejo

## 🎯 1. Contexto e Objetivos
O assunto de interesse escolhido para este caderno temático no Google NotebookLM foi a **Hiperautomação e Engenharia de Resiliência no Varejo**. 

Como estudante de Inteligência Artificial e Tecnologia da Informação, e vivenciando a realidade comercial na análise de dados em grandes redes de drogarias, vejo diariamente os gargalos causados por sistemas legados. O objetivo de estudo com este material foi dominar a orquestração de integrações (iPaaS como n8n), o processamento massivo de dados em memória (Python/Pandas) e o tratamento de erros (Error Handling) utilizando IA Generativa para desenhar arquiteturas de software seguras (LGPD) e à prova de falhas.

---

## 📚 2. Curadoria de Fontes
Para evitar alucinações e garantir que a IA tomasse decisões baseadas em documentações técnicas reais e métodos consagrados de mercado, o NotebookLM foi alimentado com as seguintes fontes abertas:

1. **[Documentação Oficial do n8n (Core Nodes & Webhooks)](https://docs.n8n.io/)**: Base técnica para entender fluxos orientados a eventos e roteamento de dados.
2. **[Documentação Oficial do Pandas (Python Data Analysis)](https://pandas.pydata.org/docs/)**: Referência para a limpeza de DataFrames massivos e tratamento de exceções como o `EmptyDataError`.
3. **[Curva ABC e Gestão de Estoque (Wikipedia/Open Access)](https://pt.wikipedia.org/wiki/Curva_ABC)**: Base de negócios para priorização de rupturas de estoque no varejo.
4. **[A Startup Enxuta - Eric Ries (Resumo Executivo)](https://en.wikipedia.org/wiki/Lean_startup)**: Metodologia base para desenhar cenários *As-Is* e *To-Be* criando MVPs rápidos.

---

## 🛠️ 3. Engenharia de Prompts e "Cicatrizes" (Troubleshooting)

Ao longo do projeto, testei diversas abordagens para forçar a IA a agir como um Engenheiro Sênior. Abaixo estão documentadas as perguntas estratégicas, as falhas das primeiras respostas e o raciocínio por trás da correção.

### 🔴 Tentativa 1: O Caminho Feliz (Happy Path)
* **Prompt Testado:** *"Um ERP exporta um CSV de 120 mil linhas. Preciso limpar os CPFs dos clientes e mandar um aviso para cada gerente de loja. Como fazer?"*
* **Resposta Obtida:** A IA sugeriu usar Python para ler o arquivo, mas no n8n sugeriu um `Switch Node` para separar a mensagem de cada filial.
* **A Cicatriz (Dificuldade):** Em uma rede com muitas lojas, um `Switch Node` cria um labirinto inescalável.
* **Troubleshooting (A Correção):** Criei um "Documento Semente" no NotebookLM com **Leis Imutáveis**. Adicionei a *Lei do Roteamento Dinâmico*, proibindo o Switch Node e forçando a IA a gerar um único nó alimentado por variáveis JSON dinâmicas (`{{ $json.chat_id }}`).

### 🔴 Tentativa 2: A Trava de Comunicação
* **Prompt Testado:** *"Explique como o Webhook e a API funcionam para a equipe de vendas."*
* **Resposta Obtida:** A IA utilizou termos corporativos jargões como *"As-Is"*, *"To-Be"* e *"JSON payloads"*.
* **A Cicatriz (Dificuldade):** A explicação era inacessível para usuários leigos do chão de loja.
* **Troubleshooting (A Correção):** Adicionei o gatilho de Persona dupla: *"Me explique como se eu tivesse 10 anos"*. Com essa variação de prompt, a IA substituiu Webhooks por "Campainhas" e Bancos de Dados por "Livros Secretos".

### 🔴 Tentativa 3: O Teste de Estresse (Falha Silenciosa)
* **Prompt Testado:** *"O que acontece com o nosso código Python se hoje o ERP der um erro e exportar o CSV totalmente em branco (0 bytes)?"*
* **Resposta Obtida:** A IA previu que o Pandas daria o crash `EmptyDataError` e a automação morreria em silêncio.
* **Troubleshooting (A Correção):** Instruí a IA a reescrever o código com um bloco `try...except`, criando uma *Dead Letter Queue*. Agora, se o arquivo vier vazio, ela desvia a rota e manda um alerta crítico para a equipe de TI.

*(Nota: O histórico completo das interações e códigos gerados estão documentados na pasta `docs/` deste repositório).*

---

## 📖 4. Miniguia de Estudo (Entrega Final)

### 📝 Resumos Estruturados do Assunto
* **Orquestração Inteligente:** Sistemas iPaaS (n8n, Make) brilham na conexão de APIs, mas não devem ser usados para processar milhares de linhas em *loops* visuais. O processamento pesado deve ser delegado a scripts em Python (Pandas) rodando internamente no fluxo.
* **Prevenção de Falhas Silenciosas (*Sanity Check*):** Todo fluxo que ingere dados externos deve checar a estrutura do arquivo antes de processá-lo. Se houver falha de estrutura (*Schema Drift*), o fluxo deve abortar e avisar a TI via Fila de Mensagens Mortas (DLQ).
* **Conformidade LGPD (Fail-Fast):** A remoção de dados sensíveis (CPFs, nomes) deve ser a primeira ação do script após a validação do arquivo, garantindo que a informação não trafegue nas próximas etapas do fluxo.

### 📚 Glossário
* **Webhook:** Uma "campainha" virtual. Em vez de perguntar ao sistema a todo minuto se há novidades (*Polling*), o webhook espera o sistema avisar passivamente que algo aconteceu.
* **Roteamento Dinâmico:** Técnica onde um único nó de envio de mensagem utiliza variáveis embutidas no código (ex: `chat_id`) para enviar dados para múltiplos destinos, evitando ramificações visuais estáticas.
* **Dead Letter Queue (DLQ):** Um canal de "fuga" na automação. Quando um erro é detectado, os dados defeituosos são enviados para esta fila (ex: um grupo de suporte no Telegram) para análise, evitando que o erro passe despercebido.
* **Schema Validation:** Verificação automática que checa se os nomes das colunas de um arquivo (o "contrato de dados") continuam os mesmos esperados pela automação.

### 🤖 Prompts Reutilizáveis
Estes prompts podem ser copiados e adaptados para gerar arquiteturas de automação em qualquer nicho de negócios:

**1. Para Mapeamento de Cenários e Arquitetura:**
> "Desafio de Arquitetura Sênior:
> Cenário As-Is: [Descreva como o processo manual é feito hoje]
> Gargalo e Volume: [Descreva onde dói e a quantidade de dados transacionados]
> Objetivo To-Be: [O que você espera que aconteça no final de forma automatizada]
> 
> Baseado nas regras de negócios, desenhe o fluxo lógico (Gatilho > Ação > Resultado), indique as ferramentas ideais e pontue possíveis falhas de segurança."

**2. Para Tradução de Termos e Treinamento de Equipe:**
> "[Cole aqui a explicação técnica gerada no prompt acima]
> Eu não entendo nada de programação, APIs ou bancos de dados. Por favor, ative sua persona didática e me explique como se eu tivesse 10 anos, usando analogias simples do mundo físico."

**3. Para Auditoria e Prevenção de Falhas (Teste de Estresse):**
> "No fluxo desenhado acima, imagine que o arquivo/sistema de origem entregou um dado corrompido, 100% vazio, ou com as colunas renomeadas silenciosamente. O que acontece com a arquitetura? Redesenhe o fluxo aplicando um mecanismo de Sanity Check e uma Fila de Mensagens Mortas (DLQ) para prevenir falhas silenciosas."
