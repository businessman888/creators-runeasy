## RUNEASY

## Creator Platform Product & Technical Blueprint

Como construir um sistema proprio de creators, embaixadores e afiliados com tracking, atribuicao, comissoes, pagamentos, ranking, conteudo e impacto real na corrida.

## Tese central

A plataforma deve transformar creator marketing em um canal mensuravel e recorrente: cada creator sabe quantas pessoas colocou em movimento, quanto gerou para a RunEasy, quanto ganhou, o que precisa desbloquear e o que deve criar a seguir.

Versao 1.0 - 11 de agosto de 2026

Baseado nas premissas atuais informadas: assinatura media de R\$ 24,90; marketing hoje majoritariamente organico; custos fixos muito baixos; custo de tokens de ate aproximadamente R\$ 4 por assinatura. Valores economicos neste documento sao hipoteses para teste, nao regras finais.


## Mapa do documento

- 1. Resposta curta: a plataforma e tecnicamente viavel

- 2. Visao do produto e principios de design

- 3. O que pode ser automatizado e onde existem limitacoes

- 4. Arquitetura recomendada e stack

- 5. Login, perfis, permissoes e onboarding do creator

- 6. Tracking de Instagram, TikTok e YouTube

- 7. Registro de conteudo e sincronizacao de metricas

- 8. Links, codigos e atribuicao de usuarios

- 9. RevenueCat, Superwall e eventos de assinatura

- 10. Motor de comissoes e modelo economico recomendado

- 11. Ledger, saldo pendente, disponivel e reembolsos

- 12. Saques e pagamentos via Pix

- 13. Dashboard do creator e linguagem de impacto

- 14. Ranking, niveis, conquistas e missoes

- 15. Biblioteca de ideias e projetos privados

- 16. Backoffice do time RunEasy

- 17. Modelo de dados

- 18. Webhooks, jobs, sincronizacao e observabilidade

- 19. Fraude, seguranca, privacidade e LGPD

- 20. Roadmap MVP -> escala

- 21. Backlog de implementacao

- 22. KPIs para validar o canal

- 23. Decisoes que ainda precisam ser tomadas

- 24. Fontes oficiais e notas tecnicas

- 25. Plano pratico dos primeiros 30 dias

## Recomendacao de lancamento

Nao espere Instagram/TikTok APIs, Pix automatico e gamificacao completa para colocar o programa no ar. Lance primeiro o loop que gera dinheiro: login -> link/codigo -> atribuicao -> compra -> comissao -> dashboard -> pagamento. Depois adicione social analytics e automacao.


## 1. Resposta curta: a plataforma e tecnicamente viavel

Sim. Quase tudo que voce descreveu pode ser automatizado. O ponto importante e separar o que a RunEasy controla diretamente do que depende de autorizacao das redes sociais.

| Feature | Automatico? | Fonte / API | Observacao |
| --- | --- | --- | --- |
| Login e perfil do creator | Sim | Supabase Auth + Postgres | Totalmente controlado pela |
|   |   |   | RunEasy. |
| Cliques em link individual | Sim | Redirect proprio / short link | 100% deterministico no clique. |
| Codigo do creator | Sim | Banco da RunEasy + app | Excelente fallback de atribuicao. |
| Conversoes, renovacoes e | Sim | RevenueCat Webhooks | Fonte principal para dinheiro. |
| refunds |   |   |   |
| Comissao e saldo | Sim | Motor proprio + ledger | Nao deixar calculo no frontend. |
| Saque via Pix | Sim | Asaas Transfers API ou payout | Pagamento e reconciliacao |
|   |   | manual no MVP | separados da App Store. |
| Instagram Reels/posts - views e | Sim, com condicoes | Instagram API + Insights | Creator precisa conectar conta |
| engagement |   |   | profissional e conceder |
|   |   |   | permissoes. |
| Instagram Stories - insights | Sim, com condicoes | Instagram Media Insights | Capturar media IDs e |
|   |   |   | sincronizar; contas pessoais nao |
|   |   |   | entram. |
| TikTok videos - | Sim, com autorizacao | TikTok Display API / video.list | Creator autoriza a RunEasy; API |
| views/likes/comments/shares |   |   | retorna view_count e outros |
|   |   |   | campos. |
| YouTube views | Sim | YouTube Data API; Analytics API | Public stats sao simples; |
|   |   | para dados profundos | analytics detalhado requer |
|   |   |   | OAuth do canal. |
| Ranking, levels, achievements | Sim | Banco proprio | Derivados das metricas internas. |
| Ideias, roteiros e projetos | Sim | CMS proprio | Uma das features mais simples. |
| privados |   |   |   |

## Onde nao existe perfeicao

Nenhuma plataforma social deve ser tratada como banco de dados infalivel. Tokens expiram, permissoes mudam, APIs tem rate limits e contas podem nao ser elegiveis. Por isso a plataforma precisa de reautenticacao, jobs de retry e fallback manual para comprovacao de Story/conteudo. O dinheiro, por outro lado, deve vir do RevenueCat e do proprio banco da RunEasy.

## 2. Visao do produto e principios de design

O produto nao deve parecer um painel de afiliados. Deve parecer um sistema operacional do creator RunEasy.

- Impacto antes de vendas: a home deve dizer "Voce ajudou 78 pessoas a comecar a correr este mes", nao "78 conversoes".

- Dinheiro transparente: o creator enxerga exatamente a base de calculo, a porcentagem do nivel e o que esta pendente/disponivel.


- Progressao visivel: Creator -> Partner -> Ambassador -> Elite, com requisitos objetivos.

