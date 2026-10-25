## RunEasy Creators — Arquitetura Técnica e Viabilidade

O ct 22, 2026 · @João Carlos

## 1. Veredito: tudo é viável, e o dinheiro não depende de Meta nem TikTok

As 13 telas do protótipo e o PRD são construíveis com serviços que a Oyto já usa ou pode contratar sozinha. O único trecho que depende de terceiros é a leitura de views/likes das redes, e ele entra depois do lançamento, sem bloquear o resto.

| Bloco Login, perfil, papéis (creator / marketing / financeiro / admin) Link, código, QR code, | Viabilidade Depende de Total Total | Observação Supabase Auth Já é o auth do app Redirect próprio Clique por origem (bio, |   |
| --- | --- | --- | --- |
| campanhas, cliques Atribuição, trials, | Total | story, YouTube) vem de parâmetro no link, não de API social RevenueCat (já Fonte de verdade do |   |
| assinaturas, receita Comissão, ledger, | Total | integrado ao app) dinheiro Asaas (conta PJ nova) Pagamento separado |   |
| wallet, saque Pix Impacto ("você ajudou 78 pessoas a começar a correr", km) Ranking, conquistas, níveis, missões, Creative Hub, projetos Views, alcance, likes do Instagram | Total Total Com aprovação | das lojas, não há split na compra Banco do RunEasy Só agregados, nunca (workouts) dado individual do corredor Banco próprio CRUD + regras Meta App Review + Só contas profissionais verificação da (Business ou Creator) empresa |   |
| Views, likes, shares do TikTok | Com aprovação | Revisão do app no Creator autoriza um por TikTok for Developers um |   |


| Bloco | Viabilidade Depende de |   | Observação |
| --- | --- | --- | --- |
| Installs | Parcial | Sem MMP: só quem | Install exato pede |
|   |   | abre o app com | AppsFlyer (fase 2) |
|   |   | link/código |   |

Mudança de rota no Instagram. A conclusão anterior era que o caminho "Instagram Login" não dá insights e exigiria Página do Facebook. A documentação atual (Graph API v25.0) diz o contrário: desde março de 2025 o Instagram Login lê views de posts, reels e stories pela

permissão instagram_business_manage_insights , sem Página vinculada. Recomendo

migrar para esse caminho; detalhes e o único custo (stories viram polling) na seção 8.

Ordem certa: Money Loop primeiro (link → atribuição → compra → comissão → painel → Pix), com piloto de 5–10 creators e métricas sociais preenchidas por print. Instagram e TikTok entram quando as revisões saírem, e elas só podem ser pedidas com o portal já no ar, porque os revisores testam o fluxo real.

## 2. Arquitetura geral

Um único serviço novo, o creators-api , mexe em dinheiro, tokens e regras. O app só envia o código do creator, o banco é o mesmo Supabase, e o backend atual do app não muda.


## Um servigo novo concentra dinheiro e redes; 0 app sé envia o cédigo

*arquitetura · 9 componentes, 1 serviço novo*

O app compra pela loja e o RevenueCat avisa o creators-api por um segundo webhook; o Asaas pergunta antes de cada Pix (seta dupla); o backend do app só divide o banco.

## 3. Stack: reaproveitar o que a Oyto já opera, com um serviço novo isolado

A plataforma roda no mesmo Supabase do app (schema próprio), com um backend NestJS separado do backend do app. O PRD sugeria Next.js Route Handlers ou Edge Functions; troquei por NestJS porque é a stack que você já domina, tem filas e cron prontos, e Pix com IP fixo pede um servidor de verdade.

| Camada | Escolha Por quê |
| --- | --- |
| Portal (creator + manager) UI | Next.js (App Router) + TypeScript Vercel Pro já contratada; na Vercel, em subdomínio já decidido creators.runeasy.com.br Tailwind + componentes próprios; As telas são preto/branco + gráficos com Recharts ciano #00D4FF , fácil de tokenizar |


