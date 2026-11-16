# Aula: GTM Engineering no caso da EduData

> Caso fictício, criado para um exercício do curso Análise de Dados e TI aplicada à Gestão (Prepara Portugal).

Esta aula reúne os conceitos usados no projeto, com exemplos fora da tecnologia (jurídico, marketing), exercícios e correções.

---

## Parte 1: Conceitos

### 1.1 GTM e GTM Engineering

**GTM (go-to-market)** é o caminho que leva o serviço até o cliente: atrair, conquistar, atender e manter.

**GTM Engineering** trata esse caminho como um sistema construído com dados. Você define o que é cada etapa, mede e automatiza o que se repete.

*Jurídico:* um escritório que atende famílias tem um funil: interessado, consulta, contrato assinado, processo em andamento e cliente que fica ou sai. Um GTM Engineer olharia esse funil com números e regras, não com a memória de cada advogado.

### 1.2 Lead, conversão e churn

- **Lead:** potencial cliente que demonstrou interesse e ainda não comprou.
- **Conversão:** quantos leads viram clientes. Se 4 em cada 100 leads viram cliente, a conversão é 4%.
- **Churn:** perda de clientes, por cancelamento ou por deixar de usar o serviço.

Fórmula do churn usada no projeto: cancelamentos no mês ÷ clientes no início do mês × 100.

*Jurídico:* lead é quem pediu consulta; conversão é o contrato assinado; churn é o cliente de assessoria mensal que cancela antes do fim do acompanhamento.

### 1.3 Os três níveis

1. **Fundação de Dados:** registros corretos, atualizados e sem duplicidade. Na EduData: CRM com escolas cadastradas duas vezes.
2. **Modelagem de Dados:** definições. O que é lead, como se conta a conversão, o que é churn. Na EduData: conversão de 4% sem explicação das causas.
3. **Ativação de Dados:** usar os dados para agir, com alertas, playbooks e tarefas. Na EduData: pesquisa manual e demora para ver sinais de churn.

**Por que a ordem importa:** automatizar um alerta sobre dados duplicados avisa a pessoa errada.

*Jurídico:* o mesmo processo cadastrado duas vezes faz o prazo aparecer em dobro. Antes de criar um alerta de prazo, é preciso um cadastro único de processo.

### 1.4 Chave única

Chave única é o identificador que aparece uma só vez no sistema. No projeto, é o código da escola (E01 a E08). Sem ele, duas linhas com nomes parecidos viram duas escolas.

*Jurídico:* o número do processo ou o CPF do cliente funciona como chave única.

### 1.5 Regra de "pelo menos um sinal"

A regra do projeto liga três condições com "ou":
- queda de acessos de 30% ou mais; **ou**
- pelo menos 1 reclamação no mês; **ou**
- duas tentativas de contato sem resposta em 7 dias.

Basta uma delas para gerar alerta.

*Jurídico:* um alerta de processo que dispara se faltar documento, **ou** o prazo estiver a 5 dias, **ou** o cliente não responder há 10 dias.

### 1.6 Janela de tempo

A janela de 7 dias conta só as tentativas dentro de um período. Sem ela, tentativas de setembro e de novembro somariam, e a escola receberia alerta por um problema antigo.

*Jurídico:* "duas tentativas sem resposta" precisa dizer "em até 7 dias", senão o alerta pega casos antigos.

### 1.7 Métrica e linha de base

Métrica é um número com regra de cálculo definida antes. Linha de base é o valor da métrica antes da mudança.

Métricas do projeto:
- **Tempo entre alerta e primeiro contato** (principal): mede a demora descrita no cenário.
- **Tempo de primeira resposta do suporte:** verifica a hipótese de que o atendimento influencia o churn.
- **Churn mensal:** cancelamentos ÷ clientes no início do mês × 100.

*Jurídico:* dizer que "o tempo de resposta caiu para 3 dias" só significa algo se você souber que antes era 10.

### 1.8 Simulação, piloto e efeito real

- **Simulação:** você cria dados e confere se a regra faz o que está escrito. É o que o projeto fez.
- **Piloto:** você testa com dados reais de poucas escolas por um período.
- **Efeito real:** você mede se o churn caiu de fato. Exige meses de dados.

A simulação prova que a regra funciona. Não prova que ela reduz o churn.

### 1.9 Playbook

Playbook é um roteiro de ação padronizado: quando X acontece, a equipe faz Y, com passos definidos antes.

*Jurídico:* quando um cliente novo chega, o escritório segue um checklist de documentos, abertura de processo e prazo inicial.

*Marketing:* quando um lead abre o e-mail, a equipe faz contato em 24 horas e, se não houver resposta, envia um material e agenda uma ligação.

---

## Parte 2: Planilha para iniciantes

Cada linha da aba **Analise** é uma escola. Cada coluna é uma conta.