- Feedback rapido: cada conteudo deve mostrar views, clicks, trials, assinaturas e receita atribuida quando possivel.

- Aprendizado coletivo: winners e insights viram briefs para toda a rede; briefs privados ficam em projetos pessoais.

- Nao pagar por vanity metrics: views servem para diagnostico e ranking criativo; comissao nasce de receita validada.

- Distribuicao como recompensa: collabs, reposts e destaque no perfil oficial podem ser rewards importantes para microcreators.

## 3. O que pode ser automatizado e onde existem limitacoes

A regra de arquitetura e simples: use APIs oficiais para observar redes sociais, mas mantenha atribuicao, dinheiro, regras e estado do programa dentro da infraestrutura da RunEasy.

## Instagram

A Instagram API atual permite acesso a dados de contas profissionais (Business e Creator). A API de Insights retorna dados de midia de contas profissionais, incluindo metricas de performance. Para usar Insights com Instagram Login, a RunEasy precisa solicitar a permissao de insights e passar pelo processo de acesso/revisao apropriado. Contas pessoais nao sao suportadas para esse fluxo [S1][S2].

## TikTok

O TikTok Display API permite consultar videos do usuario autorizado. O endpoint de Query Videos aceita o scope video.list e pode retornar view_count, like_count, comment_count e share_count, alem de dados basicos do video [S3]. Isso e suficiente para um painel de performance organica de videos publicados pelo creator.

## YouTube

O YouTube Data API fornece estatisticas publicas de videos, incluindo view count. Para analytics mais profundos do proprio canal, o YouTube Analytics API exige autorizacao do dono do canal [S10].

## RevenueCat

RevenueCat e a fonte mais importante para o motor financeiro. Customer Attributes podem guardar fatos arbitrarios - inclusive o deep link de instalacao - e aparecem server-side em webhooks/REST. Webhooks enviam eventos como INITIAL_PURCHASE, RENEWAL, CANCELLATION e EXPIRATION, com preco, moeda e estimativas de taxa/commission da loja [S4][S5][S6].

## 4. Arquitetura recomendada e stack

Stack recomendada para construir rapido, com baixo custo inicial e boa seguranca:

| Camada | Tecnologia | Papel |
| --- | --- | --- |
| Frontend creator/admin | Next.js + TypeScript | Dashboard web responsivo; pode virar |
|   |   | PWA. |
| Auth + Database | Supabase Auth + Postgres | Login, roles, RLS, storage e cron/queues |
|   |   | na mesma base [S13]. |
| Backend server-side | Next.js Route Handlers ou Supabase Edge | Webhooks, APIs sociais, payout, calculos e |
|   | Functions | secrets. |


| Deploy | Vercel + Supabase | Baixa friccao operacional. |
| --- | --- | --- |
| Subscriptions | RevenueCat | Eventos financeiros e status de |
|   |   | assinatura. |
| Paywalls | Superwall | Experimentos, paywalls e targeting; nao |
|   |   | usar como ledger financeiro. |
| Install attribution | MVP: codigo + link proprio; Scale: | RevenueCat nao e attribution network; |
|   | AppsFlyer OneLink | MMP aumenta acuracia [S7][S8]. |
| Social metrics | Meta Instagram API + TikTok Display API | OAuth por creator. |
|   | + YouTube APIs |   |
| Payout Brazil | MVP manual Pix; Scale: Asaas Transfers | Asaas permite transferir para chave Pix |
|   | API | por API [S11]. |
| Observabilidade | Sentry + logs estruturados + audit_log | Erros de webhook/API precisam ser |
|   |   | rastreaveis. |

## Fluxo principal do sistema

- 1. Creator entra por convite, cria login e conecta Instagram/TikTok quando elegivel.

- 2. Plataforma gera slug, link e codigo exclusivos.

- 3. Usuario clica em runeasy.app/c/<creator>; o backend grava click_id e creator_id e redireciona.

- 4. Se houver AppsFlyer/OneLink, o creator_id atravessa o fluxo de instalacao. Sem MMP, o codigo do creator funciona como fallback deterministico.

- 5. Na primeira sessao valida, a RunEasy grava creator_id no usuario e envia o atributo para RevenueCat. A atribuicao fica bloqueada conforme a regra do programa.

- 6. RevenueCat envia purchase/renewal/refund para /api/webhooks/revenuecat.

- 7. O backend calcula proceeds, aplica o tier do creator e cria um lancamento no ledger.

- 8. Eventos do app (primeiro treino, treinos, km) alimentam metricas de impacto agregadas.

- 9. Jobs de social sync atualizam views/engagement dos conteudos vinculados.

- 10. Quando a comissao fica disponivel, o creator solicita payout; o backend aprova e dispara Pix via Asaas ou processo manual.

## 5. Login, perfis, permissoes e onboarding do creator

Use um unico sistema de identidades, com roles e politicas de acesso. Supabase Auth suporta email/password, magic link e OAuth; o Postgres pode aplicar Row Level Security para cada creator enxergar somente os proprios dados [S13].

- Role creator: ve apenas seus dados, conteudos globais e projetos privados enderecados a ele.

- Role marketing_manager: cria briefs, projetos, missoes e ve performance; nao altera pagamentos liquidados.

- Role finance: aprova payout e ajustes financeiros.

- Role admin: configura tiers, regras, integracoes e usuarios.

- 2FA recomendado para admin/finance; secrets e tokens sociais apenas server-side.


## Onboarding sugerido do creator:

- 1. Convite personalizado -> criar conta.

- 2. Aceitar termos do Creator Program e regras de atribuicao/comissao.

