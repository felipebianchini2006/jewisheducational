# Trocar a página inicial pelo link do Canva

Em **Admin → Settings → Canva home → Canva design link**, cole o link público do design e clique em **Save settings**. Links `canva.link` são resolvidos pelo backend para a URL pública do design. Ao abrir novamente o site, a página inicial exibe esse Canva.

Edite os textos, as imagens e os links dos botões no Canva. Salve e publique o design quando aplicável. O botão **Browse materials** continua abrindo a biblioteca do site, com busca e downloads. Links de botões criados no Canva também podem apontar para `/library` ou para uma categoria usando a URL completa do site.

Apagar o campo e salvar restaura a home do editor que já está guardada no banco. Esse campo continua vazio depois de recarregar Settings. O link histórico de referência não o preenche novamente.

## Correção de 13/10/2026

Na versão `88748b6`, o campo visível passou a gravar `canva_design_reference_url` quando o modo era `native`. A home continuava lendo `home_canva_url`, que ficava vazio. Por isso, a cliente salvava outro link e o design da página inicial não mudava.

O campo voltou a gravar `home_canva_url`. O payload de Settings usa `canva_integration_mode=canva` com link preenchido e `native` com o campo vazio. O layout considera o link ativo da home. A biblioteca, o tema, os materiais, a busca, os limites e as configurações de download são preservados.

O link mais recente da cliente foi mantido: `https://canva.link/zoihr88052powpk`, resolvido como `https://www.canva.com/design/DAHX5KhZ_is/Xfy2_LyMo0UPQFgGSdaGcA/view`. A referência histórica e os blocos da home nativa permanecem guardados.

## Publicação e testes

A preparação usa `scripts/restore-admin-settings.js` para acrescentar somente o módulo Settings corrigido. Se o HTML já possui um import map, o utilitário atualiza apenas seu destino de Settings e preserva as outras entradas. Mapas inválidos, múltiplos ou com uma regra de Settings em `scopes` são recusados.

Os arquivos atuais do Hosting e a configuração da API são preservados. No arquivo fonte da API, somente `generated/index.html` é atualizado para o mesmo HTML do Hosting. Antes da ativação, compare os valores atuais e os horários de atualização das configurações, incluindo a referência da cliente, para não sobrescrever edições feitas durante a publicação.

O gate de release passou com build e 285 testes: 107 backend, 170 frontend e 8 utilitários. A regressão executa o setup real do componente Settings, edita o campo e verifica o payload enviado à API. Também cobre campo vazio e erro de URL inválida.

No navegador local, foram salvos dois designs diferentes, incluindo um link curto. A home carregou o iframe correspondente a cada um. Apagar e salvar restaurou a home guardada; recarregar Settings manteve o campo vazio. A busca por `Alphabet` retornou dois materiais. Um PDF de teste foi baixado pela interface com uma conta local; seus bytes foram iguais ao arquivo servido pelo backend atual. Esses testes não escrevem em produção.

Publicação confirmada: Hosting `a249c493ed74f9ef`, API `api-00087-zif`, revisão do editor `26`. Foram preservados os 113 arquivos anteriores fora do HTML e 139 configurações não relacionadas à ativação. O ambiente e os secrets da API permaneceram iguais. Em produção, foram conferidos o Canva da cliente, a navegação entre páginas do design, o acesso à biblioteca, a busca, o documento e o pedido de login para baixar. A home e os filtros da biblioteca foram verificados em 390×844, sem corte lateral ou erros relevantes no console.

Quem já estiver com o painel aberto deve recarregar a página uma vez para receber a tela Settings corrigida. O registro reversível e os prints estão em `/Users/felipebianchini/Developer/artifacts/jewisheducational-canva-link-2026-10-13/`.

## Navegação dentro do Canva — publicação pendente

O código de `CanvaHome.vue` foi preparado para mostrar somente o design, sem a barra branca externa. A remoção depende de botões de biblioteca e login no Canva, com estas URLs:

- Biblioteca: `https://jewisheducationalresources.org/library`
- Login: `https://jewisheducationalresources.org/login`

Em 13/10/2026, o link disponível abriu em modo de visualização e a edição ficou desabilitada. O usuário confirmou que possui somente o link público. A mudança permanece sem publicação até que esses botões sejam incluídos e seus destinos sejam testados. A versão publicada continua com a navegação externa.

A preparação passou no build e em 17 testes de Settings, URLs do Canva, rotas e Analytics. Uma prévia local compilou o componente real com o design atual: o iframe iniciou na posição vertical zero e ocupou a altura inteira em 1280×720 e 390×844, sem rolagem horizontal nem erros no console. A navegação do Canva avançou para a página 2 e retornou à página 1. Essa validação não inclui a criação dos botões no design.

## Relatório por documento no Google Analytics — configuração pendente

O Firebase confirmou a propriedade `541487710`, chamada `jewish-educational-resources`, e o fluxo `15065400088`, com Measurement ID `G-HHPLFCMXZC`. O acesso ao Firebase não permitiu configurar o Analytics: as contas conferidas no Brave abriram a tela inicial de criação de conta, e o token da CLI foi recusado pela API de Analytics por falta de escopo.

Na conta com acesso de editor à propriedade, cadastrar dimensões de escopo Evento para `resourceTitle` (Documento) e `fileLabel` (Arquivo), se não existirem. Depois, criar uma exploração em formato livre com Documento e Arquivo nas linhas, Nome do evento nas colunas e Contagem de eventos nos valores. Filtrar os eventos `resource_preview_open` e `download_completed` e salvar como **Visualizações e downloads por documento**.

`resource_preview_open` mede a abertura da prévia. `download_completed` é enviado depois que o endpoint devolve o arquivo completo e o navegador recebe a ação de download. `download_page_open` conta somente a abertura da tela de download. O código publicado utiliza os parâmetros em camelCase nesses eventos; `file_download` é um evento separado, com parâmetros em snake_case. Não somar os dois eventos de download como se fossem entregas diferentes.

As dimensões novas podem levar de 24 a 48 horas para aparecer nos relatórios. A tabela da conta não foi criada nesta etapa por falta de acesso à propriedade.
