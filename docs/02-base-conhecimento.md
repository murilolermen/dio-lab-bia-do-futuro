# Base de Conhecimento

## Dados Utilizados

Descreva se usou os arquivos da pasta data, por exemplo:

| Arquivo | Formato | Utilização no Agente |
|---------|---------|----------------------|
| `transacoes.csv` | CSV | Calcular o total de receitas e despesas do mês para descobrir o saldo real líquido que sobra para investir (R$ 2.511,10). |
| `historico_atendimento.csv` | CSV | Dar contexto de que o cliente já tirou dúvidas recentes sobre CDB e Tesouro Selic, evitando que a IA explique o básico tudo de novo. |
| `perfil_investidor.json` | JSON | Mapear o objetivo principal (completar reserva de R$ 15 mil), verificar quanto falta (R$ 5 mil) e travar investimentos de risco (`"aceita_risco": false`). |
| `produtos_financeiros.json` | JSON | Servir como catálogo exclusivo de consulta para a IA recomendar apenas produtos que se encaixem nas regras do perfil. |

---

## Adaptações nos Dados

Não fiz alterações nos valores numéricos originais. Mantive os dados mockados exatamente como vieram no repositório da DIO porque notei que os números foram planejados para um teste prático: no fluxo de caixa sobram exatamente R$ 2.511,10 por mês e faltam R$ 5.000,00 para bater a meta da reserva. Isso me permitiu testar se a IA conseguiria cruzar os arquivos e calcular sozinha o plano de ação de 2 meses.

*(Nota: Apenas realizei um tratamento básico no código Python via Pandas para garantir que as datas e valores flutuantes fossem lidos corretamente sem erros de formatação).*

---

## Estratégia de Integração

### Como os dados são carregados?
Os arquivos CSV e JSON são carregados uma única vez no início da execução da aplicação (quando a sessão do Streamlit é iniciada), utilizando a biblioteca `pandas` no Python. O script lê os arquivos da pasta local `/data`, organiza as informações principais e as armazena em memória para não precisar abrir os arquivos a cada nova mensagem digitada no chat.

### Como os dados são usados no prompt?
Os dados transformados pelo Pandas são convertidos em um bloco de texto limpo (string formatada) e inseridos de forma fixa no **System Prompt** da IA no início de cada conversa. Dessa forma, o modelo já recebe todo o "raio-X" financeiro e o catálogo de produtos do cliente antes mesmo de o usuário mandar o primeiro *"Olá"*.

---

## Exemplo de Contexto Montado

Mostre um exemplo de como os dados são formatados para o agente:

```text
=== DADOS DO CLIENTE ===
- Nome: João Silva (32 anos, Analista de Sistemas)
- Renda Mensal: R$ 5.000,00 | Patrimônio Atual: R$ 15.000,00
- Reserva de Emergência Atual: R$ 10.000,00
- Meta Prioritária: Completar Reserva (Objetivo: R$ 15.000,00 | Faltam: R$ 5.000,00)
- Perfil: Moderado | Aceita Risco: NÃO (Regra: Proibido indicar Renda Variável ou Ações)

=== RESUMO DE FLUXO DE CAIXA (OUTUBRO/2025) ===
- Entradas: R$ 5.000,00
- Saídas Totais: R$ 2.488,90
  * Moradia: R$ 1.380,00 (Aluguel + Luz)
  * Alimentação: R$ 570,00 (Supermercado + Restaurante)
  * Transporte: R$ 295,00 (Uber + Combustível)
  * Saúde: R$ 188,00 (Farmácia + Academia)
  * Lazer: R$ 55,90 (Netflix)
- Capacidade Real de Aporte no Mês: R$ 2.511,10

=== CONTEXTO DE ATENDIMENTOS ANTERIORES ===
- 15/09: Tirou dúvidas sobre rentabilidade e prazos de CDB (Resolvido)
- 01/10: Pediu explicação sobre como funciona o Tesouro Selic (Resolvido)

=== CATÁLOGO OFICIAL DISPONÍVEL ===
1. Tesouro Selic | Renda Fixa | Risco: Baixo | Rentabilidade: 100% da Selic | Mínimo: R$ 30,00
2. CDB Liquidez Diária | Renda Fixa | Risco: Baixo | Rentabilidade: 102% do CDI | Mínimo: R$ 100,00
3. LCI/LCA | Renda Fixa | Risco: Baixo | Rentabilidade: 95% do CDI | Mínimo: R$ 1.000,00 (Isento de IR, carência 90 dias)
4. Fundo Multimercado | Fundo | Risco: Médio | Rentabilidade: CDI + 2% | Mínimo: R$ 500,00
```
