# Planilha da simulação (dados fictícios)

Os dados abaixo foram digitados à mão, como na planilha do exercício. Em uma implementação real, reclamações, contatos e pedidos de suporte viriam do Google Forms, e os acessos viriam de uma exportação da plataforma.

## Parâmetros da regra

| Parâmetro | Valor | Observação |
|---|---|---|
| Limite de queda de acessos | 30% | Hipótese inicial: 30% ou mais dispara o sinal |
| Mínimo de reclamações no mês | 1 | Qualquer reclamação dispara o sinal |
| Mínimo de tentativas sem resposta | 2 | Duas tentativas sem resposta |
| Prazo em dias | 7 | Prazo para contar as tentativas |

## Escolas

| Código | Escola | Tipo | Região | Data de cancelamento |
|---|---|---|---|---|
| E01 | Colégio Horizonte | Privada | Região A | |
| E02 | Escola Bom Pastor | Privada | Região A | |
| E03 | Instituto Saber Vivo | Privada | Região A | |
| E04 | Colégio São Lucas | Privada | Região A | |
| E05 | Escola Girassol | Privada | Região B | 10/11/2026 |
| E06 | Colégio Nova Era | Privada | Região B | |
| E07 | Escola Nascer Bem | Privada | Região B | |
| E08 | Colégio Ponte Viva | Privada | Região B | |

## Acessos

| Código | Outubro | Novembro |
|---|---|---|
| E01 | 180 | 175 |
| E02 | 200 | 140 |
| E03 | 150 | 145 |
| E04 | 120 | 116 |
| E05 | 220 | 209 |
| E06 | 160 | 150 |
| E07 | 100 | 71 |
| E08 | 130 | 80 |

## Reclamações (novembro)

- E03: reclamação sobre acesso de professores.
- E08: reclamação sobre relatórios.

## Tentativas de contato sem resposta

| Código | Datas (novembro) |
|---|---|
| E04 | 03/11 |
| E05 | 05/11 e 09/11 |
| E06 | 01/11 e 09/11 |
| E08 | 04/11 e 06/11 |

Também há um contato respondido da E01 (02/11). Ele não entra na regra, que conta só tentativas sem resposta.

## Suporte (horas até a primeira resposta)

| Código | Horas |
|---|---|
| E01 | 3 |
| E02 | 6 |
| E03 | 24 |
| E05 | 24 |
| E08 | 20 |

## Resultados calculados

| Código | Queda | Sinal de acessos | Reclamação | Sinal de contato | Sinais | Decisão |
|---|---|---|---|---|---|---|
| E01 | 2,8% | Não | Não | Não | 0 | Não: continuar monitorando |
| E02 | 30,0% | Sim | Não | Não | 1 | Sim: gerar alerta |
| E03 | 3,3% | Não | Sim | Não | 1 | Sim: gerar alerta |
| E04 | 3,3% | Não | Não | Não | 0 | Não: continuar monitorando |
| E05 | 5,0% | Não | Não | Sim | 1 | Sim: gerar alerta |
| E06 | 6,3% | Não | Não | Não | 0 | Não: continuar monitorando |
| E07 | 29,0% | Não | Não | Não | 0 | Não: continuar monitorando |
| E08 | 38,5% | Sim | Sim | Sim | 3 | Sim: gerar alerta |

## Alertas e tempos

| Escola | Alerta | Primeiro contato | Horas entre alerta e contato |
|---|---|---|---|
| E02 | 12/11 às 09:00 | 13/11 às 15:00 | 30 |
| E03 | 11/11 às 10:00 | 11/11 às 16:30 | 6,5 |
| E05 | 09/11 às 08:00 | 10/11 às 10:00 | 26 |
| E08 | 10/11 às 09:00 | 10/11 às 11:00 | 2 |

Média: **16,1 horas**.

## Churn de novembro

- Clientes no início do mês: 8
- Cancelamentos no mês: 1 (E05)
- Taxa de churn: 1 ÷ 8 × 100 = **12,5%**

Com 8 escolas, uma escola altera a taxa em 12,5 pontos. Use para comparar tendências, não como medida estatística.

## Fórmulas da planilha

As fórmulas estão escritas em inglês porque a planilha foi configurada em en_US. A lógica é esta:

| Coluna | Fórmula (exemplo, linha 2) | Em palavras |
|---|---|---|
| Queda (%) | `=IF(C2=0,0,(C2-D2)/C2)` | (mês anterior − mês atual) ÷ mês anterior; zero se o mês anterior for zero |
| Sinal de acessos | `=IF(E2>=Parametros!B2,"Sim","Não")` | Sim se a queda for igual ou maior que o limite |
| Primeira tentativa sem resposta | `=IF(COUNTIFS(Contatos!A:A,A2,Contatos!C:C,"Não")=0,"",MINIFS(Contatos!B:B,Contatos!A:A,A2,Contatos!C:C,"Não"))` | Menor data entre as tentativas sem resposta da escola |
| Tentativas sem resposta no prazo | `=IF(I2="",0,COUNTIFS(Contatos!A:A,A2,Contatos!C:C,"Não",Contatos!B:B,">="&I2,Contatos!B:B,"<="&(I2+Parametros!B5)))` | Tentativas sem resposta entre a primeira e a primeira + 7 dias |
| Quantidade de sinais | `=(F2="Sim")+(H2="Sim")+(K2="Sim")` | Soma dos sinais (cada "Sim" vale 1) |
| Decisão | `=IF(L2>=1,"Sim: gerar alerta","Não: continuar monitorando")` | Alerta se houver pelo menos um sinal |

Observação: o parâmetro de queda é guardado como 0,3 (30%). Digitar 30 no lugar quebra a regra.
