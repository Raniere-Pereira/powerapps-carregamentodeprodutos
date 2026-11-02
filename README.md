# Carregamento de Produtos

Este repositório reúne a exportação de uma solução do Microsoft Power Platform para o gerenciamento de carregamento de produtos em um aplicativo Canvas do Power Apps.

## Visão geral

A solução foi identificada como um pacote de personalização do Dataverse/Power Platform com o nome `CarregamentodeProdutos`, contendo um componente raiz do tipo aplicativo Canvas (`type="300"`) e um publisher com prefixo `cr583`.

O objetivo do projeto, com base nos nomes e metadados, é dar suporte a um fluxo de carregamento de produtos, com foco em uso de interface do Power Apps e integração com o ecossistema Microsoft Power Platform.

## Informações da solução

- Nome da solução: `CarregamentodeProdutos`
- Publisher: `Cr618fa`
- Prefixo: `cr583`
- Código do idioma: `1046` (português - Brasil)
- Versão: `1.0.0.1`
- Componente raiz: `cr583_aplicativocarregamento_2680f`
- Tipo principal: aplicativo Canvas do Power Apps

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

## Descrição dos arquivos principais

### `README.md`
Arquivo de apresentação e documentação do repositório.

### `.gitignore`
Configuração para ignorar arquivos temporários e artefatos gerados por exportação e desenvolvimento do Power Platform, como:
- arquivos ZIP
- arquivos `.msapp`
- pastas `bin/` e `obj/`
- arquivos temporários e logs

### `CarregamentodeProdutos/src/Other/Solution.xml`
Arquivo que define a solução exportada, incluindo:
- nome da solução
- publisher
- versão
- componente raiz
- metadados do pacote da solução

### `CarregamentodeProdutos/src/Other/Customizations.xml`
Arquivo que registra as personalizações do ambiente e a configuração de idioma da solução.

### `CarregamentodeProdutos/src/CanvasApps/`
Diretório com os artefatos do aplicativo Canvas exportado da Power Platform, incluindo metadados e referências do app.

## Observações técnicas

Este repositório não contém uma aplicação tradicional em código-fonte (como .NET, Java, Python, React ou Node.js). Ele representa uma exportação de solução do Microsoft Power Platform, usada para versionamento e manutenção de componentes do Power Apps/Dataverse em Git.

Em outras palavras, a maior parte do valor do projeto está nos arquivos XML, JSON e nos metadados gerados pela Power Platform, e não em código de linguagem de programação usual.

## Como este projeto é normalmente usado

1. Exportar a solução do ambiente do Power Platform
2. Armazenar os artefatos em Git
3. Revisar versões e alterações em commits
4. Importar a solução em outro ambiente ou publicar novas versões

## Requisitos de contexto

Para utilizar plenamente este repositório, normalmente é necessário:
- acesso a um ambiente Microsoft Dataverse
- acesso ao Power Apps
- conhecimento de soluções do Power Platform
- possibilidade de importar/exportar soluções em ambiente de teste ou produção

## Práticas recomendadas

- manter uma política clara de versionamento
- revisar alterações em `Solution.xml` antes de importação
- validar o aplicativo em Canvas após cada atualização
- documentar regras de negócio e dependências do app
- manter backups do ambiente antes de importações em produção

## Status do projeto

O repositório está em estrutura inicial e funcional para versionamento da solução exportada, sendo um ponto de partida para documentação, revisão e evoluções futuras.

## Autor

Raniere Pereira

## Observação

A documentação foi preparada com base nos metadados da solução exportada disponíveis no repositório e pode ser refinada conforme o projeto evoluir e novos arquivos forem adicionados.