| Camada | Escolha | Por quê |
| --- | --- | --- |
| Backend | creators-api : NestJS novo no | Isola dinheiro e jobs pesados |
|   | Railway, mesmo repositório | do backend do app em |
|   |   | produção; deploy |
|   |   | independente; só ele precisa |
|   |   | de IP fixo. Domínio: creators- |
|   |   | api.runeasy.com.br |
| Banco | Supabase PROD/staging atuais, | Impacto ("78 pessoas |
|   | schema creators | correram") exige cruzar com |
|   |   | workouts ; sem sincronizar |
|   |   | dois bancos |
| Auth | Supabase Auth do mesmo projeto | Creator entra com a mesma |
|   |   | conta RunEasy; o mesmo |
|   |   | user_id permite detectar |
|   |   | autoindicação |
| Filas e cron | BullMQ + Redis (já no Railway) + | Sync social, disponibilidade |
|   | @nestjs/schedule | de comissão, reconciliação de |
|   |   | Pix |
| Arquivos | Supabase Storage (assets do | Já incluso; vídeo pesado pode |
|   | Creative Hub, entregáveis, | ir para R2 depois |
|   | comprovantes) |   |
| Links curtos | Rewrite na Vercel | Domínio confiável no link, |
|   | runeasy.com.br/c/:slug → | lógica no backend |
|   | creators-api |   |
| Assinaturas | RevenueCat (2º webhook, só para o | RevenueCat aceita várias |
|   | creators-api ) | integrações de webhook por |
|   |   | projeto; o webhook do app |
|   |   | fica intocado |
| Paywall | Superwall recebe creator_id | Paywall por origem; nunca |
|   | como atributo (opcional) | calcula comissão |
| Pagamentos | Asaas API v3 (transferência Pix) | Decisão tomada; seção 7 |
| Redes | Instagram API com Instagram Login | Seções 8–10 |
|   | ( graph.instagram.com v25.0), |   |
|   | TikTok Display API v2, YouTube Data |   |
|   | API v3 |   |
| E-mail | Resend ( mail.runeasy.com.br , já | Convites, saque pago, missão |
|   | configurado) | nova |


| Camada | Escolha | Por quê |
| --- | --- | --- |
| Observabilidade | Sentry + PostHog (já usados) + | Erro de webhook e de Pix |
|   | tabela audit_log | precisa ser rastreável |

Três regras de arquitetura valem para tudo:

- 1. O navegador nunca fala com o banco. O schema creators fica fora da Data API do Supabase; só o creators-api (service role) lê e escreve, e checa papel e dono em cada rota.

- 2. Dinheiro é cálculo determinístico no backend e vira lançamento imutável no ledger. Nenhum saldo é um número editável.

- 3. Tokens do Instagram/TikTok e chaves do Asaas ficam só no backend, com os tokens sociais criptografados (AES-256-GCM, chave em variável do Railway).

## 4. Mapa tela → dado → fonte

Cada número das telas tem uma fonte determinada; nenhum vem de estimativa da IA. Onde a tela promete algo que a fonte não entrega, a coluna "Ajuste" diz o que muda.

| Tela | O que mostra | Fonte | Ajuste necessário |
| --- | --- | --- | --- |
| Login | E-mail + senha, | Supabase Auth | Aceitar também magic |
|   | esqueci a senha |   | link; convite |
|   |   |   | obrigatório (não há |
|   |   |   | cadastro aberto) |
| Onboarding Boas-vindas → |   | creators , | Passos: termos → |
|   | configuração | creator_profiles | dados de pagamento → |
|   |   |   | conectar redes |
|   |   |   | (opcional) → link |
|   |   |   | gerado |
| Visão Geral | "Você ajudou 78 | Ledger + | "Pessoa que começou |
|   | pessoas", meta | user_attributions + | a correr" = atribuído |
|   | 100, nível Partner | workouts + click_events | que completou o 1º |
|   | 7/10, novos |   | treino (não só pagou); |
|   | corredores, |   | corrigir o rótulo |
|   | assinantes ativos, |   | "INICIARAHHM |
|   | ganhos, CTR, funil |   | TESTE" |
|   | da semana, |   |   |
|   | conteúdo |   |   |
|   | destaque, missão |   |   |


| Tela | O que mostra | Fonte | Ajuste necessário |
| --- | --- | --- | --- |
| Performance Views, cliques, |   | APIs sociais (views) + | Installs = só quem |
|   | installs, trials, | redirect (cliques) + | abriu o app com |
|   | assinantes, | RevenueCat (trials, | link/código; receita por |
|   | comissão; gráfico | assinaturas) | conteúdo exige um link |
|   | 30d; funil; tabela |   | por conteúdo |
|   | por conteúdo |   |   |
| Creative | Ideia "High | content_library | v1: score e "tendência" |
| Hub | converting" com | (curadoria do marketing) | editoriais; score |
|   | score 98%, hook, |   | calculado só quando |
|   | estrutura, CTA, |   | houver conversão |
|   | referência visual, |   | agregada por ideia |
|   | experimento da |   |   |
|   | semana |   |   |
| Ideia #128 | Hook, estrutura, | content_library + Storage | "Marcar como vou |
|   | CTA, assets para | + tracking_links | produzir" cria o link |
|   | baixar, link de |   | /c/mcosta/km7 |
|   | tracking |   | daquela ideia; música |
|   |   |   | só com licença |
|   |   |   | comercial |
| Missões | Disponíveis / | missions , | Recompensa "+5% de |
|   | participando / | mission_progress | comissão" vira regra |
|   | concluídas, |   | temporária no motor |
|   | recompensa, |   | ( commission_boosts ) |
|   | prazo, |   |   |
|   | participantes |   |   |
| Projetos | Briefing, | creator_projects , | Chat simples por |
|   | entregáveis, | project_deliverables , | projeto; notificação |
|   | arquivos, | project_messages , Storage | por e-mail |
|   | comentários |   |   |
| Ranking | Posição, novos | View mensal sobre | Nunca mostrar receita |
|   | corredores do | user_attributions + | dos outros; nomes de |
|   | mês, tier | workouts | tier divergem entre |
|   |   |   | telas e PRD (seção 14) |
| Conquistas | Badges | achievements , | Regras em JSON |
|   | desbloqueados e | creator_achievements | avaliadas por job |
|   | em progresso |   |   |


| Tela | O que mostra | Fonte | Ajuste necessário |
| --- | --- | --- | --- |
| Earnings & | Disponível, | commission_ledger , | Trocar @joaocorredor |
| Wallet | pendente, total | payout_requests | por "Assinante #4821": |
|   | recebido, receita |   | o creator não pode ver |
|   | gerada, nível, |   | quem assinou (LGPD) |
|   | histórico, saques |   |   |
| My Link | URL, código, QR, | tracking_links , | Origem (bio / story / |
|   | cliques por | click_events | YouTube) vem de link |
|   | origem, |   | diferente por posição |
|   | campanhas |   | ( ?src=story ), não de |
|   |   |   | API |
| Profile | Instagram/TikTok | creator_social_accounts , | Botão desconectar |
|   | conectados, | payout_profiles | rede = revoga e apaga |
|   | editar perfil, |   | o token |
|   | pagamento, |   |   |
|   | notificações, |   |   |
|   | privacidade |   |   |

## 5. Modelo de dados (schema creators )

São 27 tabelas em 6 grupos. Valores em dinheiro sempre em centavos ( bigint ), nunca float . O schema fica fora da Data API e com RLS "nega tudo" ligada, como segunda barreira caso alguém exponha por engano.

| Grupo | Tabela | Campos principais |
| --- | --- | --- |
| Identidade | creators | user_id (auth.users), slug , code |
|   |   | (único, maiúsculo), tier_id , |
|   |   | status (invited / active / paused / |
|   |   | banned) |
| Identidade | creator_profiles | display_name , handle , |
|   |   | avatar_url , niche , bio |
| Identidade | payout_profiles | person_type (PF / MEI / PJ), |
|   |   | cpf_cnpj_enc , pix_key_type , |
|   |   | pix_key_enc , legal_name , |
|   |   | verified_at |
| Identidade | staff_roles | user_id , role (marketing / |
|   |   | finance / admin) |


| Grupo | Tabela | Campos principais |
| --- | --- | --- |
| Identidade | creator_social_accounts | platform , platform_user_id , |
|   |   | username , account_type , scopes , |
|   |   | token_enc , expires_at , status |
| Atribuição | tracking_links | creator_id , slug , placement (bio |
|   |   | / story / youtube / conteúdo / |
|   |   | campanha), content_post_id? , |
|   |   | campaign_tag? , active |
| Atribuição | click_events | link_id , click_id , ts , os , |
|   |   | ip_hash (com sal, retenção 90 dias) |
| Atribuição | user_attributions | app_user_id (único), creator_id , |
|   |   | source (code / link / manual), |
|   |   | click_id? , attributed_at , |
|   |   | locked_at , lock_until |
| Dinheiro | subscription_events | rc_event_id (único), |
|   |   | app_user_id , type , product_id , |
|   |   | period_type , price , currency , |
|   |   | tax_percentage , |
|   |   | commission_percentage , |
|   |   | transaction_id , environment , |
|   |   | raw |
| Dinheiro | tiers | name , order , commission_bps , |
|   |   | unlock_rule (JSON) |
| Dinheiro | commission_boosts | creator_id , extra_bps , |
|   |   | starts_at , ends_at , |
|   |   | mission_id? |
| Dinheiro | commission_ledger | creator_id , |
|   |   | subscription_event_id? , type |
|   |   | (accrual / refund_adjustment / |
|   |   | bonus / payout / manual), |
|   |   | amount_cents (±), state (pending |
|   |   | / available / paid / reversed), |
|   |   | available_at , payout_id? , |
|   |   | created_by |
| Dinheiro | payout_requests | amount_cents , status (requested |
|   |   | / approved / sent / paid / failed / |
|   |   | cancelled), asaas_transfer_id , |
|   |   | external_reference , |
|   |   | receipt_url , approved_by |


| Grupo | Tabela | Campos principais |
| --- | --- | --- |
| Conteúdo | content_posts | platform , platform_media_id , |
|   |   | media_type , url , published_at , |
|   |   | project_id? , mission_id? , |
|   |   | tracking_link_id? |
| Conteúdo | social_metric_snapshots | content_post_id , captured_at , |
|   |   | views , reach , likes , comments , |
|   |   | shares , saves , source (api / |
|   |   | manual) |
| Conteúdo | manual_proofs | content_post_id , file_path |
|   |   | (print de Insights), status , |
|   |   | reviewed_by |
| Conteúdo | content_library | kind (ideia / roteiro / inspiração / |
|   |   | winner), hook , structure (JSON), |
|   |   | cta , references , tags , status , |
|   |   | score? , visibility |
| Conteúdo | content_library_saves | creator_id , item_id , state |
|   |   | (salvo / vou produzir / produzido) |
| Engajamento | missions / mission_progress | brief, janela, recompensa (JSON), |
|   |   | segmentação por tier / estado, |
|   |   | conteúdo vinculado |
| Engajamento | achievements / | code , rule (JSON), unlocked_at , |
|   | creator_achievements | progress |
| Engajamento | creator_projects / | briefing, status, prazo, arquivo, |
|   | project_deliverables | thread creator ↔ marketing / |
|   | project_messages |   |
| Impacto | impact_daily (view materializada) | creator_id , date , new_paid , |
|   |   | activated_runners , |
|   |   | active_runners , workouts , km |
| Operação | webhook_inbox | provider (revenuecat / asaas), |
|   |   | event_id (único), payload , |
|   |   | status , attempts |
| Operação | audit_log | actor_user_id , action , entity , |
|   |   | before , after , created_at |

O elo com o app é só user_attributions.app_user_id → auth.users.id ;

impact_daily lê workouts por esse elo e grava apenas contagens.


## 6. Money Loop: link → atribuição → compra → comissão

Sem MMP, o código do creator é a atribuição real; o link mede cliques e leva a pessoa até o código. A loja apaga parâmetros na instalação, e casar clique com usuário por IP + navegador é impreciso e ruim para LGPD, então não fazemos.

## 6.1 Link e código

- 1. Creator divulga runeasy.com.br/c/marina (ou uma variação por posição: ? src=story , ou por conteúdo: /c/mcosta/km7 ).

- 2. A Vercel repassa para o creators-api , que grava click_events (com click_id ) e responde 302.

- 3. Destino = página-ponte runeasy.com.br/convite/marina : "Marina te convidou · use o código MARINA", botão copiar, botões das lojas por sistema. Se o app já estiver instalado, o Universal/App Link abre o app com creator=marina .

- 4. No app, o campo "Código de quem te indicou" (reaproveitar a tela de referral que já existe) chama POST /attribution no creators-api com o JWT do usuário.

- 5. O creators-api valida o código, bloqueia autoindicação ( creator.user_id = app_user_id ), grava user_attributions e espelha creator_id e creator_code como atributos no RevenueCat (para relatórios e Superwall). A fonte da verdade continua sendo a tabela.

Pré-requisito a conferir no código do app: o app_user_id do RevenueCat precisa ser o user_id do Supabase. Se o app compra com ID anônimo antes do login, o webhook chega com um ID que a tabela não conhece.

## 6.2 Regras de atribuição (do PRD, adotadas)

- Código digitado vence link. Sem código, vale o último link aberto no app (Universal Link) nos 30 dias antes do 1º pagamento.

- No 1º pagamento a atribuição trava ( locked_at ) pelo período do programa (12 meses é a proposta; decisão pendente).

- Renovação nunca troca de creator; troca manual só com registro no audit_log .

## 6.3 Webhook do RevenueCat

Uma segunda integração de webhook aponta para https://creators- api.runeasy.com.br/webhooks/revenuecat , filtrada para produção (outra para staging). O handler confere o header de autorização e a assinatura X-RevenueCat-Webhook- Signature (HMAC), grava o payload em webhook_inbox com event.id único, responde 200 e processa numa fila. Evento repetido é ignorado pelo índice único.


| Evento | O que o motor faz |
| --- | --- |
| INITIAL_PURCHASE com period_type = | Conta trial; sem comissão |
| TRIAL |   |
| INITIAL_PURCHASE pago | Trava atribuição; lança comissão pending |
| RENEWAL (inclui conversão de trial) | Lança comissão pending enquanto a |
|   | atribuição estiver vigente |
| PRODUCT_CHANGE | Nada no ato; a próxima cobrança gera o |
|   | lançamento |
| CANCELLATION por estorno | Lançamento negativo refund_adjustment |
|   | (conferir no payload de exemplo da |
|   | RevenueCat como o reembolso vem |
|   | marcado) |
| CANCELLATION comum, EXPIRATION , | Só status (alimenta "assinantes ativos") |
| BILLING_ISSUE |   |
| TRANSFER | Fila de revisão manual |
| Qualquer evento SANDBOX em produção | Ignorado |

## 6.4 Fórmula da comissão

A base é o proceeds que o próprio RevenueCat estima por transação; a taxa é a do tier no momento do evento, somada a boosts de missão ativos, e fica gravada na linha (mudar tier depois não reescreve histórico).

```
\text{comissão} = \left\lfloor \text{preço} \times (1 -
\text{tax\_percentage} - \text{commission\_percentage}) \times
\frac{\text{tier\_bps} + \text{boost\_bps}}{10000}
\right\rfloor_{\text{centavos}}
```

Exemplo ilustrativo (impostos 10% + loja 15%): mensal R\$ 24,90 → proceeds R\$ 18,67 → Partner 30% = R\$ 5,60; anual R\$ 149,90 → proceeds R\$ 112,42 → 30% = R\$ 33,72. A tela de Earnings hoje calcula 30% sobre o preço cheio (R\$ 359 → R\$ 107,70); precisa mostrar a base real.

## 6.5 Ledger: pendente → disponível → pago

- Todo valor entra pending . Um job diário move para available quando passa o available_at (proposta: fechamento mensal, liberado no dia 10 do mês seguinte).

- Saldo exibido = soma dos lançamentos por estado. Nada de coluna balance .


- Estorno depois do saque vira lançamento negativo; o saldo pode ficar negativo e é compensado nos próximos ganhos (precisa estar nos termos).

- Tier: promoção na hora em que a meta é batida; rebaixamento só no fechamento do mês.

## 7. Asaas: saque por Pix com quatro travas

O botão "Sacar" cria um pedido; o Pix sai pelo POST /v3/transfers do Asaas só depois de quatro travas. O dinheiro das lojas não passa pelo Asaas: ele cai na conta PJ atual (Conta Simples), e antes de cada rodada de pagamentos a Oyto transfere para o Asaas o total aprovado.

## 7.1 O que precisa existir antes

- Conta PJ da Oyto no Asaas aprovada (envio de documentos) e uma conta no Sandbox para desenvolver.

- Chave de API de produção só nas variáveis do creators-api no Railway.

- IP fixo de saída no serviço creators-api : o Railway Pro oferece Static Outbound IPs (3 IPs balanceados, presos à região). São esses IPs que entram na whitelist do Asaas.

- Tarifa de Pix enviado da conta Asaas: confirmar na tabela de tarifas da conta antes de definir o saque mínimo.

## 7.2 As quatro travas

| Trava | Onde configura | O que faz |
| --- | --- | --- |
| Whitelist de IPs | Asaas → Integrações → | Chave vazada não funciona fora |
|   | Mecanismos de segurança | dos IPs do Railway |
| Evento crítico em | Mesma tela (ligado por | Cada saque via API fica pendente |
| saque | padrão) | até alguém aprovar com token no |
|   |   | Asaas. Manter ligado no v1 = |
|   |   | dupla aprovação de graça; |
|   |   | desligar só para os IPs da whitelist |
|   |   | quando o volume crescer |


| Trava | Onde configura | O que faz |
| --- | --- | --- |
| Webhook de | Mesma tela: URL + e-mail + | ~5 s após criar o Pix, o Asaas |
| validação de saque | token | pergunta ao creators-api se |
|   |   | aprova. Comparamos ID, valor e |
|   |   | chave com o payout_request e |
|   |   | respondemos APPROVED ou |
|   |   | REFUSED . Três falhas = Pix |
|   |   | cancelado |
| Aprovação no portal Manager Portal (papel |   | Ninguém saca sem aprovação |
|   | finance ) | humana no v1; depois, auto- |
|   |   | aprovação abaixo de um teto |

## 7.3 Fluxo de um saque

- 1. Creator pede o saque. O creators-api confere: saldo available ≥ mínimo, perfil de pagamento verificado, sem outro saque aberto, sem alerta de fraude.

- 2. Primeiro saque de cada creator: consulta a chave Pix no Asaas e confere se o titular bate com o CPF/CNPJ cadastrado.

- 3. Financeiro aprova no portal.

- 4. creators-api cria a transferência com externalReference = id do pedido. Se a chamada cair, consulta pelo externalReference antes de tentar de novo (nunca dois Pix).

- 5. Asaas chama o webhook de validação → APPROVED .

- 6. Webhook de transferência concluída ( TRANSFER_DONE ) → lançamento payout negativo no ledger, linhas marcadas paid , comprovante salvo, e-mail ao creator. Falha ( TRANSFER_FAILED ) devolve o pedido para revisão sem mexer no saldo.

## 8. Instagram API: caminho "Instagram Login", sem Página do Facebook

Use a Instagram API com Instagram Login (Business Login for Instagram, host graph.instagram.com , v25.0) com só duas permissões: instagram_business_basic e

instagram_business_manage_insights . Um app Meta usa Facebook Login ou Instagram

Login, nunca os dois; as permissões do caminho Facebook que já foram adicionadas

( instagram_manage_insights , instagram_basic , pages_read_engagement , pages_show_list , business_management ) saem do pedido de revisão.


## 8.1 O que dá e o que não dá

| Dá para ler | Não dá |
| --- | --- |
| Lista de posts, reels e stories ativos do | Conta pessoal: o creator precisa virar conta |
| creator ( /me/media , /me/stories ) | profissional (Creator ou Business, grátis, |
|   | sem Página) |
| views em posts, reels e stories; reach , | impressions e plays : descontinuadas em |
| likes , comments , shares , saved , | abril de 2025 (usar views ) |
| total_interactions |   |
| Reels: ig_reels_avg_watch_time , | Métricas total_* (somando anúncios e |
| reels_skip_rate | Facebook): só no Facebook Login |
| Posts e stories: profile_visits , follows ; | Webhook story_insights : só no |
| stories: navigation | Facebook Login (aqui, stories por polling) |
|   | Story depois de 24 h (só se for para os |
|   | Destaques); story com menos de 5 |
|   | visualizações dá erro 10 |
|   | Métricas de itens dentro de carrossel; |
|   | cliques no sticker de link (nós contamos |
|   | pelo redirect) |

Os números podem atrasar até 48 h e ficam disponíveis por 2 anos; mesmo assim gravamos snapshots próprios.

## 8.2 Tokens

O código do OAuth vira token curto, que trocamos na hora por token longo (cerca de 60 dias) e renovamos por job antes de vencer. Token revogado ou vencido marca a conta como "reconectar" e avisa o creator.

## 8.3 Acesso Standard × Advanced

Sem revisão, o app tem Standard Access: só funciona para contas com papel no app (você e testers). Para ler contas de creators que a Oyto não administra é obrigatório Advanced Access, que exige App Review e Verificação da Empresa.

## 8.4 Passo a passo até a aprovação

- Concluir a Verificação da Empresa da Oyto no portfólio "Oyto Tecnologia" (CNPJ, comprovante, domínio com e-mail @oytotecnologia.com ).

- Confirmar no painel que o app "RunEasy Creators" é do tipo Business.


- Em Instagram → "API setup with Instagram login": gerar Instagram App ID e secret; cadastrar o redirect https://creators.runeasy.com.br/api/auth/instagram/callback .

- Preencher os callbacks obrigatórios: Deauthorize e Data Deletion Request apontando para endpoints reais do creators-api (não só a página /creators/exclusao-de- dados ).

- Pedir só instagram_business_basic + instagram_business_manage_insights . Permissão pedida e não usada é motivo clássico de recusa.

- Passar sua conta de Instagram para Creator e adicioná-la como Instagram Tester; aceitar o convite no app do Instagram.

- Com o portal em staging, fazer pelo menos 1 chamada bem-sucedida por permissão ( /me , /me/media , /{media-id}/insights ); sem isso o botão de pedir Advanced Access não libera.

- Gravar o screencast: login no portal → "Conectar Instagram" → tela de consentimento → volta ao portal → lista de posts → vincular um Reel a uma missão → card de métricas. Legendas em inglês.

- Escrever, por permissão, para que serve e onde aparece ("mostrar ao creator as views dos conteúdos que ele vinculou às campanhas da RunEasy").

- Política de privacidade, termos e exclusão já publicados em runeasy.com.br/creators/* : revisar se citam Instagram, quais dados, por quanto tempo e como desconectar.

- Colocar o app em modo Live e enviar para revisão. Planeje margem para ao menos uma devolução com pedido de ajuste.

- Depois da aprovação: manter o Data Use Checkup anual em dia e o callback de exclusão funcionando.

## 9. TikTok API: Login Kit + Display API

O TikTok entrega exatamente o que a tela de Performance pede (views, likes,

comentários, shares por vídeo), mas nada de alcance, retenção ou tempo assistido. Produtos: Login Kit (Web) + Display API, com os scopes user.info.basic e video.list

(opcional: user.info.stats para seguidores).

## 9.1 O que dá e o que não dá

| Dá para ler | Não dá |
| --- | --- |
| POST /v2/video/list/ : vídeos recentes do | Alcance, tempo assistido, retenção, origem |
| creator, paginados | do tráfego, demografia |


| Dá para ler | Não dá |
| --- | --- |
| POST /v2/video/query/ : até 20 IDs por | Vídeos de outras contas (só a Research API, |
| chamada, só vídeos do próprio creator | restrita a pesquisa acadêmica) |
| Campos view_count , like_count , | Cliques no link da bio (contamos pelo |
| comment_count , share_count , share_url , | redirect) |
| cover_image_url , create_time |   |

O usuário pode aceitar só parte dos scopes; o backend lê o campo scope da resposta do token e trata a falta de video.list como "conexão incompleta".

## 9.2 Dois cuidados que travam a revisão

- Site oficial não pode ser landing page nem página de login. Informe https://runeasy.com.br e garanta que ele tenha uma seção /creators explicando o programa, com botão para o portal. Informar creators.runeasy.com.br (que é só login) é recusa provável.

- O domínio do vídeo de demonstração precisa bater com o site informado. Comece o vídeo em runeasy.com.br/creators e só então entre no portal.

## 9.3 Passo a passo até a aprovação

- App já criado na organização "Runeasy" (tipo "Other", Draft). Nome "RunEasy Creators" serve: não cita o TikTok e bate com o produto.

- Preencher ícone, descrição (aparece na tela de autorização), categoria, URLs de termos e privacidade ( runeasy.com.br/creators/termos e /privacidade ).

- Plataforma Web com site https://runeasy.com.br .

- Adicionar Login Kit com redirect https://creators.runeasy.com.br/api/auth/tiktok/callback (HTTPS obrigatório).

- Adicionar Display API e marcar user.info.basic + video.list (só o que vai usar).

- Criar um Sandbox e adicionar sua conta TikTok como usuário-alvo para desenvolver antes da aprovação.

- Implementar o botão "Continuar com TikTok" seguindo o Design Guidelines do TikTok.

- Gravar o vídeo: abrir runeasy.com.br/creators → Entrar → Perfil → Conectar TikTok → autorizar → lista de vídeos → vincular a uma missão → métricas na Performance.

- Explicar, por produto e por scope, como cada um aparece na experiência do creator.

- Enviar. A revisão leva de alguns dias a duas semanas; se voltar, o motivo fica no histórico do app.


## 9.4 Tokens

Token de acesso curto e refresh token de longa duração: o job de sync renova antes de cada rodada e marca "reconectar" se o refresh falhar.

## 10. YouTube e demais serviços externos

YouTube: só estatísticas públicas, sem OAuth. O creator cola a URL do vídeo; o

creators-api lê viewCount , likeCount e commentCount pela YouTube Data API v3 com

uma chave de API. Assim não há tela de consentimento nem verificação OAuth do Google (o mesmo tipo de processo que travou a Google Health API). Tempo assistido e retenção exigiriam a YouTube Analytics API com OAuth do dono do canal: fica fora até haver demanda.

| Serviço | Para quê | Situação | Custo novo |
| --- | --- | --- | --- |
| Supabase | Banco, auth, | Já contratado | Marginal (schema novo |
|   | storage |   | no mesmo projeto) |
| Railway | creators-api + | Já usado | 1 serviço a mais; Static |
|   | Redis |   | Outbound IP exige plano |
|   |   |   | Pro |
| Vercel | Portal + rewrite dos | Já em Pro | Nenhum previsto |
|   | links |   |   |
| RevenueCat | Eventos de | Já contratado | Webhook extra na |
|   | assinatura |   | mesma conta |
| Asaas | Pix para creators | Abrir conta PJ | Tarifa por Pix enviado |
|   |   |   | (confirmar) |
| Meta | Views e | App criado, falta | Grátis |
| (Instagram | engajamento | revisão |   |
| API) |   |   |   |
| TikTok for | Views e | App em Draft | Grátis |
| Developers | engajamento |   |   |
| YouTube Data | Views públicas | Criar chave no projeto | Grátis dentro da cota |
| API v3 |   | runeasy-production | diária |
| Resend | E-mails | Já configurado | Nenhum previsto |
|   | transacionais |   |   |
| Sentry / | Erros e funil do | Já usados | Nenhum previsto |
| PostHog | portal |   |   |


| Serviço | Para quê | Situação | Custo novo |
| --- | --- | --- | --- |
| AppsFlyer | Install exato e | Não contratado | Pago; só quando o |
| (fase 2) | deferred deep link |   | volume justificar |

## 11. Jobs, webhooks e observabilidade

Todo evento externo entra por webhook_inbox (idempotente) e é processado em fila com retry exponencial; só a validação de saque do Asaas responde síncrona, porque o Asaas espera a resposta no próprio POST.

| Job / endpoint | Gatilho | Função |
| --- | --- | --- |
| POST /webhooks/revenuecat | Evento | Grava inbox → fila → atribuição, |
|   |   | ledger, contadores |
| POST | ~5 s após cada Pix | Compara com |
| /webhooks/asaas/validate | criado | payout_requests e responde |
|   |   | APPROVED / REFUSED |
|   |   | (síncrono) |
| POST /webhooks/asaas/events | Evento | Status de transferência → |
|   |   | fecha o saque |
| GET /c/:slug | Clique | Grava click_events e |
|   |   | redireciona (precisa responder |
|   |   | rápido; gravação assíncrona) |
| Callbacks Meta (deauthorize, data | Evento | Apaga token e dados sociais do |
| deletion) |   | creator; devolve código de |
|   |   | confirmação |
| Sync social rápido | 1 h, 6 h, 24 h, 72 h e 7 | Snapshot de métricas do |
|   | d após publicar | conteúdo novo |
| Sync social lento | Diário até 30 dias | Conteúdo maduro |
| Sync de stories (Instagram) | A cada 2 h enquanto | Captura antes de sumir em 24 |
|   | o story está no ar | h (sem webhook neste |
|   |   | caminho) |
| Renovação de tokens | Diário | Instagram e TikTok; falha marca |
|   |   | "reconectar" e manda e-mail |
| Disponibilidade de comissão | Diário, 03:00 SP | pending → available |
|   |   | quando vence available_at |


| Job / endpoint | Gatilho | Função |
| --- | --- | --- |
| Impacto | De hora em hora | Atualiza impact_daily |
|   |   | (pagos, ativados, treinos, km) |
| Tiers e conquistas | Após o job de | Promove tier, desbloqueia |
|   | impacto | badges, progresso de missões |
| Fechamento do mês | Dia 1, 04:00 SP | Congela ranking, aplica |
|   |   | rebaixamentos, gera relatório |
|   |   | para a contabilidade |
| Reconciliação de Pix | De hora em hora | Saques sent há mais de 1 h |
|   |   | sem retorno → consulta o |
|   |   | Asaas |

Saúde das integrações no Manager Portal: último evento do RevenueCat recebido, fila do inbox, tokens a vencer ou revogados por rede, saques parados, erros das últimas 24 h (Sentry). Alerta por e-mail quando o RevenueCat ficar mais de 6 h sem evento em produção.

## 12. Segurança, fraude, LGPD e obrigações fiscais

O maior risco jurídico não é técnico: é pagar pessoa física sem documento fiscal e mostrar ao creator quem assinou. Os dois têm conserto simples se decididos antes do piloto.

## 12.1 Fraude

- Comissão só nasce de receita validada pelo RevenueCat; views nunca pagam nada.

- Autoindicação bloqueada pelo mesmo user_id ; mesma chave Pix em dois creators gera alerta.

- Um transaction_id gera no máximo um lançamento; rc_event_id único impede duplicata.

- Código sem desconto no v1: código com desconto vaza para sites de cupom, e desconto fora do mecanismo da loja já violou regra da Apple uma vez. Desconto por creator, se vier, é via offer codes da Apple/Google (fase 2).

- Primeiros saques de cada creator com revisão manual; acima de um teto, duas aprovações; taxa de estorno alta por creator bloqueia saque.

## 12.2 Segurança

- Papéis em staff_roles ; toda consulta do creators-api filtra por dono. MFA (TOTP do Supabase) obrigatório para finance e admin .


- Webhooks autenticados: RevenueCat (header + HMAC), Asaas ( asaas-access- token ), Meta deletion (assinatura com o app secret).

- Rate limit em login, /c/:slug e pedido de saque. audit_log em tudo que mexe em dinheiro, tier ou atribuição.

## 12.3 LGPD

- O creator vê só agregados. A tabela de transações mostra "Assinante #4821 · Plano Anual", nunca nome ou @.

- Nome e foto no Ranking só com consentimento nos termos do programa.

- CPF/CNPJ e chave Pix criptografados; IP de clique só como hash, apagado em 90 dias.

- Atualizar a política de privacidade do app: ela precisa dizer que a origem da assinatura (código/link de parceiro) é registrada e compartilhada em forma agregada.

- Desconectar rede ou pedido de exclusão da Meta/TikTok apaga token e dados vindos daquela plataforma.

## 12.4 Fiscal e regras do programa (validar com a contabilidade)

- Pagar MEI/PJ: o creator emite NFS-e e anexa no pedido de saque; o Pix só sai com a nota. É o caminho mais simples para o v1.

- Pagar pessoa física: exige recibo (RPA) com retenções e obrigações acessórias. Só abrir quando a contabilidade montar esse fluxo; o payout_profiles.person_type já separa os casos.

- Termos do programa cobrindo: base e % da comissão, prazo de travamento, estorno negativo, saque mínimo, prazo de pagamento, uso de imagem, encerramento.

- Todo conteúdo pago precisa de identificação de publicidade (#publi / parceria paga), conforme as regras do CONAR para influenciadores; isso entra no briefing de toda missão.

## 13. Roadmap e checklist

Quatro fases, cada uma só começa quando o portão anterior passa. Os trâmites (Asaas, Meta, termos) andam em paralelo desde o dia 1, porque são eles que mais demoram e não dependem de código. Durações são estimativas do PRD.


## Dinheiro primeiro: as redes entram sé depois do 12 Pix real

## Fase 2- Piloto

cerca de 2 semanas

Creative Hub, Projetos Ranking mensal saque Pix pelo Asaas 5a10 creators reais

de teste

certa

Conta PJ no Asaas + Railway Pro com IP fixo

da na Meta + Sandbox do TikTok empresa

Termos do programa + fluxo fiscal MEI/PJ

roadmap · 4 fases, 3 portões, 4 trâmites

A Fase 1 é a única em andamento; as revisões de Meta e TikTok só são enviadas na Fase 3, com o portal no ar.

## Fase 4 - Escala

continuo

e boosts

Conquistas Auto-aprovar saques AppsFlyer se preciso

G3 Meta e TikTok com Advanced Access

Revisdes Meta/TikTok

## Fase 1- Money Loop

2a3semanas

Schema + auth + papéis Links e Webhook RC + ledger

Geral e Earnings

gera a

Tramites em paralelo (comegar j&)

## Fase

2a4sem +

OAuth Instagram/TikTok Sync + Performance YouTube Stories por polling

G2-12Pix real pago e conciliado

## Checklist do que fazer agora (antes de escrever código):

- Confirmar no código do app que o app_user_id do RevenueCat é o user_id do Supabase.

- Fechar as 10 decisões da seção 14.

- Abrir a conta PJ no Asaas e uma conta no Sandbox; confirmar a tarifa de Pix enviado.

- Ligar Static Outbound IPs no serviço creators-api (Railway Pro) e anotar os 3 IPs.

- Retomar a Verificação da Empresa na Meta e trocar o app para o caminho Instagram Login.

- Criar o Sandbox do app TikTok e adicionar sua conta como usuário-alvo.

- Criar a página runeasy.com.br/creators (explica o programa, linka o portal): requisito do TikTok.

- Levar à contabilidade: pagamento MEI/PJ com NFS-e, termos do programa, atualização da política de privacidade do app.

## Portões, em detalhe:

- G1 — em staging, uma compra sandbox com código gera evento no inbox, trava a atribuição e lança a comissão com o valor calculado à mão; um estorno gera o lançamento negativo; o mesmo evento reenviado não duplica.


- G2 — um Pix real para um creator do piloto passou pelas quatro travas, voltou com TRANSFER_DONE e o saldo do portal bate com o extrato do Asaas.

- G3 — Advanced Access concedido para as duas permissões do Instagram e app TikTok aprovado; só então o botão "Conectar" aparece para todos.

## 14. Decisões pendentes e riscos

Dez decisões precisam estar fechadas antes da Fase 1, porque mudam schema, termos ou telas. A recomendação de cada uma está na última coluna.

| # | Decisão | Opções | Recomendação |
| --- | --- | --- | --- |
| 1 | Nomes e % dos | PRD: Creator 25% / Partner | Escada do PRD em todas as |
|   | níveis | 30% / Ambassador 35% / | telas |
|   |   | Elite 40%. Telas: Partner |   |
|   |   | 30% → Elite 40% no |   |
|   |   | Earnings, mas Elite / Pro / |   |
|   |   | Advanced no Ranking |   |
| 2 | O que sobe de nível | Assinantes pagos ou | Pagos para dinheiro; ativados |
|   |   | corredores ativados | como requisito extra |
| 3 | Duração da comissão | 12 meses, vida toda, N | 12 meses |
|   | por assinante | renovações |   |
| 4 | Quando pending | Janela fixa ou fechamento | Fechamento mensal, |
|   | vira available | mensal | liberação no dia 10 |
| 5 | Saque mínimo | R$ 50 ou R$ 100 | R$ 50 (depende da tarifa Pix |
|   |   |   | do Asaas) |
| 6 | Quem pode receber | Só MEI/PJ, ou também PF | Só MEI/PJ no piloto |
|   | no v1 |   |   |
| 7 | Caminho do | Facebook Login (exige | Instagram Login |
|   | Instagram | Página) ou Instagram Login |   |
| 8 | País dos creators no | Só Brasil ou internacional | Só Brasil (Pix) |
|   | v1 |   |   |
| 9 | AppsFlyer | Agora ou depois do piloto | Depois, se o piloto mostrar |
|   |   |   | perda de atribuição |
| 10 | Teto para dupla | Valor em R$ | Definir com o financeiro; |
|   | aprovação de saque |   | sugestão R$ 1.000 |

## Riscos e mitigação


- inteira. É a primeira coisa a confirmar no código do app. app_user_id do RevenueCat diferente do user_id do Supabase quebra a atribuição

- Atribuição só por código subestima o creator (quem clica e esquece o código). Mitigação: página-ponte com o código em destaque, creator sempre falando o código no vídeo, AppsFlyer na fase 2.

- Meta ou TikTok recusam ou demoram. O Money Loop não depende deles; até aprovar, métricas sociais entram por print revisado pelo marketing.

- Service role do creators-api enxerga o banco do app. Mitigação: o serviço só lê fora do schema workouts por uma view agregada criada para ele; revisão de código em toda query creators .

- IP fixo do Railway pode ser compartilhado com outros clientes. A whitelist ajuda, mas a trava real é o webhook de validação de saque.

- Métricas do Instagram marcadas "em desenvolvimento" ( views , a versão da API junto do snapshot. total_interactions ) podem mudar de definição. Gravar sempre o nome da métrica e

## 15. Fontes

Páginas oficiais consultadas em 22/10/2026.

- Instagram Media Insights — referência (v25.0): métricas por tipo de mídia, o que é exclusivo do Facebook Login, limites de stories [URL 🔗](https://developers.facebook.com/docs/instagram-platform/reference/instagram-media/insights/)

- Instagram API with Instagram Login: sem Página do Facebook, scopes instagram_business_* [URL 🔗](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-instagram-login)

- [Blog Meta: insights no Instagram Login (mar/2025)](https://developers.facebook.com/blog/post/2025/03/24/user-and-media-insights-on-instagram-api-with-instagram-login/)

- Instagram Platform — overview e níveis de acesso: Standard × Advanced, Verificação da Empresa [URL 🔗](https://developers.facebook.com/docs/instagram-platform/overview)

- App Review for Instagram API: um login por app, 1 chamada bem-sucedida antes de pedir acesso [URL 🔗](https://developers.facebook.com/docs/instagram-platform/app-review)

- TikTok — Query Videos: campos e limite de 20 IDs [URL 🔗](https://developers.tiktok.com/doc/tiktok-api-v2-video-query)

- TikTok — Display API get started: user.info.basic + video.list [URL 🔗](https://developers.tiktok.com/doc/display-api-get-started/)

- TikTok — App Review Guidelines: site não pode ser landing/login, domínio do vídeo [URL 🔗](https://developers.tiktok.com/doc/app-review-guidelines)

- TikTok — App Review FAQ: prazo da revisão [URL 🔗](https://developers.tiktok.com/doc/getting-started-faq)

- [Asaas — Validação de saque via webhooks](https://docs.asaas.com/docs/mecanismo-para-validacao-de-saque-via-webhooks)

- Asaas — Whitelist de IPs: evento crítico em saques [URL 🔗](https://docs.asaas.com/docs/whitelist-de-ips)

- [Railway — Static Outbound IPs](https://docs.railway.com/networking/static-outbound-ips)

- RevenueCat — Webhooks: várias integrações por projeto, assinatura HMAC [URL 🔗](https://www.revenuecat.com/docs/integrations/webhooks)

- [RevenueCat — Event Types and Fields](https://www.revenuecat.com/docs/integrations/webhooks/event-types-and-fields)


- PRD interno: RunEasy Creator Platform — Product & Technical Blueprint v1.0 (11/08/2026)