- 3. Preencher nome, CPF/CNPJ ou dados necessarios ao pagamento, pais e chave Pix quando aplicavel.

- 4. Conectar Instagram/TikTok opcionalmente; explicar por que as permissoes sao usadas.

- 5. Gerar codigo e link.

- 6. Fazer tour curto: Impacto -> Ganhos -> Conteudos -> Niveis.

- 7. Entrar automaticamente no Creator Sprint.

## 6. Tracking de Instagram, TikTok e YouTube

## Instagram - implementacao

- Criar app Meta e integrar Instagram API with Instagram Login.

- Solicitar permissao para acesso basico e insights; advanced access/review pode ser necessario para uso com creators externos [S2].

- Armazenar access token criptografado no backend e data de expiracao.

- Importar lista de midias do creator e permitir que ele associe cada post/Reel/Story a um projeto RunEasy.

- Para cada media_id, sincronizar views/reach/likes/comments/shares/saves/total interactions conforme o tipo e a disponibilidade da API [S1].

- Para Stories, capturar os IDs enquanto o Story esta ativo e sincronizar varias vezes durante a janela. A Meta informa que dados de insights podem ser armazenados por ate 2 anos, mas a RunEasy deve persistir snapshots proprios para evitar depender de recuperacao tardia [S1].

- Fallback: upload de screenshot de Insights quando a conta nao for profissional, a API falhar ou a permissao ainda estiver em review.

## TikTok - implementacao

- Login Kit/OAuth do TikTok para cada creator.

- Scope video.list; listar/consultar videos autorizados.

- Salvar id, share_url, create_time e snapshots de view_count, like_count, comment_count e share_count [S3].

- Creator seleciona o video que pertence a uma missao/projeto; nao tente adivinhar automaticamente no MVP.

- Atualizar metricas em 1h, 6h, 24h, 72h, 7d e depois diariamente enquanto houver relevancia.

## YouTube - opcional na fase 2/3

- Para Shorts/public videos, o Data API fornece estatisticas publicas de video [S10].

- Para watch time, retencao e dados do canal, pedir OAuth e usar YouTube Analytics API [S10].

## Nao use scraping como fundacao

Scraping de Instagram/TikTok pode quebrar, violar termos e exigir manutencao constante. APIs oficiais + fallback manual e a arquitetura correta para uma plataforma que pretende pagar dinheiro com base nos dados.


## 7. Registro de conteudo e sincronizacao de metricas

A plataforma precisa saber quais posts pertencem a RunEasy. A forma mais confiavel e o creator registrar/importar o conteudo no projeto depois de publicar.

- 1. Creator abre a missao/projeto e clica em "Vincular conteudo".

- 2. Se a rede esta conectada, a plataforma mostra os posts recentes via API.

- 3. Creator seleciona o Reel/TikTok/Story correto; alternativamente cola a URL.

- 4. O backend cria content_post com platform_media_id e campaign_id.

- 5. Worker agenda snapshots de metricas.

- 6. O dashboard exibe delta de views, engagement e - principalmente - clicks/conversions atribuidos.

| Metrica | Fonte | Uso |
| --- | --- | --- |
| Views | API social | Diagnostico de alcance; nao pagar |
|   |   | comissao por ela. |
| Likes/comments/shares/saves | API social | Sinal de qualidade/engagement. |
| Clicks | Redirect RunEasy | Deterministico; excelente para CTR. |
| Installs | MMP ou aproximacao | AppsFlyer/OneLink melhora atribuicao; |
|   |   | codigo pode ser fallback. |
| Trials | RevenueCat + app | Evento de assinatura/trial. |
| Paid subscribers | RevenueCat | Metric principal do canal. |
| Revenue/proceeds | RevenueCat webhook | Base do motor financeiro. |
| Activated runners | Banco RunEasy | Usuario atribuido que completa primeiro |
|   |   | treino. |
| KM/treinos agregados | Banco RunEasy | Impacto sem expor dados pessoais. |

## 8. Links, codigos e atribuicao de usuarios

Nao dependa apenas de links da App Store. A RunEasy deve possuir seus proprios links curtos, por exemplo runeasy.app/c/ana, para registrar todos os cliques antes do redirecionamento.

## Regra recomendada de atribuicao:

- 1. Codigo do creator explicitamente inserido pelo usuario tem prioridade maxima, pois e uma declaracao direta.

- 2. Sem codigo, usar o ultimo creator link elegivel dentro de uma janela de 30 dias antes da primeira assinatura paga.

- 3. No primeiro pagamento, bloquear a atribuicao daquela assinatura ao creator por 12 meses (ou a duracao definida pelo programa).

- 4. Nao permitir troca de creator em renovacoes, salvo ajuste manual auditado.

- 5. Refunds e chargebacks ajustam o ledger do mesmo creator.


## MVP sem MMP

O redirect registra click_id + creator_id. Se o app ja estiver instalado, Universal/App Link pode levar o creator_id para dentro do app. Se o usuario instalar pela loja, o parametro pode se perder. Por isso o MVP deve mostrar um campo discreto "Codigo do creator" no onboarding/checkout e salvar creator_id no usuario. Isso ja permite atribuicao financeira muito confiavel para quem usa o codigo.

## Escala com AppsFlyer

Quando volume justificar, use OneLink para deep linking/deferred deep linking. A documentacao do AppsFlyer

descreve o fluxo para novos e existentes usuarios; RevenueCat possui integracao oficial com AppsFlyer. RevenueCat deixa claro que ele proprio nao e uma attribution network e nao popula automaticamente os dados

