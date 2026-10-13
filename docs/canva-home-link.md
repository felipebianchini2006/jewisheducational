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
