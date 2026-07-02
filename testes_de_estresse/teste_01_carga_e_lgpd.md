# Teste de Estresse 01: Carga Massiva, Sistemas Legados e LGPD

## 📌 Objetivo do Teste
Validar a capacidade do Assistente de IA de propor uma arquitetura escalável para processar relatórios massivos de um ERP legado (sem API), garantindo a anonimização de dados sensíveis de clientes antes da distribuição, em conformidade estrita com a LGPD.

---

## 🧑‍💻 O Prompt Enviado (O Desafio)
> **Cenário (As-Is):** Um ERP legado sem API exporta, à meia-noite, um arquivo CSV com 120.000 linhas para o Google Drive de uma rede de varejo farmacêutico. Um analista comercial baixa esse arquivo manualmente e gasta 4 horas no Excel cruzando dados para identificar produtos zerados, gerando um PDF genérico que é compartilhado no WhatsApp.
> 
> **Gargalo:** O arquivo satura as máquinas locais. O PDF é ignorado pelo excesso de dados, e há altíssimo risco de desconformidade com a LGPD devido ao tráfego de dados sensíveis (CPFs).
> 
> **Objetivo (To-Be):** Criar um fluxo automatizado que capture o arquivo passivamente, limpe os dados sensíveis (LGPD), filtre os produtos zerados da Curva A e dispare alertas individualizados via Telegram para cada gerente.

---

## 🤖 A Resposta do Assistente (Resumo da Solução)
A IA demonstrou excelente aderência às regras de negócio e boas práticas de engenharia de dados:

* **Arquitetura Orientada a Eventos:** Sugeriu um gatilho de Webhook (`On File Added`) no Google Drive, eliminando rotinas de *polling*.
* **Manipulação de Matrizes (Python/Pandas):** Identificou que o n8n travaria ao tentar rodar 120.000 linhas em *loops* visuais. Sugeriu um *Code Node* em Python para processar os dados em memória.
* **Tratamento LGPD Nativo (*Fail-Fast*):** Aplicou o comando `df.drop()` no Python para eliminar imediatamente as colunas sensíveis antes mesmo de rodar os filtros comerciais.

**Trecho do Código Python Gerado:**
```python
import pandas as pd

# Carregamento performático da base
df = pd.read_csv("relatorio_vendas.csv")

# Sanitização LGPD: Eliminação imediata de dados sensíveis
df_clean = df.drop(columns=["cpf_cliente", "nome_cliente", "medicamento_controlado_comprador"], errors="ignore")

# Filtragem de Negócio: Estoque zerado e produtos Curva A
df_ruptura = df_clean[(df_clean["estoque_atual"] == 0) & (df_clean["curva_abc"] == "A")]

## 🩹 A Cicatriz e a Correção (Troubleshooting)
O Ponto Cego (A Falha Arquitetural)
Apesar da genialidade no uso do Python para evitar o crash do servidor, a IA cometeu um erro grave de escalabilidade na etapa final. Ela sugeriu o uso de um nó visual de roteamento estático (Switch Node) para separar os alertas de cada loja. Em uma rede comercial com dezenas de filiais, isso criaria um fluxo visual monstruoso e insustentável de gerenciar.

O Feedback e a Iteração
Para corrigir esse desvio, não apenas instruí a IA no chat, mas alterei a base de conhecimento (o Documento Semente), injetando uma nova diretriz de escalabilidade:

Feedback / Nova Regra Implementada: "A partir de agora, obedeça à Lei do Roteamento Dinâmico. Para envio de dados a múltiplos destinatários, é terminantemente proibido utilizar nós de roteamento visual (Switch). A arquitetura deve utilizar um único nó final alimentado por variáveis dinâmicas do JSON (ex: {{ $json.chat_id_loja }} e {{ $json.mensagem }})."

Veredito da Versão 1.0
A IA absorveu a instrução e redesenhou o fluxo final com um único nó HTTP Request para o Telegram, rodando em loop inteligente por baixo dos panos. O fluxo passou de um labirinto inescalável para uma linha reta resiliente.