de campanha [S7][S8].

## 9. RevenueCat, Superwall e eventos de assinatura

## RevenueCat: fonte de verdade financeira

- No app, ao resolver o creator_id, salvar como atributo customizado do customer, por exemplo creator_id="crt_123" e creator_code="ANA". RevenueCat permite custom attributes e os expoe server-side [S4].

- Configurar webhook HTTPS exclusivo da RunEasy com secret e idempotency.

- Processar INITIAL_PURCHASE, RENEWAL, CANCELLATION/refund, EXPIRATION e billing issues conforme necessario [S5].

- Usar event.id como chave unica para impedir comissao duplicada.

- Depois de receber webhook, opcionalmente buscar o subscriber pelo REST API para reconciliar estado, como recomendado pela propria RevenueCat [S5].

## Superwall: camada de experiencia e oferta

Superwall deve continuar cuidando de paywalls, experiments e targeting. Ele suporta user attributes e integracao com RevenueCat [S9]. O creator_id pode ser repassado tambem como user attribute para testar paywalls/offerings especificos por origem, mas a comissao nao deve ser calculada no Superwall.

## Principio de dados

RevenueCat diz "quanto dinheiro e qual evento". O banco RunEasy diz "de qual creator, qual regra, qual saldo e qual payout". Superwall diz "qual experiencia/paywall foi mostrada".

## 10. Motor de comissoes e modelo economico recomendado

Eu nao recomendo calcular a comissao como porcentagem do lucro liquido final da empresa. Isso deixa o creator dependente de custos que ele nao controla e torna o valor pequeno e dificil de auditar. O creator e o canal de marketing; sua remuneracao deve sair da economia de aquisicao.

## Base recomendada: Proceeds da assinatura

RevenueCat fornece price_in_purchased_currency, tax_percentage e commission_percentage. A propria documentacao sugere calcular proceeds como: preco x (1 - tax_percentage - commission_percentage) [S6].


## Formula recomendada

Creator commission = Proceeds da transacao x percentual do tier.

Proceeds = preco pago - estimativa de impostos da loja - comissao da loja.

A RunEasy absorve tokens, infra fixa, contabilidade e despesas gerais. Esses custos servem para decidir se o tier e sustentavel, mas nao precisam aparecer como desconto na base do creator.

## Por que isso e melhor:

- O creator entende a regra sem conhecer o P&L interno da RunEasy.

- O valor por conversao fica mais motivador.

- A comissao substitui parte do CAC/marketing pago, entao nao faz sentido descontar "marketing" antes de pagar o proprio canal.

- O motor fica calculavel por transacao e por renovacao.

- Custos fixos baixos da RunEasy aumentam a capacidade de ser agressiva na parceria.

## Hipotese inicial de tiers

| Nivel | % dos proceeds | Logica |
| --- | --- | --- |
| Creator Sprint | 25% | Entrada; ganha desde a primeira |
|   |   | assinatura. |
| Partner | 30% | Ex.: desbloqueia apos 10 novos |
|   |   | corredores pagos/ativados. |
| Ambassador | 35% | Ex.: 50 novos corredores ou meta |
|   |   | mensal consistente. |
| Elite | 40% | Creator comprovadamente lucrativo e |
|   |   | consistente. |

## Exemplo didatico com assinatura de R\$ 24,90

A tabela abaixo usa apenas um cenario ilustrativo em que impostos + comissao da loja somam 25%. Na producao, nao hardcode 25%: use os percentuais estimados que vierem do RevenueCat por transacao.

| Cenario | Valor creator | Observacao |
| --- | --- | --- |
| Proceeds estimados | R$ 18,68 | R$ 24,90 x 75% |
| Creator 25% | R$ 4,67 | RunEasy fica com R$ 14,01 antes de |
|   |   | tokens/overhead |
| Partner 30% | R$ 5,60 | RunEasy fica com R$ 13,07 antes de |
|   |   | tokens/overhead |
| Ambassador 35% | R$ 6,54 | RunEasy fica com R$ 12,14 antes de |
|   |   | tokens/overhead |
| Elite 40% | R$ 7,47 | RunEasy fica com R$ 11,21 antes de |
|   |   | tokens/overhead |


Se o custo variavel de AI/tokens for R\$ 4 por assinante e o creator estiver em 40%, ainda restariam aproximadamente R\$ 7,21 por transacao neste cenario antes de custos fixos. Como os custos fixos informados hoje sao baixos, a estrutura merece ser testada com dados reais de retention/LTV, nao descartada por uma margem projetada agregada.

## Opcao de acelerador

Para tornar o primeiro mes mais excitante sem elevar a taxa permanente, voce pode adicionar um bonus de primeira conversao ou milestone. Exemplo: tier normal 25%, mas ao chegar a 10 novos assinantes no Sprint recebe um bonus fixo; depois sobe para 30%. Assim a RunEasy compra motivacao pontual sem comprometer todo o LTV.

## 11. Ledger, saldo pendente, disponivel e reembolsos

Nao implemente "saldo" como um numero mutavel em creators.balance. Implemente um ledger imutavel de lancamentos. O saldo exibido e a soma dos lancamentos.

| Tipo | Sinal | Uso |
| --- | --- | --- |
| commission_accrual | + R$ 5,60 | Nova compra/renovacao |
| refund_adjustment | - R$ 5,60 | Refund da transacao |
| manual_bonus | + R$ 100,00 | Bonus aprovado pela RunEasy |
| payout | - R$ 350,00 | Valor pago ao creator |
| manual_adjustment | +/- | Somente admin/finance, com motivo e |
|   |   | audit log |