| Coluna | Fórmula | Em palavras |
|---|---|---|
| Queda (%) | `=IF(C2=0,0,(C2-D2)/C2)` | (anterior − atual) ÷ anterior; zero se o anterior for zero |
| Sinal de acessos | `=IF(E2>=Parametros!B2,"Sim","Não")` | Sim se a queda for igual ou maior que o limite |
| Tentativas no prazo | `=COUNTIFS(...)` | Conta tentativas sem resposta dentro da janela |
| Quantidade de sinais | `=(F2="Sim")+(H2="Sim")+(K2="Sim")` | Soma dos sinais |
| Decisão | `=IF(L2>=1,"Sim: gerar alerta","Não: continuar monitorando")` | Alerta se houver pelo menos um sinal |

Três pontos de iniciante:
1. **Nomes em inglês.** `IF`, `COUNTIFS` e `MINIFS` aparecem em inglês porque a planilha está em en_US.
2. **Porcentagem guardada como 0,3.** Digitar 30 no parâmetro quebra a regra.
3. **Separador.** Em en_US, as fórmulas usam vírgula. Em pt-BR, ponto e vírgula. Misturar os dois gera erro.

---

## Parte 3: O fluxo

1. **Entrada:** reclamações, pedidos e contatos entram pelo Google Forms; acessos vêm de uma exportação.
2. **Organização:** a planilha guarda uma linha por escola, pelo código.
3. **Decisão:** a regra verifica os três sinais.
4. **Ação:** o alerta é gerado e uma pessoa do Customer Success entra em contato.

**Por que a etapa 4 é humana:** o contato com a escola precisa de consentimento e cuidado. Mensagens automáticas sem revisão podem ferir a proteção de dados (RGPD).

**Por que ferramentas gratuitas:** o exercício pede uma solução que possa ser testada sem custo.

---

## Parte 4: Diagnóstico e escolha

- **Fato:** o que está no enunciado (ex.: "só 4% dos leads viram clientes").
- **Hipótese:** a causa que o enunciado não informa (ex.: "a demora no primeiro contato explica a conversão baixa").

Hipótese se testa. Fato se registra.

Classificação:
- **Fundação:** CRM duplicado.
- **Modelagem:** conversão de 4%.
- **Ativação:** pesquisa manual e demora no churn.

Por que churn: é verificável com dados (data do alerta e do contato), tem consequência clara (perda de receita recorrente) e cabe em ferramenta gratuita.

---

## Parte 5: A simulação

Oito escolas, cada uma testando um caso. O resultado esperado foi definido antes de aplicar a regra.

| Escola | Caso | Esperado | Resultado |
|---|---|---|---|
| E01 | Acessos quase estáveis, contato respondido | Não | Não |
| E02 | Queda de exatamente 30% | Sim | Sim |
| E03 | Uma reclamação | Sim | Sim |
| E04 | Uma tentativa sem resposta | Não | Não |
| E05 | Duas tentativas em 4 dias | Sim | Sim |
| E06 | Segunda tentativa 8 dias depois | Não | Não |
| E07 | Queda de 29% | Não | Não |
| E08 | Três sinais ao mesmo tempo | Sim | Sim |

Resultado: 8 de 8.

---

## Parte 6: Insights e limites

- A regra separa bem os casos de fronteira (E06 e E07).
- A reclamação dispara sozinha. Pode gerar muitos alertas por situações simples.
- O alerta da E05 chegou um dia antes do cancelamento. O contato foi no mesmo dia, e a simulação não diz se foi antes ou depois. Um piloto precisa medir isso.
- 16,1 horas (alerta até contato) e 15,4 horas (primeira resposta do suporte) são medidas diferentes e não se comparam diretamente.
- Uma escola pesa 12,5 pontos de churn. Com oito escolas, a taxa serve para tendências, não para conclusões.
- Dados fictícios testam a regra, não o comportamento real.

---

## Exercícios

**Exercício 1 (jurídico).** Um escritório tinha 40 clientes de assessoria mensal no início de março. Dois cancelaram em março. Qual o churn?

**Exercício 2 (jurídico).** O portal do cliente teve 50 acessos no mês anterior e 33 no atual. A regra de queda de 30% dispara?

**Exercício 3 (tentativas).** Um cliente teve tentativas sem resposta nos dias 3, 6 e 12. Prazo de 7 dias, mínimo de 2. Qual o resultado? E se a segunda tentativa tivesse sido no dia 11?

**Exercício 4 (marketing).** 200 leads, 12 viraram clientes. Qual a conversão?

**Exercício 5 (conceitual).** Por que a E06 não gera alerta, mesmo com duas tentativas?

### Correção

1. 2 ÷ 40 × 100 = **5%**.
2. (50 − 33) ÷ 50 = 34%. Como 34% ≥ 30%, **dispara**.
3. Janela a partir do dia 3: até o dia 10. Dentro dela estão os dias 3 e 6, que são 2 tentativas. Como 2 ≥ 2, **sinal Sim**. Se a segunda tentativa fosse no dia 11 e não no dia 6, a janela teria só 1 tentativa e o sinal ficaria **Não**.
4. 12 ÷ 200 × 100 = **6%**.
5. A primeira tentativa foi no dia 1 e a segunda no dia 9. A janela vai até o dia 8, então a segunda fica fora. Conta só 1, e 1 é menor que 2. **Não gera alerta**, como esperado.
