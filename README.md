# Carregamento de Produtos

Este repositório contém a exportação de uma solução do Microsoft Power Platform para o controle de carregamento de notas fiscais e volumes de produtos em um aplicativo Canvas do Power Apps.

## Visão geral

O projeto implementa um fluxo completo de gestão de carregamento de produtos, com foco em:

- criação e edição de notas fiscais
- listagem de NF pendentes e concluídas
- registro de volumes por produto
- consulta de histórico por NF/volume/peça/série
- cancelamento de NF com justificativa
- estorno individual de volumes já carregados

A solução foi identificada como um pacote Power Platform com os seguintes metadados principais:

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

## Arquitetura funcional do aplicativo

Os trechos do código anexados indicam um aplicativo com vários screens, sendo os principais:

- `scrInicio` — tela inicial / navegação geral
- `scrNovaNF` — cadastro e edição de NF
- `scrPendentes` — listagem de NFs pendentes
- `scrCarregamento` — registro de volumes em uma NF
- `scrConsulta` — consulta do histórico e estorno de volumes
- `scrConcluidas` — listagem de NFs concluídas

Os dados do app são tratados por meio das coleções e variáveis globais, principalmente:

- `VarCabecalho` — NF selecionada no momento
- `VarProcessando` — bloqueio de operação em carregamento
- `VarProcessandoEstorno` — bloqueio de operação em estorno
- `VarMostrarEstorno` — exibição do modal de estorno
- `VarLinhaEstorno` — linha selecionada para estorno
- `VarMostrarCancelamento` — exibição do modal de cancelamento
- `VarNFParaCancelar` — NF selecionada para cancelamento
- `VarAba` — alternância entre registro e volumes em mobile
- `colLinhasNF` — linhas carregadas da NF atual
- `VarVolumesCarregados` — contagem atual de volumes carregados
- `VarProximoVolume` — próximo número de volume disponível

## Fontes de dados e entidades

Os nomes dos dados sugerem que o app usa entidades/coleções de Power Apps relacionadas a expedição:

- `expedicao_cabecalho` — cabeçalho da NF/expedição
- `Expedicao_Linhas` — linhas/volumes da NF

Campos e propriedades observados:

- `ID`
- `NumeroNF`
- `Cliente`
- `Volumes`
- `VolumesCarregados`
- `Status` (tipo de opção / Choice)
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

## Regras de negócio principais

### 1) Cadastro de nova NF
Na tela `scrNovaNF`, o usuário preenche:

- número da NF
- cliente
- quantidade de volumes
- observação opcional

A lógica de criação/edição valida:

- número da NF não pode ficar em branco
- cliente obrigatório
- quantidade de volumes deve ser positiva
- a edição não permite alterar o número da NF
- ao confirmar, a NF entra em status `PENDENTE`

### 2) Status da NF
A aplicação utiliza um fluxo de status em torno da expedição:

- `PENDENTE` — NF aberta, ainda não concluída
- `EM CARREGAMENTO` — há pelo menos 1 volume carregado, mas faltam mais
- `CONCLUÍDO` — todos os volumes foram carregados
- `CANCELADO` — NF cancelada por motivo informado
- `ESTORNADO` — volume individual removido do carregamento

Regra da tela de carregamento:

- se `VarVolumesCarregados >= VarCabecalho.Volumes`, a NF é tratada como concluída
- se `VarVolumesCarregados > 0`, status vira `EM CARREGAMENTO`
- se `VarVolumesCarregados <= 0`, status volta para `PENDENTE`

### 3) Registro de volumes
Na tela `scrCarregamento`, o usuário:

- escolhe o tipo de produto: `Peça` ou `Máquina`
- informa a série ou a peça conforme o tipo
- registra o volume com base no próximo número disponível

Validações aplicadas:

- a NF não pode receber novos carregamentos se já está `CONCLUÍDO` ou `CANCELADO`
- `Tipo de Produto` é obrigatório
- para máquina: a série é obrigatória e deve conter apenas números
- para peça: o campo de peça deve ser preenchido
- não permite registrar volumes excedentes ao total da NF
- não permite duplicar série já ativa em outra linha com status `CARREGADO`
- não permite registrar o mesmo volume/chave de volume já em uso

A lógica de controle interna usa:

```powerapps
Sequence(VarCabecalho.Volumes) As Vol
Filter(... !(Vol.Value in colLinhasNF.Lin_NumeroVolume))
```

Essencialmente, o app calcula o próximo volume livre da sequência e evita duplicações.