## Estados recomendados:

- Pending: comissao registrada, ainda dentro da janela operacional/reconciliacao.

- Available: elegivel a pagamento.

- Paid: lancamentos incluidos em payout concluido.

- Adjusted: refund/estorno/ajuste posterior.

Politica simples para MVP: fechar o mes calendario, reconciliar refunds no inicio do mes seguinte e pagar em um dia fixo (por exemplo dia 10). O botao "Sacar" pode existir, mas eu sugiro inicialmente permitir saque apenas quando available >= R\$ 50 ou R\$ 100. Isso reduz custo operacional e fraude. Se um refund acontecer depois do payout, o valor pode virar ajuste negativo no proximo saldo, conforme os termos do programa.

## 12. Saques e pagamentos via Pix

Ponto importante: a assinatura e processada pelas lojas (Apple/Google), nao pelo creator. Portanto nao existe um "split" automatico no momento da compra como em um marketplace de cartao. A RunEasy recebe seus proceeds e depois paga a comissao como uma obrigacao separada.

## MVP recomendado

- Creator cadastra chave Pix e dados fiscais necessarios.

- Dashboard mostra saldo; creator solicita payout.

- Finance aprova; RunEasy faz Pix manual e marca payout como pago.


- O sistema guarda comprovante/reference e audit trail.

## Automacao recomendada: Asaas

A API do Asaas permite criar transferencias para conta externa ou diretamente para chave Pix, com externalReference para reconciliar o payout com o seu sistema [S11]. Isso combina melhor com a realidade da RunEasy do que tentar usar split de checkout, pois a origem do dinheiro e App Store/Google Play.

- 1. Creator pede saque -> payout_request CREATED.

- 2. Backend valida saldo, minimo, KYC e ausencia de bloqueios.

- 3. Finance aprova (ou autoaprova abaixo de limite apos maturidade).

- 4. Backend chama Asaas /v3/transfers com pixAddressKey, value e externalReference.

- 5. Sistema acompanha status; DONE -> cria lancamento payout no ledger e marca request PAID.

- 6. Falha -> retorna para REVIEW sem reduzir saldo definitivamente.

## Stripe Connect como alternativa

Stripe Connect e excelente para plataformas que processam pagamentos e repassam saldo a contas conectadas, mas para este caso e menos natural porque o pagamento principal nasce nas lojas. Alem disso, payouts no Brasil possuem particularidades; a documentacao da Stripe indica que payouts de contas brasileiras sao automaticos/diarios em varios fluxos [S12]. Eu usaria Asaas/Pix para o primeiro desenho brasileiro e deixaria Connect para uma expansao internacional ou web billing.

## Cuidado regulatorio

No produto, prefira os termos "comissoes a receber", "saldo de comissoes" e "solicitar pagamento" em vez de vender a feature como uma carteira financeira. A RunEasy deve validar com contador/juridico as obrigacoes de cadastro, documento fiscal, retencoes e pagamentos para PF/PJ antes de automatizar grandes volumes.

## 13. Dashboard do creator e linguagem de impacto

## Home - cards prioritarios

| Card | Exemplo |
| --- | --- |
| Impacto principal | Voce ajudou 78 pessoas a comecar a correr este mes. |
| Ativacao | 51 delas ja completaram o primeiro treino. |
| Comunidade | Seus corredores completaram 1.240 km este mes. |
| Ganhos | R$ 384,20 disponiveis / R$ 126,40 pendentes. |
| Progressao | Faltam 4 novos corredores para desbloquear Partner (30%). |
| Ranking | #7 entre os creators RunEasy este mes. |


## Definicao tecnica de "novo corredor"

Para a frase ter integridade, eu nao igualaria automaticamente "assinante" a "corredor". Defina Activated Runner como usuario atribuido ao creator que completou o primeiro treino/corrida registrado na RunEasy. Assim "voce ajudou 78 pessoas a comecar a correr" e uma metrica de comportamento real, e nao apenas uma venda.

- New paid subscribers: usuarios pagos atribuidos.

- Activated runners: completaram primeiro treino.

- Active referred runners: fizeram >= 1 treino nos ultimos 30 dias.

- Community kilometers: soma agregada de km dos usuarios atribuidos em uma janela definida.

- Nunca mostrar nome, localizacao ou detalhes individuais do corredor ao creator sem consentimento explicito; preferir agregados.

## Tela Performance por conteudo

Cada post vinculado pode exibir: views -> engagement -> clicks -> trials -> paid -> proceeds -> sua comissao. Isso ensina o creator a produzir o que converte, e nao apenas o que viraliza.

## 14. Ranking, niveis, conquistas e missoes

## Ranking

- Ranking principal por novos corredores pagos/ativados, nao por views.

- Ranking mensal zera para manter o jogo ganhavel.

- All-time leaderboard pode existir como prestigio, sem substituir o mensal.

- Rising Creators: ranking separado para creators com menos de X dias no programa.

- Nao publicar receita/comissao de outros creators sem consentimento.

## Conquistas

- First Runner - primeira pessoa ativada.

- 10 Runner Club / 50 / 100 / 500 / 1000.

- 100 km de impacto / 1.000 km de impacto.

- First Annual Subscriber.

- 4 Weeks of Impact - pelo menos um novo corredor em quatro semanas consecutivas.

- Conversion Winner - creative com maior taxa de click -> paid em uma missao.

## Missoes

Missoes permitem ao marketing coordenar a rede sem mandar DMs individuais. Uma missao tem objetivo, janela, assets, brief, CTA, rewards e criterios. Exemplo: "Primeira meia maratona - publique um conteudo mostrando como voce estruturaria a semana com RunEasy".

