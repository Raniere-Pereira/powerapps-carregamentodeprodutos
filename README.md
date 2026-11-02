# Carregamento de Produtos

## Visão geral

Este repositório contém a exportação de uma solução do Microsoft Power Platform desenvolvida para controlar o processo de carregamento de produtos e volumes de notas fiscais em um aplicativo Canvas do Power Apps.

A solução foi estruturada para permitir o gerenciamento do ciclo operacional de uma nota fiscal, desde o cadastro inicial até o carregamento, consulta e eventual estorno de volumes.

## Objetivo do sistema

O sistema tem como objetivo acompanhar e controlar o carregamento de produtos em uma nota fiscal, com foco em:

- cadastro de NF
- acompanhamento do status da NF
- registro de volumes por série ou peça
- validação de performance e integridade do carregamento
- consulta de histórico por NF e volume
- estorno de volume individual
- cancelamento da NF quando necessário

## Solução Power Platform

Metadados identificados no repositório:

- Nome da solução: `CarregamentodeProdutos`
- Publisher: `Cr618fa`
- Prefixo: `cr583`
- Idioma: `1046` (português - Brasil)
- Versão: `1.0.0.1`
- Componente raiz: `cr583_aplicativocarregamento_2680f`

## Estrutura do repositório

```text
powerapps-carregamentodeprodutos/
├── README.md
├── .gitignore
└── CarregamentodeProdutos/
    └── src/
        ├── CanvasApps/
        │   ├── cr583_aplicativocarregamento_2680f.meta.xml
        │   ├── cr583_aplicativocarregamento_2680f_AdditionalUris0_identity.json
        │   └── cr583_aplicativocarregamento_2680f_BackgroundImageUri
        └── Other/
            ├── Customizations.xml
            └── Solution.xml
```

## Fluxo funcional do app

### 1. Cadastro de NF
A operação inicia com o cadastro ou edição da nota fiscal. Nessa etapa o usuário informa:

- número da NF
- cliente
- quantidade de volumes
- observação opcional

Validações aplicadas:

- número da NF obrigatório
- cliente obrigatório
- quantidade de volumes positiva
- edição da NF não deve alterar o número original
- status inicial da NF é `PENDENTE`

### 2. Listagem de pendentes
A tela de pendentes apresenta as NFs em andamento, com informações como:

- número da NF
- cliente
- volumes totais
- volumes carregados
- saldo restante
- status atual

Também há ações para:

- abrir uma NF para carregamento
- editar uma NF
- cancelar NF quando ela ainda está pendente e sem carregamento

### 3. Registro de carregamento
Ao abrir uma NF, o usuário acessa a tela de carregamento e informa:

- tipo do produto (`Peça` ou `Máquina`)
- número de série da máquina, ou conteúdo da peça
- volume atual a ser registrado

O sistema calcula automaticamente o próximo volume disponível e impede duplicações.

### 4. Validações no carregamento
O app valida as regras de negócio antes da gravação:

- NF não pode receber novos carregamentos se já está `CONCLUÍDO` ou `CANCELADO`
- tipo do produto é obrigatório
- série de máquina obrigatória e numérica
- peça obrigatória quando o tipo for `Peça`
- não pode exceder o total de volumes previstos
- não pode repetir série já carregada em outra linha ativa
- não pode duplicar a chave do volume já registrada

### 5. Status da NF
O status da NF é calculado automaticamente conforme o carregamento:

- `PENDENTE`: sem volumes carregados
- `EM CARREGAMENTO`: há volumes carregados, mas ainda faltam alguns
- `CONCLUÍDO`: todos os volumes foram carregados
- `CANCELADO`: NF cancelada

### 6. Consulta do histórico
A área de consulta mostra todos os volumes vinculados à NF selecionada, incluindo:

- volume
- tipo de produto
- data/hora do carregamento
- status da linha
- possibilidade de estorno

A busca pode ser feita por:

- número do volume
- número de série
- peça

### 7. Estorno de volume
Quando um volume foi carregado, o usuário pode realizar o estorno individual, desde que:

- informe o motivo do estorno
- a linha esteja com status `CARREGADO`
- a NF não esteja cancelada

Ao confirmar, o sistema:

