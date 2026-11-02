# Carregamento de Produtos

Este repositório contém a solução exportada do Microsoft Power Platform para o aplicativo de carregamento de produtos, estruturado como uma solução Dataverse/Power Apps.

## Visão geral

O projeto foi identificado como uma solução Power Platform com o nome:

- Nome da solução: CarregamentodeProdutos
- Prefixo do publisher: `cr583`
- Aplicativo principal: `cr583_aplicativocarregamento_2680f`
- Idioma principal: português do Brasil (`1046`)
- Versão: `1.0.0.1`

A estrutura indica que se trata de um aplicativo Canvas do Power Apps empacotado em uma solução exportada, juntamente com os metadados de solução e personalização.

## Estrutura do repositório

```text
powerapps-carregamentodeprodutos/
├── .gitignore
├── CarregamentodeProdutos/
│   ├── .gitignore
│   └── src/
│       ├── CanvasApps/
│       │   ├── cr583_aplicativocarregamento_2680f.meta.xml
│       │   ├── cr583_aplicativocarregamento_2680f_AdditionalUris0_identity.json
│       │   └── cr583_aplicativocarregamento_2680f_BackgroundImageUri
│       └── Other/
│           ├── Customizations.xml
│           └── Solution.xml
```

## Principais arquivos

### `CarregamentodeProdutos/src/Other/Solution.xml`
Arquivo principal da solução, contendo:
- nome da solução
- publisher
- prefixo do componente
- componente raiz do aplicativo
- versão e metadados do pacote

### `CarregamentodeProdutos/src/Other/Customizations.xml`
Arquivo de personalização da solução, contendo:
- configurações de linguagem
- lista de componentes personalizados
- metadados gerais do ambiente

### `CarregamentodeProdutos/src/CanvasApps/`
Diretório que armazena os arquivos do aplicativo Canvas exportado, incluindo a definição do app e demais artefatos associados.

## Observações

- Este repositório não é um projeto tradicional em JavaScript, Python ou .NET.
- Ele representa uma exportação de solução da Power Platform para versionamento e manutenção em Git.
- A importação e o uso deste conteúdo normalmente são feitos no Power Platform / Dataverse, por meio do pacote de solução ou do Power Platform CLI.

## Próximos passos sugeridos

1. Validar o arquivo `Solution.xml` e confirmar os componentes expostos na solução.
2. Revisar o aplicativo em Canvas no Power Apps para confirmar a lógica de carregamento de produtos.
3. Adicionar documentação específica da tela, regra de negócio, fontes de dados e fluxo de uso.
4. Considerar a criação de um processo de publicação e versionamento para a solução.

## Licença

Nenhuma licença foi definida explicitamente no repositório até o momento.

## Autor

Raniere Pereira