## 15. Biblioteca de ideias e projetos privados

## Content Hub global

- Feed diario/semanal de ideias, hooks e formatos.

- Reference URLs/embed de videos inspiracao.

- Scripts/roteiros opcionais, sempre separados de "formato" e "mensagem".

- Tags: beginner, 5k, 10k, half marathon, pace, injury prevention, motivation, treadmill, etc.


- Status: experimental / promising / proven / fatigued.

- Metrica agregada interna: quantos creators testaram, conversao media, observacoes do marketing.

## Projetos pessoais

Cada creator recebe uma area privada em que o marketing pode publicar briefings, ideias, deadlines, assets e feedbacks. Tecnicalmente e a mesma entidade project com visibility="creator" e creator_id preenchido.

| Objeto | Campos chave |
| --- | --- |
| Projeto | Nome, objetivo, status, deadline, creator_id |
| Brief | Hook, mensagem, CTA, feature, restricoes, referencias |
| Assets | Imagens, videos, logos, links |
| Deliverables | Quantidade, plataforma, status, URL publicada |
| Feedback | Thread interna creator <-> marketing |
| Performance | Metricas sincronizadas do conteudo vinculado |

## 16. Backoffice do time RunEasy

- Creators: status, tier, canais conectados, payout/KYC, risk flags.

- Prospecting pipeline: lead -> contacted -> replied -> sprint -> partner -> ambassador.

- Performance: top creators por paid, proceeds, activated runners, retention e ROI.

- Content: publicar ideias globais e privadas; templates e references.

- Missions: criar, segmentar por tier/nicho/plataforma e acompanhar adesao.

- Finance: commission ledger, pending/available, payout queue, refunds, manual adjustments.

- Rules: percentuais por tier, attribution window, payout minimum, hold period.

- Integrations health: RevenueCat webhook status, Meta/TikTok token health, payout failures.

- Audit log: toda alteracao financeira ou de tier com autor/data/motivo.

## 17. Modelo de dados

| Tabela | Campos principais |
| --- | --- |
| creators | id, user_id, slug, code, tier_id, status, joined_at |
| creator_profiles | bio, niche, country, payout_profile, metadata |
| creator_social_accounts | creator_id, platform, platform_user_id, encrypted_token, expires_at, |
|   | status |
| content_posts | creator_id, platform, platform_media_id, url, project_id, |
|   | published_at |
| social_metric_snapshots | content_post_id, captured_at, views, likes, comments, shares, saves, |
|   | reach |
| tracking_links | creator_id, slug, destination, campaign_id |
| click_events | tracking_link_id, click_id, ts, referrer, device metadata minimal |
| user_attributions | app_user_id, creator_id, source, click_id, attributed_at, locked_at |


| subscription_events | revenuecat_event_id, app_user_id, type, price, currency, tax_pct, |
| --- | --- |
|   | store_commission_pct, transaction_id |
| commission_rules | tier_id, percent, starts_at, ends_at |
| commission_ledger | creator_id, event_id, amount, currency, state, type, available_at |
| payout_requests | creator_id, amount, provider, external_reference, status, |
|   | requested_at, paid_at |
| tiers | name, order, commission_pct, unlock_rule_json |
| achievements | code, title, rule_json |
| creator_achievements | creator_id, achievement_id, unlocked_at |
| missions | title, brief, starts_at, ends_at, reward_json, visibility |
| mission_progress | creator_id, mission_id, state, linked_content_id |
| content_library | type, title, body, reference_url, tags, visibility, status |
| creator_projects | creator_id, title, brief, status, due_at |
| workout_impact_daily | creator_id, date, activated_runners, active_runners, workouts, |
|   | kilometers |
| audit_log | actor_user_id, action, entity, before_json, after_json, created_at |

## Regra de seguranca

O creator nunca consulta subscription_events ou commission_ledger de outros creators diretamente. Views de dashboard devem ser protegidas por RLS e/ou endpoints server-side. Secret keys e tokens nunca entram no browser [S13].

## 18. Webhooks, jobs, sincronizacao e observabilidade

| Job/evento | Cadencia | Funcao |
| --- | --- | --- |
| RevenueCat webhook | Tempo real | Compra/renewal/refund -> ledger. |
| Social sync fast | 1h/6h/24h | Conteudo novo; capta aceleracao inicial. |
| Social sync slow | Diario por 7-30d | Conteudo maduro. |
| Story sync | Durante vida do Story | Capturar final metrics e snapshots. |
| Tier evaluator | Diario + event-driven | Promove/demove conforme regra. |
| Impact aggregator | Diario | Atualiza runners/km/treinos por creator. |
| Commission availability | Diario | Move pending -> available conforme |
|   |   | janela. |
| Payout reconciliation | Minutos/horas | Consulta status do provider e resolve |
|   |   | falhas. |
| Token health | Diario | Detecta OAuth expirado/revogado e pede |
|   |   | reconnect. |


- Fila de retry com exponential backoff para Meta/TikTok/Asaas.

- Dead-letter queue para eventos que falharam repetidamente.

- Webhook payload bruto armazenado (com politica de retencao) para auditoria.

- Sentry para exceptions; dashboard interno de integracoes.

- Idempotency em webhooks e payouts.

## 19. Fraude, seguranca, privacidade e LGPD

## Fraude / abuso

- Nao pagar comissao por views. Views podem ser infladas e nao provam receita.

- Self-referral: creator nao recebe comissao de assinatura propria ou contas relacionadas quando detectavel.