### 4) Atualização da NF após registro
Ao registrar um volume, o app:

- atualiza a tabela `Expedicao_Linhas`
- cria uma linha com:
  - `Lin_CabecalhoID`
  - `Lin_NumeroNF`
  - `Lin_NumeroVolume`
  - `Lin_ChaveVolume`
  - `Lin_TipoProduto`
  - `Lin_SerieProduto` ou `Lin_Peca`
  - `Lin_DataCarregamento`
  - `Lin_UsuarioCarregamento`
  - `Lin_Status = "CARREGADO"`
- recalcula `VolumesCarregados` no cabeçalho
- atualiza status da NF conforme o saldo restante

### 5) Consulta de volumes e histórico
Na tela `scrConsulta`, a galeria apresenta todos os volumes vinculados à NF selecionada.

A busca pode ser feita por:

- número de série
- peça
- número do volume

A lógica utiliza filtros condicionais:

- se busca vazia: mostra todos os volumes da NF
- se busca for numérica: filtra por `Lin_NumeroVolume` ou `Lin_SerieProduto`
- se busca for texto: filtra por `Lin_SerieProduto` ou `Lin_Peca`

A tela exibe informações como:

- data e hora do carregamento
- tipo de produto
- volume
- status
- possibilidade de estorno quando o status for `CARREGADO`

### 6) Estorno de volume
A funcionalidade de estorno é acionada na lista de consulta.

Ao confirmar o estorno, o app:

- valida se houve preenchimento do motivo
- valida se a linha existe e está com status `CARREGADO`
- valida se a NF não está `CANCELADO`
- executa `Patch` na `Expedicao_Linhas` alterando:
  - `Lin_Status = "ESTORNADO"`
  - `Lin_DataEstorno = Now()`
  - `Lin_UsuarioEstorno = User().FullName`
  - `Lin_MotivoEstorno = Trim(txtMotivoEstorno.Text)`
- recalcula o total de volumes carregados da NF
- recalcula o status do cabeçalho baseado no novo saldo
- atualiza `DataCarregamento` somente quando a NF entra em condição concluída

A lógica da atualização do cabeçalho foi implementada com a seguinte regra de negócio:

- se `VarVolumesCarregados - 1 >= VarCabecalho.Volumes` → status `CONCLUÍDO`
- se `VarVolumesCarregados - 1 <= 0` → status `PENDENTE`
- caso contrário → `EM CARREGAMENTO`

### 7) Cancelamento de NF
Na tela `scrPendentes`, o usuário pode cancelar uma NF quando ela ainda está em estado inicial.

As condições para cancelamento:

- NF deve estar `PENDENTE`
- `VolumesCarregados` deve ser zero
- não pode haver linhas com status `CARREGADO`

Quando confirmada, a aplicação faz `Patch` em `expedicao_cabecalho` com:

- `Status = {Value: "CANCELADO"}`
- `MotivoCancelamento`
- `DataCancelamento`
- `UsuarioCancelamento`

## Fluxo principal do usuário

1. Acessa a tela inicial
2. Cria ou seleciona uma NF
3. Em `scrPendentes`, abre a NF para carregamento
4. Na tela de carregamento, registra cada volume
5. Acompanhar o saldo e status da NF
6. Consultar histórico e estornar volumes se necessário
7. Concluir a operação ou cancelar a NF antes do início do carregamento

## Padrões visuais e UX

O aplicativo usa uma identidade visual consistente baseada em:

- cor principal: vermelho escuro (`RGBA(164, 38, 44, 1)`) 
- fundo cinza claro (`RGBA(234, 234, 234, 1)`) 
- fonte `Open Sans`
- cards e listas com bordas vermelhas e destacado visual para itens selecionados
- layout responsivo com regras específicas para telas pequenas (`App.Width < 600`)

## Observações finais

O código anexado demonstra que o app foi construído para controlar o processo de carregamento de produtos de forma operacional e com regras de integridade importantes, principalmente em relação a:

- integridade de sequência de volumes
- unicidade de séries ativas
- manutenção do status da NF
- controlo de estorno por volume
- rastreabilidade de dados por usuário e motivo

Este repositório, portanto, não é apenas um app visual, mas um conjunto de regras de negócio e fluxo operacional para gestão de expedição e carregamento de produtos em ambiente Power Platform.

## Autor

Raniere Pereira

## Status

Repositório inicializado com a solução exportada e documentação funcional em evolução.
