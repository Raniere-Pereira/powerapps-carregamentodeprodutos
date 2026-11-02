# Carregamento de Produtos

Repositório da solução do Microsoft Power Platform para controle de carregamento de produtos e volumes de notas fiscais.

## Estrutura

- `CarregamentodeProdutos/src/` — metadados da solução exportada
- `telas/` — documentos das telas do app, organizados por tela

### Documentos das telas

- `telas/scrConsulta.txt` — consulta da NF, filtros e estorno de volume
- `telas/scrConcluidas.txt` — NFs concluídas
- `telas/scrCarregamento.txt` — registro de volumes
- `telas/scrPendentes.txt` — NFs pendentes
- `telas/Screens.txt` — arquivo consolidado legado, mantido apenas como referência

## Fluxo principal

1. Cadastro/edição da NF
2. Listagem de pendentes
3. Registro de volumes por série ou peça
4. Consulta por NF/volume
5. Estorno de volume individual
6. Cancelamento ou conclusão da NF

## Observação

Este repositório funciona como documentação e backup do app Power Apps, preservando a estrutura das telas e as regras de negócio em formato legível.
