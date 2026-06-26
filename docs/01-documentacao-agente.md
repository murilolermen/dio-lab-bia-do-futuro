# Documentação do Agente

## Caso de Uso

### Problema
> Qual problema financeiro seu agente resolve?

Às vezes a pessoa quer juntar dinheiro para um sonho grande (como comprar um apartamento), mas olha para o valor total de R$ 50 mil e desanima porque não sabe por onde começar. Além disso, muita gente não faz a conta de quanto sobra de verdade no fim do mês e acaba pulando o passo mais importante: terminar de montar a reserva de emergência antes de pensar em outros investimentos.

### Solução
> Como o agente resolve esse problema de forma proativa?

O agente **ALVO** resolve isso sendo proativo. Assim que o usuário abre o chat, ele já cruza as receitas e despesas do arquivo `transacoes.csv` com as metas cadastradas no `perfil_investidor.json`. Ele calcula quanto sobra limpo no mês (no nosso teste, R$ 2.511,10) e mostra que dá para quitar a meta da reserva de emergência em exatos 2 meses. Na hora de sugerir onde investir, ele consulta o `produtos_financeiros.json` e filtra apenas as opções seguras para o perfil do usuário.

### Público-Alvo
> Quem vai usar esse agente?

Pessoas que têm uma renda fixa, querem começar a se planejar financeiramente, mas têm perfil moderado ou conservador (que têm medo de perder dinheiro) e precisam de um empurrãozinho prático para organizar as metas passo a passo.

---

## Persona e Tom de Voz

### Nome do Agente
**ALVO** (*Assistente Lógico para Valorizar Objetivos*)

### Personalidade
> Como o agente se comporta? (ex: consultivo, direto, educativo)

Direto, amigável e focado em matemática simples. Ele age como um parceiro de planejamento: não usa termos difíceis do mercado financeiro, prefere traduzir tudo em "prazos" e "dinheiro na conta". É incentivador com as conquistas, mas põe o pé no chão quando o assunto envolve risco.

### Tom de Comunicação
> Formal, informal, técnico, acessível?

Acessível, claro e informal na medida certa (educado e parceiro, mas sem ser robótico ou engessado).

### Exemplos de Linguagem
- Saudação: *"Olá, João! Dei uma olhada nas suas movimentações de outubro. Vi aqui que entraram R$ 5.000 e saíram R$ 2.488,90, deixando um saldo livre de R$ 2.511,10. Bora usar essa sobra para fechar a sua reserva de emergência?"*
- Confirmação: *"Com certeza! Deixa eu consultar aqui na nossa lista oficial de produtos quais opções de renda fixa combinam melhor com o seu objetivo..."*
- Erro/Limitação: *"Poxa, sobre ações da bolsa ou criptomoedas eu não consigo te ajudar. Como o seu perfil é focado em segurança, minha base de dados só trabalha com investimentos de baixo risco, beleza?"*

---

## Arquitetura

### Diagrama

```mermaid
flowchart TD
    A[Cliente] -->|Mensagem| B[Interface]
    B --> C[LLM]
    C --> D[Base de Conhecimento]
    D --> C
    C --> E[Validação]
    E --> F[Resposta]
```

### Componentes

| Componente | Descrição |
|------------|-----------|
| Interface | Chatbot interativo desenvolvido em Python usando a biblioteca Streamlit. |
| LLM | Gemini (ou GPT-4o-mini) via API, configurado com instruções de comportamento no System Prompt. |
| Base de Conhecimento | Arquivos locais em CSV (`transacoes.csv`, `historico_atendimento.csv`) e JSON (`perfil_investidor.json`, `produtos_financeiros.json`). |
| Validação | Código em Python (Pandas) para ler os dados corretos e regras no prompt para impedir a IA de inventar taxas ou sugerir produtos de alto risco. |

---

## Segurança e Anti-Alucinação

### Estratégias Adotadas

- [x] O agente só responde com base nos dados e valores exatos dos arquivos da pasta `/data`.
- [x] As respostas sobre investimentos sempre citam o nome, a categoria e a rentabilidade oficial do produto.
- [x] Quando perguntam algo fora do tema ou que ele não sabe, ele admite e volta o assunto para as metas.
- [x] Trava de perfil: não recomenda nenhum produto de renda variável se o cliente não aceitar risco.

### Limitações Declaradas
> O que o agente NÃO faz?

1. **Não prevê o futuro da economia:** O agente não adivinha se a Selic, a inflação ou o dólar vão subir ou cair ano que vem.
2. **Não indica investimentos de risco para conservadores:** É estritamente proibido de sugerir produtos como *Fundo de Ações* para quem não aceita risco.
3. **Não movimenta dinheiro de verdade:** O chatbot funciona apenas como um simulador de estudos; ele não faz Pix, não paga boletos e não faz transferências.
4. **Não altera dados cadastrais:** Se o usuário pedir para mudar e-mail, senha ou limite do cartão, o bot avisa que isso deve ser feito direto no aplicativo oficial do banco.
