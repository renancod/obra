# Boletim Diário de Produção — Size Engenharia

App para o encarregado preencher o boletim direto no celular, salvando automaticamente numa Planilha Google (Drive). No computador, o mesmo app abre em modo **Visualizar** (somente leitura) para acompanhar o que foi lançado no dia.

## Arquivos desta pasta

- `index.html` — o aplicativo (funciona tanto no celular quanto no computador).
- `manifest.json`, `service-worker.js`, `icon-192.png`, `icon-512.png`, `icon-180.png` — permitem instalar o app na tela inicial do celular como um aplicativo de verdade.
- `logo.png` — logo oficial da Size Engenharia, já recortado com fundo transparente.
- `Code.gs` — o backend que grava e lê os dados na Planilha Google. **Não vai para o site**, é colado no Google Apps Script (passo a passo abaixo).

## Parte 1 — Criar a planilha e o backend (uma única vez)

1. Acesse **sheets.google.com** e crie uma planilha nova. Dê um nome, por exemplo *"Boletins — Size Engenharia"*.
2. No menu, vá em **Extensões → Apps Script**.
3. Apague todo o conteúdo do editor (`Code.gs` que abre em branco) e cole o conteúdo do arquivo `Code.gs` desta pasta.
4. Clique no ícone de salvar (💾) e dê um nome ao projeto, por exemplo *"Backend Boletim"*.
5. Clique em **Implantar → Nova implantação**.
   - Em "Selecionar tipo", escolha **App da Web**.
   - **Executar como:** você mesmo (sua conta Google/Drive).
   - **Quem pode acessar:** *Qualquer pessoa*.
6. Clique em **Implantar**. O Google vai pedir para autorizar o script a acessar sua planilha — aceite (é o script que você mesmo colou, é seguro).
7. Copie a **URL do app da Web** gerada (termina em `/exec`). É essa URL que o aplicativo vai usar para salvar e ler os dados.

> Sempre que você editar o `Code.gs` depois, é preciso ir em **Implantar → Gerenciar implantações → editar (ícone de lápis) → Nova versão → Implantar** para as mudanças valerem no link já publicado.

## Parte 2 — Publicar o app (index.html)

O jeito mais simples é o mesmo que você já usa no app de diário de obra: arrastar a pasta para o **Netlify** (netlify.com → arrastar a pasta na área de deploy). Isso gera um link fixo (ex: `algumnome.netlify.app`) que funciona tanto no celular quanto no computador.

O logo oficial da Size Engenharia já vem incluso (`logo.png`) — não precisa fazer nada, ele aparece automaticamente no cabeçalho.

## Parte 3 — Configurar em cada aparelho (uma única vez por aparelho)

1. Abra o link do app.
2. Na tela inicial, cole a **URL do App da Web** (a do passo 7 da Parte 1) e toque em **Salvar e continuar**.
3. No topo, escolha o modo:
   - **Editar em campo** — para o celular do encarregado (preenche e envia o boletim do dia).
   - **Visualizar** — para o seu computador (mostra tudo o que já foi enviado, sem poder editar).
4. (Opcional) No ícone de engrenagem, você pode ajustar o Empreendimento e o Encarregado padrão, para não precisar digitar todo dia.
5. No celular, pelo navegador (Chrome/Safari), use "Adicionar à tela inicial" para o app abrir como se fosse um aplicativo normal.

## Como funciona no dia a dia

- O encarregado preenche o boletim do dia e toca em **Enviar boletim** — os dados vão direto para a aba "Boletins" da planilha no Drive.
- Se estiver sem internet no momento do envio, o app guarda o boletim no próprio celular e mostra um aviso; assim que a conexão voltar, basta tocar em "tentar enviar" (ou ele tenta sozinho ao reconectar).
- No seu computador, em modo Visualizar, toque em "Atualizar" para ver os boletins mais recentes, e use a busca para filtrar por empreendimento, encarregado ou data.
- Cada item de "Produção do dia" e "Materiais utilizados" vira uma linha na planilha (colunas "Tipo", "Local/Trecho ou Material", "Serviço Executado", "Quantidade", "Unidade"), então dá para montar filtros e tabelas dinâmicas na própria planilha depois.

## Liberar para outros encarregados

Para qualquer outro encarregado usar, é só mandar o mesmo link do app e a mesma URL do App da Web — todos escrevem na mesma planilha, cada um identificado pelo campo "Encarregado" e "Preenchido por".

## Confirmação do empreiteiro (link remoto)

Depois que o encarregado envia um boletim, aparece uma tela com o botão **"Compartilhar com o empreiteiro"** — ele abre o WhatsApp (ou o menu de compartilhar do celular) já com um link único daquele boletim. O encarregado escolhe o contato do empreiteiro e manda.

Quando o empreiteiro abre esse link (de qualquer celular, não precisa ter o app configurado antes), ele vê um resumo do boletim e um botão **"Confirmo que está correto"**. Ao confirmar, a planilha registra a data/hora numa aba nova chamada **Confirmacoes**, e o boletim passa a aparecer com "✓ Confirmado" no modo Visualizar.

Isso é só uma confirmação de link (qualquer um com o link consegue confirmar) — não é uma senha nem uma verificação de identidade forte. Serve para deixar registrado que o empreiteiro viu e concordou com aquele lançamento.

**Importante:** se você já tinha implantado o `Code.gs` antes, precisa atualizar: cole o novo conteúdo no Apps Script e vá em **Implantar → Gerenciar implantações → editar (lápis) → Nova versão → Implantar**. A URL continua a mesma, não precisa reconfigurar o app.

## Atualização: Plano de Ação e Painel de progresso

Essa versão muda a forma de lançar a execução: em vez de digitar local/serviço livremente, o encarregado agora **escolhe uma etapa já cadastrada** e diz quanto foi executado hoje daquela etapa. Isso permite acompanhar o progresso (%) de cada frente de trabalho.

**Como usar:**
1. Cadastre as etapas antes, na aba **"Plano de Ação"** do app (empreendimento, nome da etapa, quantidade planejada/base e unidade — ex: 150 m de rede cloacal). Isso pode ser feito pelo computador ou pelo celular, por quem for montar o planejamento.
2. No dia a dia, o encarregado usa a aba **"Editar em campo"**: em vez de digitar, ele seleciona a etapa numa lista e informa quantos metros/unidades executou naquele dia. O progresso da etapa é calculado automaticamente (executado acumulado ÷ planejado).
3. Materiais continuam sendo digitados livremente, com a opção de vincular a uma etapa (para aparecer no painel "material gasto hoje" daquela frente).
4. A aba **"Painel"** mostra o progresso de cada etapa com uma barra que vai de vermelho (início) a verde (concluído), quem executou hoje e quanto material foi usado — além da lista de boletins enviados.
5. Quando uma etapa termina, use o botão **"Encerrar etapa"** (na aba Plano de Ação) para marcá-la como Concluída — isso é o momento de conferir se o total executado bateu com o planejado no projeto.

Uma nova aba **"Plano de Ação"** é criada automaticamente na planilha na primeira vez que você cadastrar uma etapa — não precisa criar nada manualmente lá.

**Atenção se você já tinha lançado boletins de teste antes desta atualização:** a estrutura da aba "Boletins" ganhou uma nova coluna ("ID Etapa"). Como o app só cria o cabeçalho novo se a aba ainda não existir, se você já tem a aba "Boletins" com dados antigos, é melhor apagar essas linhas de teste (Parte anterior deste guia explica como) antes de continuar usando — assim tudo fica alinhado com o novo formato, sem misturar dado antigo com o novo layout de colunas.