- Duplicidade: um subscription transaction_id so gera um lancamento financeiro.

- Coupon leakage: se o codigo der desconto, pode vazar para sites de cupom. Considere codigo de atribuicao sem desconto ou regras de origem.

- Attribution hijacking: nao sobrescrever creator_id depois do primeiro pagamento sem revisao.

- New creator payout hold: primeiros pagamentos podem exigir revisao manual.

- Large payout approval: duas etapas para valores acima de limite.

- Refund/chargeback: lancamento negativo correspondente.

## Seguranca

- Criptografar tokens OAuth de redes sociais; nunca enviar token para frontend.

- Guardar RevenueCat/Asaas/Superwall secrets em ambiente server-side.

- RLS por creator e RBAC para admin/finance/marketing.

- Rate limits em endpoints de login, link redirect e payout.

- Assinatura/secret verification de webhooks quando suportado.

- Audit log obrigatorio para money movements.

## Privacidade

- Creator ve dados agregados dos corredores que trouxe, nao dados pessoais individuais.

- Explicar no onboarding do creator quais dados sociais sao lidos e por que.

- Coletar somente dados necessarios para pagamento/KYC e operacao.

- Definir politica de retencao e exclusao; preparar fluxo de desconectar social accounts.

- Antes de escalar pagamentos PF/PJ, validar LGPD, tributacao e documentacao fiscal com assessoria profissional.

## 20. Roadmap MVP -> escala

## Fase 0 - 3 a 5 dias: prototipo navegavel

- Design de dashboard, creator profile, earnings, projects e ranking com dados mockados.

- Validar a experiencia com 3-5 creators antes de automacao profunda.

## Fase 1 - Money Loop (2 a 3 semanas)

- Supabase Auth + roles.

- Creators, tiers, tracking links/codes.

- RevenueCat attributes + webhook.

- Attribution table + commission ledger.

- Dashboard: paid, proceeds, commission, pending/available.


- Content Hub + projetos privados simples.

- Payout manual Pix + registro de comprovante.

- Ranking por paid/activated runners.

## Fase 2 - Social Intelligence (2 a 4 semanas + approvals externos)

- Meta Instagram OAuth + Insights.

- TikTok OAuth + video metrics.

- Importar/vincular posts.

- Performance por conteudo.

- Story snapshots + fallback screenshot.

- YouTube opcional.

## Fase 3 - Payout Automation (1 a 2 semanas)

- Asaas Pix API.

- Payout requests, minimum, approval, status/retry.

- Reconciliacao financeira e export para contabilidade.

## Fase 4 - Gamification e Network Effects

- Achievements, missions, levels automaticos, share cards, Rising Creators.

- Notificacoes de milestone.

- Insights coletivos de creatives vencedores.

- Recomendacoes de conteudo por perfil do creator.

## Dependencia externa

Meta App Review e autorizacoes sociais podem demorar e mudar. Por isso o MVP financeiro nao deve depender delas. A RunEasy pode operar creators com link/codigo + RevenueCat desde o primeiro dia e adicionar social metrics depois.

## 21. Backlog de implementacao

| Prioridade | Item | Definition of done |
| --- | --- | --- |
| P0 | Auth + invite + roles | Creator entra e ve somente seus dados. |
| P0 | Creator + tier + code + slug | Cada creator possui identidade de |
|   |   | atribuicao. |
| P0 | Redirect /c/:slug | Registra click e redireciona. |
| P0 | App attribution setter | Salva creator_id no usuario e RevenueCat. |
| P0 | RevenueCat webhook | Grava compras/renewals/refunds com |
|   |   | idempotency. |
| P0 | Commission engine | Calcula proceeds e tier %. |
| P0 | Ledger + balances | Pending/available/paid. |
| P0 | Creator dashboard | Impacto, paid, ganhos, progresso. |
| P0 | Admin creators + finance | Aprova creators, tiers e payouts. |


| P1 | Content Hub | Global e segmentado. |
| --- | --- | --- |
| P1 | Creator projects | Briefs privados e deliverables. |
| P1 | Impact aggregator | Activated runners, workouts e km. |
| P1 | Ranking + achievements | Gamificacao sem vanity metrics. |
| P1 | Instagram OAuth/Insights | Import e metricas. |
| P1 | TikTok OAuth/video.list | Import e metricas. |
| P1 | Social scheduler | Snapshots e retries. |
| P1 | Asaas payout | Pix automatico. |
| P2 | AppsFlyer OneLink | Deferred attribution mais forte. |
| P2 | Missions + rewards | Orquestrar campanhas na rede. |
| P2 | Share cards | Milestones compartilhavel. |
| P2 | Creative analytics | Cross-creator insights e winner detection. |

## 22. KPIs para validar o canal

O objetivo do MVP nao e provar que creators conseguem views. E provar que o programa cria aquisicao previsivel e economicamente atraente.

| KPI | Definicao |
| --- | --- |
| Activation de creators | % dos convidados que conectam/recebem link e publicam |
|   | primeiro conteudo |
| Creator activation time | Dias do convite ao primeiro post |
| Click-through | Clicks / views para conteudo vinculado |
| Click -> paid | Paid subscribers / clicks |
| Creator CAC | Comissao + bonus + custo operacional / novos pagos |
| Contribution after creator | Proceeds - creator commission - AI variable cost |
| 30/60/90d retention | Retention dos usuarios por creator/cohort |
| Activated runner rate | % de pagos atribuidos que completam primeiro treino |
| Creator concentration | Quanto do canal depende dos top 5 creators |
| Creator retention | % que continua ativo 30/60/90 dias |
| Time to payout | Tempo entre periodo fechado e pagamento |
| Fraud/refund rate | Refunds/self-referrals/ajustes por creator |


