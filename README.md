# retail-ai-solution-architect
AI-powered Solution Architect built with Google NotebookLM to design scalable and resilient automation workflows for retail operations.
# 🏗️ Arquiteto de Soluções Sênior em IA: Orquestração e Resiliência no Varejo

## 🎯 Contexto e Objetivos
Atuando na linha de frente do ecossistema comercial do varejo farmacêutico, lido diariamente com o desafio de conciliar sistemas ERP legados, alto volume de dados transacionais e a necessidade de entregar informações precisas para a ponta (gerentes de loja). 

O objetivo deste projeto, construído no Google NotebookLM, foi criar um **Assistente de Arquitetura de Soluções Sênior e Inovação**. A meta de estudo foi dominar a orquestração de integrações (n8n), o processamento massivo de dados em memória (Python/Pandas) e as práticas de resiliência corporativa, mantendo a rigorosa conformidade com a LGPD e prevenindo falhas silenciosas de dados.

## 📚 Curadoria de Fontes
Para garantir que a IA tomasse decisões embasadas em realidade operacional e engenharia de software de ponta, o NotebookLM foi alimentado com materiais focados em agilidade e eficiência logística:
1. **Curva ABC no Varejo:** Artigos sobre gestão de prioridade de estoque e prevenção de rupturas na cadeia de suprimentos.
2. **Documentação n8n e Integrações REST:** Guias focados em Webhooks e Roteamento Dinâmico.
3. **Engenharia de Dados com Pandas (Python):** Guias abertos sobre higienização de matrizes e tratamento de exceções massivas (`EmptyDataError`).
4. **Metodologia Lean e Inovação Corporativa:** Frameworks para desenho de cenários *As-Is* e *To-Be*.

## 🛠️ Engenharia de Prompts e "Cicatrizes" (Troubleshooting)
O maior aprendizado deste projeto não foi o prompt inicial, mas o processo de iteração e descoberta de pontos cegos na arquitetura (as "cicatrizes").

### A Estrutura do Prompt Semente (A Persona Dupla)
O prompt mestre foi desenhado para forçar a IA a investigar o problema (Fase 0) antes de entregar a solução. A IA possui uma dupla persona: **A Visão do Especialista** (arquitetura técnica) e a **Visão do Iniciante** (acionada pelo gatilho *"Me explique como se eu tivesse 10 anos"*).

### As Cicatrizes e a Evolução (V1.0 à V3.0)
* **V1.0 (O Caminho Feliz e a Falha de Escala):** A IA resolveu o problema de 120 mil linhas usando Python, mas sugeriu o uso de um `Switch Node` para separar mensagens por filial. *Cicatriz:* Em uma rede com muitas lojas, isso criaria um fluxo visual monstruoso. *Solução:* Foi criada a "Lei do Roteamento Dinâmico" nas diretrizes do NotebookLM.
* **V2.0 (A Fila de Mensagens Mortas):** Testamos o cenário de um ERP exportar um arquivo de 0 bytes. A automação teria falhado silenciosamente. *Cicatriz:* A operação ficaria paralisada sem aviso. *Solução:* A IA foi instruída a adicionar um bloco `try...except pd.errors.EmptyDataError`, desviando erros críticos para um canal de TI (Dead Letter Queue).
* **V3.0 (Schema Validation - O Chefão Final):** O teste final envolveu uma alteração silenciosa nas colunas do banco de dados do ERP (Schema Drift). *Cicatriz:* O Pandas leria o arquivo, mas as regras de negócio quebrariam. *Solução:* A IA implementou um *Schema Gate* rigoroso antes do tratamento de LGPD, mapeando `MANDATORY_COLUMNS` e barrando o fluxo preventivamente caso houvesse anomalias.

## 📖 Miniguia de Estudo

### 📝 Resumos Estruturados
* **Manipulação de Matrizes (Regra de Ouro):** Em orquestradores (n8n/Make), *loops* visuais são proibidos para altos volumes (> 5.000 linhas). O processamento deve ocorrer em memória via *Code Nodes* (Python/Pandas).
* **Defesa Ativa (Fail-Fast):** Falhas em integração de dados não devem ser tratadas no final do fluxo, mas barradas no primeiro microssegundo (Sanity Check) para evitar propagação de erros.

### 📚 Glossário
* **Roteamento Dinâmico:** Uso de variáveis JSON (ex: `{{ $json.chat_id }}`) em um único nó de disparo, eliminando a necessidade de dezenas de ramificações visuais estáticas (Switch).
* **Schema Drift:** Quando a estrutura do dado de origem (nome de colunas, tipo de dado) muda silenciosamente, quebrando integrações de destino.
* **Dead Letter Queue (DLQ):** Fila ou canal de escape para onde mensagens/arquivos defeituosos são enviados para análise da TI, garantindo que o erro não paralise a operação invisivelmente.
* **As-Is / To-Be:** Mapeamento de processos. *As-Is* é o estado atual (geralmente manual e com gargalos); *To-Be* é o estado futuro ideal (automatizado).

### 🤖 Prompts Reutilizáveis
**1. Para Mapeamento de Cenários e Arquitetura:**
> "Desafio de Arquitetura:
> Cenário As-Is: [Descreva como o processo é feito hoje]
> Gargalo e Volume: [Descreva onde dói e a quantidade de dados]
> Objetivo To-Be: [O que você espera que aconteça no final]
> 
> Como Arquiteto Sênior, desenhe o fluxo (Gatilho > Ação > Resultado), indique as ferramentas ideais e pontue possíveis falhas de segurança."

**2. Para Tradução de Termos (A Trava Didática):**
> "[Cole aqui a explicação técnica gerada no prompt acima]
> Eu não entendo nada de programação, APIs ou bancos de dados. Por favor, me explique como se eu tivesse 10 anos, usando analogias do mundo físico."

**3. Para Teste de Estresse e Resiliência:**
> "No fluxo desenhado acima, imagine que o arquivo/sistema de origem entregou um dado corrompido, vazio, ou com as colunas renomeadas silenciosamente. O que acontece com a arquitetura? Desenhe um mecanismo de Fail-Fast e Dead Letter Queue para prevenir essa falha silenciosa."
