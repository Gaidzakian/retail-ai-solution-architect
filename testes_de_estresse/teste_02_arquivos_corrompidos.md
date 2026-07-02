# Teste de Estresse 02: Arquivos Corrompidos e Dead Letter Queue

## 📌 Objetivo do Teste
Validar a resiliência da automação contra anomalias na fonte de dados (falhas do ERP). O objetivo é garantir que a entrada de um arquivo vazio ou corrompido não resulte em uma "falha silenciosa", implementando uma Fila de Mensagens Mortas (*Dead Letter Queue*) para alertar a equipe técnica imediatamente.

---

## 🧑‍💻 O Prompt Enviado (O Desafio do Caos)
> **Contexto e As-Is:** Temos a automação rodando onde o ERP exporta um CSV de 120 mil linhas e o n8n pega via Webhook para processar no Python (Pandas) e mandar para os gerentes.
> 
> **O Problema (Edge Case):** Hoje o ERP deu um erro bizarro e exportou o CSV totalmente em branco, apenas com a linha de cabeçalho.
> 
> **Desafio:** Como Arquiteto Sênior, o que acontece se esse arquivo vazio entrar no Code Node? Redesenhe o fluxo para lidar com essa falha antes que os gerentes fiquem esperando os alertas que nunca vão chegar. Entregue a Visão do Especialista e a Visão do Iniciante.

---

## 🤖 A Resposta do Assistente (A Solução Arquitetural)
O assistente previu perfeitamente o comportamento do motor de processamento e reestruturou a arquitetura para atuar de forma defensiva (*Fail-Fast*).

### 1. Visão do Especialista (Engenharia de Dados)
A IA identificou que um arquivo de 0 bytes faria o Pandas lançar um erro do tipo `EmptyDataError`, quebrando o fluxo silenciosamente. A solução adotada foi transformar o script em um "Guardião", que valida a integridade antes de processar.

**Trecho do Código Python Gerado:**
```python
import pandas as pd

output = []

try:
    # Tentativa de leitura defensiva (arquivos de 0 bytes)
    df = pd.read_csv("relatorio_vendas.csv")
except pd.errors.EmptyDataError:
    output.append({
        "status": "error",
        "tipo_erro": "ARQUIVO_VAZIO_TOTAL",
        "chat_id": "ID_CHAT_SUPORTE_TI",
        "mensagem": "🚨 *FALHA CRÍTICA ERP:* O arquivo exportado está em branco (0 bytes). Fluxo abortado. Verifique o ERP!"
    })
    return [{"json": item} for item in output]

# Verificação de arquivo apenas com cabeçalho
if df.empty or len(df) == 0:
    output.append({
        "status": "error",
        "tipo_erro": "SEM_DADOS_VENDAS",
        "chat_id": "ID_CHAT_SUPORTE_TI",
        "mensagem": "🚨 *ALERTA:* Arquivo com cabeçalho, mas sem transações."
    })
    return [{"json": item} for item in output]