## North Star do programa

Novos corredores ativados com contribution margin positiva.

Essa metrica une impacto de marca e economia: a pessoa nao apenas comprou - ela entrou no

esporte e a RunEasy adquiriu o usuario de forma sustentavel.

RUNEASY / CREATOR PLATFORM BLUEPRINT

## 23. Decisoes que ainda precisam ser tomadas

- Comissao vale por 12 meses, por toda a assinatura ou por numero fixo de renovacoes?

- Attribution window: 7, 14 ou 30 dias? Recomendacao inicial: 30 dias.

- Codigo do creator tera desconto para usuario ou sera apenas identificador? Recomendacao inicial: sem desconto para evitar leakage, ou desconto pequeno apenas em campanhas controladas.

- Qual evento desbloqueia tier: paid subscriber ou activated runner? Recomendacao: paid para dinheiro; activated runner como metrica de impacto e requisito complementar.

- Qual payout minimum: R\$ 50 ou R\$ 100?

- Qual janela pending antes de available?

- Qual limite exige dupla aprovacao financeira?

- Creators brasileiros somente no MVP ou internacional desde o inicio?

- AppsFlyer entra no MVP ou apos provar que o codigo cobre atribuicao suficiente?

- Quais dados de corrida agregados podem ser exibidos sem criar risco de privacidade?

## 24. Fontes oficiais e notas tecnicas

As fontes abaixo foram verificadas em agosto de 2026. APIs sociais e de pagamentos mudam; antes de implementar, confirmar changelogs e scopes atuais.

[S1] Meta for Developers - Instagram Platform / Insights - Instagram API para contas profissionais; media/account insights; limitações de contas pessoais. Fonte oficial [S2] Meta for Developers - User and Media Insights on Instagram API with Instagram Login - Permissao instagram_business_manage_insights e advanced access para insights. Fonte oficial [S3] TikTok for Developers - Query Videos - Scope video.list e campos view_count, like_count, comment_count, [URL 🔗](https://developers.facebook.com/documentation/instagram-platform/insights)

share_count. Fonte oficial [URL 🔗](https://developers.tiktok.com/doc/tiktok-api-v2-video-query)

[S4] RevenueCat - Customer Attributes - Custom attributes, leitura server-side, deep link como atributo e limites. Fonte [URL 🔗](https://www.revenuecat.com/docs/customers/customer-attributes)

[oficial](https://www.revenuecat.com/docs/customers/customer-attributes)

[S5] RevenueCat - Webhooks / Event Types - Eventos de subscription lifecycle e subscriber_attributes em payloads. Fonte [URL 🔗](https://www.revenuecat.com/docs/integrations/webhooks/event-types-and-fields)

[oficial](https://www.revenuecat.com/docs/integrations/webhooks/event-types-and-fields)

[S6] RevenueCat - Taxes and Commissions - Estimativa de impostos/comissao e formula de proceeds. Fonte oficial [S7] RevenueCat - AppsFlyer Integration - Integracao de subscription lifecycle com AppsFlyer. Fonte oficial [S8] AppsFlyer - Deep Linking / OneLink - Deep linking e deferred deep linking para instalacao. Fonte oficial [S9] Superwall Docs - User attributes, deep links e RevenueCat integration. Fonte oficial [S10] Google Developers - YouTube Analytics/Data APIs - View stats e analytics autorizados. Fonte oficial [S11] Asaas - Pix / Transfers API - Envio de dinheiro via Pix e transferencias para terceiros. Fonte oficial [S12] Stripe Connect / Payouts - Arquitetura de connected accounts e particularidades de payouts. Fonte oficial [URL 🔗](https://docs.asaas.com/reference/transferir-para-conta-de-outra-instituicao-ou-chave-pix)

[S13] Supabase Docs - Auth, Postgres, Storage, RLS, Edge Functions, Cron e seguranca. Fonte oficial [URL 🔗](https://supabase.com/docs)


## 25. Plano pratico dos primeiros 30 dias

## Semana 1 - Fundacao

- Fechar regra de comissao, attribution window, tiers e termos do programa.

- Criar schema Supabase, Auth/RLS, creator profile e painel admin minimo.

- Gerar link/codigo e implementar redirect com click tracking.

## Semana 2 - Money Loop

- Salvar creator_id no usuario/app e no RevenueCat.

- Receber webhooks, criar subscription_events, proceeds e commission_ledger.

- Dashboard inicial: novos pagos, activated runners, pending/available e progresso de tier.

Semana 3 - Operacao de creators

- Content Hub, projetos privados, deliverables e ranking mensal.

- Rodar piloto real com 5-10 creators usando payout manual Pix.

- Validar onde a atribuicao perde sinal e decidir se AppsFlyer entra cedo.

Semana 4 - Automacao

- Iniciar Meta/TikTok app review/OAuth e social import.

- Implementar Asaas sandbox para payout e reconciliacao.

- Revisar unit economics por creator e ajustar tiers antes de escalar outbound.

## Conclusao

A RunEasy nao precisa construir uma plataforma gigantesca para capturar o diferencial. O diferencial nasce quando tres loops fecham juntos: (1) atribuicao e dinheiro confiaveis; (2) feedback de creative e performance; (3) impacto real em novos corredores. Construa o Money Loop primeiro, prove que o creator CAC e a retention funcionam, e use social APIs/gamificacao para acelerar uma maquina que ja gera valor.
