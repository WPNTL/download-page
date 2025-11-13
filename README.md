# Página de Downloads REVIT

Repositório oficial do projeto **Página de Downloads REVIT**, desenvolvido pela **Projelmec** para disponibilizar aos colaboradores, clientes e parceiros um acesso rápido e organizado aos arquivos `.rfa` (famílias Revit) utilizados em projetos da empresa.

---

## Sumário

- [Visão Geral](#visão-geral)
- [Arquitetura e Estrutura do Projeto](#arquitetura-e-estrutura-do-projeto)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Geração do Arquivo Base (JSON)](#geração-do-arquivo-base-json)
- [Descrição Técnica do Script de Scrapping](#descrição-técnica-do-script-de-scrapping)
- [Hospedagem e Deploy](#hospedagem-e-deploy)
- [Guia de Instalação e Uso Local](#guia-de-instalação-e-uso-local)
- [Midias](#midias)
- [Manutenção e Atualizações](#manutenção-e-atualizações)
- [Licença](#licença)
- [Contato](#contato)

---

## Visão Geral

A **Página de Downloads REVIT** tem como objetivo centralizar o acesso a arquivos `.rfa` utilizados em projetos BIM desenvolvidos pela Projelmec.  
O sistema foi construído em **HTML, CSS e JavaScript puro**, consumindo um arquivo **JSON** que contém a estrutura completa das pastas e arquivos mapeados a partir de um diretório do **SharePoint** corporativo.

Essa solução visa:
- Reduzir o tempo de busca por arquivos de bibliotecas Revit.
- Facilitar o compartilhamento interno e externo de famílias padronizadas.
- Permitir a atualização rápida e automatizada da estrutura de download.

---

## Arquitetura e Estrutura do Projeto

O projeto é composto pelos seguintes componentes principais:

```
download-page/
├── index.html        # Página principal da aplicação
├── script.js         # Lógica de navegação e renderização dinâmica do JSON
├── style.css         # Folha de estilo da interface
├── data/
│   └── base.min.json    # Arquivo base contendo a hierarquia de pastas e arquivos
└── assets/           # Recursos visuais e ícones utilizados na interface
```

### Funcionamento Geral

1. O `index.html` é carregado no navegador, exibindo a interface inicial.  
2. O `script.js` lê o arquivo `base.min.json`, que contém o mapeamento de diretórios e arquivos.  
3. A navegação é gerada dinamicamente no front-end, permitindo download direto dos arquivos `.rfa`.  
4. A estrutura prioriza desempenho e simplicidade para uso corporativo interno.

---

## Tecnologias Utilizadas

- **HTML5**
- **CSS3**
- **JavaScript (puro)**
- **JSON**
- **Python 3** — utilizado para gerar o JSON base por meio de *scrapping* do SharePoint
- **SharePoint / OneDrive** — origem dos arquivos
- **cPanel / WHM** — ambiente de hospedagem

---

## Geração do Arquivo Base (JSON)

O arquivo `base.min.json` é gerado automaticamente por um script Python (`scrap.py`) que faz o *scrapping* do SharePoint.  
Esse script autentica-se via cookies obtidos diretamente do navegador, percorre recursivamente todas as pastas e arquivos `.rfa` e gera um JSON no formato hierárquico utilizado pelo site.

### Saída esperada:
```json
{
  "REVIT SITE": {
    "Ventiladores Centrífugos": {
      "ARRANJO 1": {
        "LINHA INDUSTRIAL LEVE": {
          "ILS-ISS-IFS": {
            "0710": {
              "90°": {
                "ILS-ISS-IFS_710_A1_CR90Z_C160.rfa": "https://projelmec-my.sharepoint.com/personal/wesley_carvalho_projelmec_com_br/_layouts/15/download.aspx?SourceUrl=%2Fpersonal%2Fwesley_carvalho_projelmec_com_br%2FDocuments%2FREVIT%20SITE%2FVentiladores%20Centr%C3%ADfugos%2FARRANJO%201%2FLINHA%20INDUSTRIAL%20LEVE%2FILS-ISS-IFS%2F0710%2F90%C2%B0%2FILS-ISS-IFS_710_A1_CR90Z_C160.rfa",
                "ILS-ISS-IFS_710_A1_R90W_C100.rfa": "https://projelmec-my.sharepoint.com/personal/wesley_carvalho_projelmec_com_br/_layouts/15/download.aspx?SourceUrl=%2Fpersonal%2Fwesley_carvalho_projelmec_com_br%2FDocuments%2FREVIT%20SITE%2FVentiladores%20Centr%C3%ADfugos%2FARRANJO%201%2FLINHA%20INDUSTRIAL%20LEVE%2FILS-ISS-IFS%2F0710%2F90%C2%B0%2FILS-ISS-IFS_710_A1_R90W_C100.rfa"
              }
            }
          }
        }
      }
    }
  }
}

```

---

## Descrição Técnica do Script de Scrapping

O script `scrap.py` foi desenvolvido em **Python 3** e realiza a coleta automatizada da estrutura de arquivos do SharePoint utilizando a API REST.

### Etapas de funcionamento:

1. **Entrada de cookies:** solicita ao usuário os cookies de autenticação (`rtFa`, `FedAuth`, `XSRF-TOKEN`), copiados diretamente do navegador via DevTools.  
2. **Extração de informações da URL:** analisa a URL do SharePoint informada na configuração e identifica domínio, site e caminho da pasta base.  
3. **Listagem recursiva:** consulta os endpoints REST (`/Files` e `/Folders`) do SharePoint para listar arquivos e subpastas.  
4. **Filtragem de arquivos:** apenas arquivos com a extensão `.rfa` são incluídos.  
5. **Geração de links diretos:** monta URLs válidas de download usando o layout `_layouts/15/download.aspx?SourceUrl=`.  
6. **Exportação para JSON:** salva a estrutura hierárquica resultante no arquivo configurado (`arquivos.json` por padrão).

### Estrutura geral do script:
```
scrap.py
├── CONFIG                 # Configurações principais (URL, extensão e nome do arquivo de saída)
├── get_cookies_from_user  # Captura os cookies do usuário
├── extract_site_info      # Analisa a URL do SharePoint
├── SharePointMapper       # Classe principal responsável pela extração e mapeamento
│   ├── list_folder_contents()
│   ├── map_recursive()
│   └── save_json()
└── main()                 # Função principal: coordena a execução
```

O script também apresenta contadores de pastas, arquivos e erros durante o processo, além de mensagens de progresso no console para facilitar o acompanhamento.

---

## Hospedagem e Deploy

O projeto está hospedado em ambiente **cPanel/WHM**, no domínio institucional da Projelmec.

**URL de Produção:** [https://projelmec.com.br/rfa/](https://projelmec.com.br/rfa/)

### Passos para Deploy

1. Fazer upload dos arquivos do repositório para o diretório `/public_html/rfa/`.  
2. Garantir que o `base.min.json` esteja atualizado.  
3. Verificar permissões adequadas de leitura.  
4. Testar a navegação e os links de download.

---

## Guia de Instalação e Uso Local

1. **Clonar o repositório:**
   ```bash
   git clone https://github.com/WPNTL/download-page.git
   cd download-page
   ```

2. **Executar localmente:**
   - Abra o arquivo `index.html` em um navegador, ou  
   - Utilize um servidor local (como o Live Server no VSCode).

3. **Atualizar o arquivo `base.min.json`:**
   - Execute o script `scrap.py` para gerar um novo arquivo JSON atualizado.

---
## Midias

### Tela Inicial:
![tela inicial](https://i.imgur.com/jrSTfrE.png)

### Ferramenta de Busca:
![Ferramenta de busca](https://i.imgur.com/rTA7bPa.png)

---

### Funcionamento na Visão do Usuario:
![Donwload-page-working](https://i.imgur.com/bNfwPF9.gif)

---

## Manutenção e Atualizações

- Executar o script de scrapping periodicamente para atualizar a listagem de arquivos.  
- Substituir o `base.min.json` antigo pela nova versão gerada.  
- Validar o carregamento no ambiente de produção após cada atualização.  
- Registrar mudanças significativas no histórico de versões do GitHub.

---

## Licença

Este projeto é licenciado sob os termos da **MIT License**.  
Consulte o arquivo `LICENSE` para mais informações.

---

## Contato

**Projelmec – Tecnologia da Informação**  
Responsável: Wesley Puntel Carvalho  
E-mail: [wesley.carvalho@projelmec.com.br](mailto:wesley.carvalho@projelmec.com.br)  

Meu site: [https://wesleypuntel.dev](https://terminal-portfolio-sand-omega.vercel.app)

Meu Github: [@WPNTL](https://github.com/WPNTL)

Site da Empresa: [https://projelmec.com.br](https://projelmec.com.br)
