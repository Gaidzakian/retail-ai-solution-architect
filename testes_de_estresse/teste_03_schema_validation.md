# Teste de Estresse 03: Validação de Schema e Falhas Silenciosas de Estrutura

## 📌 Objetivo do Teste
Validar a capacidade da arquitetura de lidar com o *Schema Drift* (quando o sistema de origem altera a estrutura dos dados silenciosamente). O objetivo é garantir que o processamento seja abortado de forma controlada antes de manipular dados sensíveis, gerando um log de erro preciso (Troubleshooting) para a equipe de TI.

---

## 🧑‍💻 O Prompt Enviado (O Desafio do Chefão Final)
> **Contexto:** Nossa automação do ERP está rodando e o tratamento de arquivos vazios (0 bytes) está operante. Porém, no fim de semana, a equipe do ERP fez uma atualização silenciosa no banco de dados e alterou o cabeçalho na exportação do CSV. A coluna `estoque_atual` agora se chama `qtd_estoque` e `curva_abc` virou `classificacao_curva`.
> 
> **O Problema:** O arquivo CSV chega com 120.000 linhas. Ele não está vazio (passa pela barreira do Teste 02), mas as colunas que o nosso negócio espera sumiram.
> 
> **Desafio:** Como Arquiteto Sênior, o que acontecerá com o código se ele tentar filtrar rupturas nessas colunas? Redesenhe o script para incluir uma *Schema Validation* rigorosa. Se as colunas faltarem, barre o processamento, acione a Fila de Mensagens Mortas (TI) detalhando quais colunas faltaram, e aborte o fluxo.

---

## 🤖 A Resposta do Assistente (A Solução Arquitetural)
O assistente compreendeu que a ausência das colunas causaria um `KeyError` no Pandas, resultando no colapso do fluxo. A solução foi a criação de um **Schema Gate** (Portão de Validação) posicionado estrategicamente *antes* das regras de negócio.

### 1. Visão do Especialista (Engenharia de Resiliência)
A IA definiu uma lista de `MANDATORY_COLUMNS` e comparou com o cabeçalho do arquivo recebido. Se houvesse divergência, o script montaria um *payload* de erro rico, apontando exatamente o que faltava, poupando horas de depuração da equipe técnica.

**Trecho do Código Python Gerado:**
```python
# VALIDAÇÃO DE SCHEMA RIGOROSA (Sanity Check)
MANDATORY_COLUMNS = ["id_loja", "codigo_produto", "descricao_produto", "estoque_atual", "curva_abc"]

missing_columns = [col for col in MANDATORY_COLUMNS if col not in df.columns]

if missing_columns:
    output.append({
        "status": "error",
        "tipo_erro": "SCHEMA_VALIDATION_FAILED",
        "chat_id": "ID_CHAT_SUPORTE_TI",
        "mensagem": (
            f"🚨 *FALHA DE ESTRUTURA (SCHEMA):* O arquivo CSV possui colunas fora do padrão.\n\n"
            f"*Colunas Faltantes:* {', '.join(missing_columns)}\n"
            f"*Colunas Recebidas:* {', '.join(df.columns.tolist())}\n\n"
            f"Processamento abortado para evitar falhas em cascata."
        )
    })
    return [{"json": item} for item in output]

# Só APÓS a validação do schema, o código executa a limpeza da LGPD
df_clean = df.drop(columns=["cpf_cliente"], errors="ignore")