- atualiza o status da linha para `ESTORNADO`
- grava a data e o usuário do estorno
- registra o motivo do estorno
- recalcula o total e o status da NF

### 8. Cancelamento de NF
A NF pode ser cancelada somente quando:

- está em status `PENDENTE`
- não há volumes carregados
- não existe linha ativa vinculada à NF

Ao cancelar, o sistema grava:

- motivo do cancelamento
- data do cancelamento
- usuário responsável

## Regras de negócio principais

### Fluxo operacional da NF

1. O usuário cadastra a NF
2. A NF fica pendente
3. O usuário registra volumes
4. O sistema atualiza a contagem e o status
5. Quando todos os volumes forem registrados, a NF passa para `CONCLUÍDO`
6. O histórico pode ser consultado
7. Um volume individual pode ser estornado
8. A NF pode ser cancelada antes do início do carregamento

### Lógica de cálculo de status

O status da NF é atualizado com base na quantidade carregada:

- se `VolumesCarregados >= Volumes` → `CONCLUÍDO`
- se `VolumesCarregados > 0` → `EM CARREGAMENTO`
- se `VolumesCarregados <= 0` → `PENDENTE`

### Lógica de próximo volume

O app calcula o próximo volume livre da sequência:

- percorre os volumes previstos
- compara com os já carregados
- identifica o primeiro volume ainda não ocupado

Isso evita duplicidade e mantém a sequência consistente.

## Dados e entidades principais

Os nomes sugerem o uso de duas entidades principais:

- `expedicao_cabecalho` — dados gerais da NF
- `Expedicao_Linhas` — dados dos volumes/itens da NF

Campos importantes identificados:

- `ID`
- `NumeroNF`
- `Cliente`
- `Volumes`
- `VolumesCarregados`
- `Status`
- `DataCarregamento`
- `Observacao`
- `MotivoCancelamento`
- `DataCancelamento`
- `UsuarioCancelamento`
- `Lin_CabecalhoID`
- `Lin_NumeroNF`
- `Lin_NumeroVolume`
- `Lin_ChaveVolume`
- `Lin_TipoProduto`
- `Lin_SerieProduto`
- `Lin_Peca`
- `Lin_DataCarregamento`
- `Lin_UsuarioCarregamento`
- `Lin_Status`
- `Lin_DataEstorno`
- `Lin_UsuarioEstorno`
- `Lin_MotivoEstorno`

## Rastreabilidade e auditoria

A aplicação mantém um bom nível de rastreabilidade, pois grava:

- data e hora do carregamento
- usuário que cadastrou o volume
- data e motivo do estorno
- usuário responsável pelo estorno
- data, usuário e motivo do cancelamento

Isso é importante para acompanhamento operacional, auditoria e suporte.

## Interface e comportamento visual

O app possui uma identidade visual consistente baseada em:

- fundo claro em cinza
- elementos com bordas e destaque em vermelho escuro
- fonte `Open Sans`
- botões com destaque para ações principais
- layout adaptativo para desktop e mobile

## Observações técnicas

Este repositório não é um projeto de código tradicional em Java, .NET, Python, Node.js ou React. Ele representa uma solução exportada do Microsoft Power Platform, contendo arquivos de metadados e artefatos de aplicação Canvas.

A manutenção da solução normalmente é feita em ambientes do Power Platform, seguindo o ciclo de:

1. exportação da solução
2. versionamento em Git
3. revisão de mudanças
4. importação em ambiente de destino

## Recomendações de uso

- manter versionamento de cada alteração da solução
- validar regras de negócio antes de importação em produção
- revisar os status das NFs em testes
- revisar os campos de estorno e cancelamento antes de publicar
- confirmar se a base de dados do ambiente contém as entidades e colunas esperadas

## Conclusão

Este projeto atende a um fluxo operacional prático e controlado de carregamento de produtos em uma nota fiscal, com regras de validação e rastreabilidade essenciais para uso em ambiente corporativo.

A solução está organizada como um pacote de exportação da Power Platform, permitindo versionamento e manutenção em Git, além de facilitar a evolução do processo ao longo do tempo.

## Autor

Raniere Pereira

## Status do projeto

Versão inicial da documentação funcional concluída, com base na análise dos arquivos exportados e do fluxo de telas identificado no repositório.
