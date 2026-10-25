# RunEasy Creators --- Product Requirements Document (PRD) {#runeasy-creators--product-requirements-document-prd}

**Versão:** 1.0\
**Data:** 25 de outubro de 2026\
**Status:** Draft para validação\
**Produto:** RunEasy Creators\
**Documentos-fonte:** *RunEasy Creator Platform Blueprint* (v1.0, 11
ago. 2026), *RunEasy Creators --- Arquitetura Técnica* (out. 2026) e
screenshots fornecidos.

> Este PRD consolida os requisitos identificáveis nos materiais
> fornecidos. Não substitui a arquitetura técnica. Valores, nomes e
> métricas exibidos nos screenshots são exemplos visuais, não dados
> reais. Regras comerciais, financeiras e jurídicas ainda não aprovadas
> estão marcadas como pendentes; agentes de desenvolvimento não devem
> inventá-las.

## 1. Resumo executivo {#1-resumo-executivo}

RunEasy Creators é uma plataforma web para administrar o programa de
creators, embaixadores e afiliados da RunEasy. O produto conecta
aquisição de usuários, impacto real na corrida, produção de conteúdo e
remuneração em um sistema rastreável.

Creators devem conseguir entender quantas pessoas foram atribuídas a
eles, quantas começaram efetivamente a correr, quantos
cliques/trials/assinaturas geraram, quanto de comissão acumularam, o que
está pendente ou disponível e o que precisam fazer para evoluir de
nível. A equipe RunEasy precisa gerenciar creators, conteúdo, campanhas,
missões, projetos, comissões, pagamentos e integrações em um backoffice.

A primeira versão deve validar o **Money Loop**: convite/login → link ou
código → atribuição → compra/renovação → comissão → dashboard →
pagamento. O lançamento financeiro inicial não pode depender de
aprovação das APIs do Instagram ou TikTok. Métricas sociais, automação
de pagamentos e gamificação avançada entram por fases.

## 2. Problema e oportunidade {#2-problema-e-oportunidade}

Sem um sistema próprio, a operação corre o risco de depender de
planilhas, apuração manual e regras pouco auditáveis. Os problemas
principais são:

-   atribuição inconsistente de usuários aos creators;
-   falta de transparência sobre cálculo e status das comissões;
-   dificuldade de distinguir alcance social de aquisição e ativação
    reais;
-   operação fragmentada de creators, campanhas, conteúdo, projetos e
    pagamentos;
-   dependência de permissões e limites de APIs de terceiros;
-   risco de fraude, pagamento duplicado e exposição de dados pessoais.

## 3. Visão, proposta de valor e princípios {#3-visão-proposta-de-valor-e-princípios}

**Visão:** transformar creator marketing em um canal mensurável e
recorrente de crescimento para a RunEasy.

**Proposta para creators:** criar, inspirar, gerar impacto e crescer com
a RunEasy.

**Proposta para a equipe:** identificar os creators e conteúdos que
geram corredores e assinaturas com margem positiva.

### Princípios

1.  Impacto antes de métricas de vaidade.
2.  Dinheiro transparente, reproduzível e auditável.
3.  Atribuição previsível e explicada ao usuário.
4.  Privacidade por padrão: creators veem dados agregados, não a
    identidade dos assinantes.
5.  APIs sociais não são fonte da verdade financeira.
6.  Falhas de APIs sociais não interrompem atribuição, comissões ou
    pagamentos.
7.  Tiers e recompensas usam critérios objetivos e versionados.
8.  Nenhum score, insight ou valor deve ser apresentado como calculado
    sem fonte e metodologia.
9.  Validar o canal com um piloto antes de investir em automação e
    gamificação avançadas.

## 4. Objetivos e métricas {#4-objetivos-e-métricas}

### Objetivos de negócio

-   Validar creators como canal de aquisição previsível e economicamente
    sustentável.
-   Reduzir custo operacional da apuração de comissões.
-   Aumentar ativação e retenção de creators com feedback, projetos,
    missões e progressão.
-   Identificar conteúdo que gera corredores pagos e ativados, não
    apenas views.
-   Permitir escala sem reescrever o núcleo financeiro.

### North Star Metric

**Novos corredores ativados atribuídos a creators com margem de
contribuição positiva.**

Definição proposta de corredor ativado: usuário atribuído a um creator
que concluiu seu primeiro treino/corrida registrado no app. Janela
temporal, reativações e cálculo de margem devem ser aprovados antes de
uso executivo.

### KPIs

  KPI                          Definição proposta
  ---------------------------- ------------------------------------------------------------------------------
  Ativação de creators         \% dos convidados que concluem onboarding e recebem link/código ou publicam
  Tempo até ativação           Dias entre convite e primeira ação de divulgação
  Cliques                      Cliques registrados nos links RunEasy
  Click-to-paid                Assinantes pagos atribuídos ÷ cliques elegíveis, com limitações explicitadas
  Novos pagos                  Usuários com primeira compra paga confirmada e atribuída
  Corredores ativados          Usuários atribuídos que completaram o primeiro treino
  Creator CAC                  Comissões + bônus + custo operacional alocado ÷ novos pagos
  Contribution after creator   Proceeds menos comissão e custos variáveis definidos
  Retenção 30/60/90 dias       Retenção por coorte de creator
  Retenção de creators         Creators ativos após 30/60/90 dias
  Tempo até payout             Tempo entre fechamento/aprovação e pagamento concluído
  Refund/fraude                Estornos, autoindicações e ajustes por creator
  Concentração do canal        Participação dos top 5 creators nos resultados

As metas numéricas devem ser definidas após o piloto; exemplos no
blueprint não são compromissos de desempenho.

## 5. Escopo {#5-escopo}

### Dentro do produto

-   Convites, autenticação, onboarding e perfis.
-   Papéis de acesso e backoffice.
-   Cadastro de creators, tiers, códigos e links.
-   Tracking de cliques e atribuição.
-   Eventos de assinatura, renovação e refund via RevenueCat.
-   Motor de comissão e ledger.
-   Painel de ganhos, histórico e pagamentos Pix.
-   Dashboard de impacto e desempenho.
-   Content Hub, projetos privados, briefings, entregáveis e feedback.
-   Missões, ranking, níveis e conquistas.
-   Métricas sociais via APIs oficiais, quando aprovadas, e fallback
    manual.
-   Métricas agregadas de impacto vindas do app.
-   Notificações, auditoria, antifraude, observabilidade e privacidade.

### Fora do MVP

-   Substituir RevenueCat ou Superwall.
-   Processar a assinatura do app na plataforma de creators.
-   Split automático no pagamento original da App Store/Google Play.
-   Tratar a plataforma como carteira financeira.
-   Scraping de redes sociais.
-   Publicação automática de conteúdo nas redes.
-   Exibir identidade ou histórico individual dos assinantes.
-   AppsFlyer/OneLink antes de haver justificativa econômica.
-   Analytics avançado de YouTube.
-   Usar views/likes como base direta para comissão.

## 6. Personas e permissões {#6-personas-e-permissões}

### Creator

Configura perfil, recebe link/código, acompanha métricas próprias,
conecta suas redes, vincula conteúdo, participa de missões/projetos,
consulta comissões e solicita payout.

Não pode ver dados pessoais de assinantes, alterar comissão/saldo,
aprovar seu próprio pagamento ou acessar tokens.

### Marketing Manager

Convida e acompanha creators, publica ideias, cria projetos e missões,
acompanha resultados e oferece feedback. Não altera pagamentos
liquidados sem permissão financeira específica.

### Finance

Revisa dados de pagamento, aprova/rejeita saques, reconcilia
transferências, registra comprovantes e trata exceções. MFA obrigatório
no desenho proposto.

### Admin

Gerencia papéis, regras, tiers e integrações. Ações financeiras críticas
devem respeitar segregação de funções.

### Regras de autorização

-   Negar por padrão.
-   Toda rota sensível valida papel e propriedade no servidor.
-   Ocultar um botão na interface não é controle de segurança.
-   Alterações de tier, regras, payout e ledger geram audit log.
-   MFA obrigatório para admin e finance.

## 7. Jornada principal do creator {#7-jornada-principal-do-creator}

1.  Recebe convite individual.
2.  Ativa conta e aceita termos/regras do programa.
3.  Preenche perfil e dados exigidos para pagamentos.
4.  Opcionalmente conecta Instagram/TikTok; isso não bloqueia o
    link/código.
5.  Recebe slug, link, código e QR code se disponível.
6.  Conhece impacto, desempenho, ganhos e níveis.
7.  Acessa biblioteca, projetos e missões.
8.  Publica e vincula conteúdo a projeto/missão.
9.  Acompanha cliques, assinaturas, impacto e comissões.
10. Solicita pagamento quando elegível e acompanha o status.

## 8. Requisitos funcionais {#8-requisitos-funcionais}

Prioridades: **P0** = necessário para validar Money Loop; **P1** =
operação/retention; **P2** = evolução posterior.

### RF-01 --- Convites e autenticação (P0) {#rf-01--convites-e-autenticação-p0}

Acesso por convite, sem cadastro público aberto no MVP; login seguro;
recuperação de senha; expiração/revogação de convites; revogação de
acesso; aceite de termos com versão registrada.

**Aceite:** convite válido ativa conta; expirado/inválido é rejeitado;
rota protegida exige autenticação; equipe pode revogar acesso.

### RF-02 --- Onboarding e perfil (P0) {#rf-02--onboarding-e-perfil-p0}

Coletar dados mínimos do programa; solicitar dados fiscais/pagamento
apenas quando necessários; editar perfil; mostrar estado de verificação;
conectar/desconectar redes. Desconectar deve invalidar autorização e
eliminar tokens conforme política.

**Aceite:** onboarding funciona sem conectar redes; dados financeiros
não são visíveis a outros creators.

### RF-03 --- Gestão de creators e tiers (P0) {#rf-03--gestão-de-creators-e-tiers-p0}

Staff autorizado cria, convida, ativa, pausa e bloqueia creators. Cada
creator tem ID, slug, código e tier. Histórico de tier é preservado;
mudança manual exige justificativa.

**Aceite:** slug/código únicos; comissão histórica continua explicável
após mudança de tier.

### RF-04 --- Links e tracking (P0) {#rf-04--links-e-tracking-p0}

Links por creator e, quando aplicável, por placement/conteúdo/campanha;
registro de click ID, link, creator e timestamp; redirecionamento
rápido; código copiável; QR opcional; metadados mínimos e retenção
definida. Não usar fingerprinting por IP/browser para reconstruir
instalações.

**Aceite:** clique é associado ao link correto; links inválidos não
criam atribuições; falha de tracking não bloqueia o redirect
indefinidamente.

### RF-05 --- Atribuição (P0) {#rf-05--atribuição-p0}

A atribuição é guardada no backend e é fonte da verdade interna. Regras
propostas:

1.  Código explicitamente digitado tem prioridade.
2.  Sem código, usar último link elegível dentro de janela proposta de
    30 dias.
3.  No primeiro pagamento, bloquear troca pelo período proposto de 12
    meses.
4.  Renovação não troca creator.
5.  Mudança manual somente com papel autorizado, motivo e audit log.
6.  Bloquear autoindicação.
7.  Não inferir atribuição perdida por fingerprinting.

**Pendente:** confirmar janela, período de lock, conflito código/link,
reatribuição pré-compra e usuários preexistentes.

**Aceite:** uma atribuição ativa por usuário conforme regra; requisição
repetida não duplica; renovação não altera atribuição travada.

### RF-06 --- RevenueCat (P0) {#rf-06--revenuecat-p0}

Receber eventos relevantes de compra inicial, renovação, cancelamento,
expiração, refund e cobrança problemática. Validar origem, persistir
evento antes do processamento, processar de forma assíncrona e
idempotente, separar sandbox/produção e reconciliar com API quando
necessário.

**Aceite:** evento repetido não duplica comissão; evento inválido não
altera ledger; falhas são observáveis e reprocessáveis.

### RF-07 --- Motor de comissão (P0) {#rf-07--motor-de-comissão-p0}

Calcular exclusivamente no backend. Base proposta: proceeds da
transação, considerando os dados válidos de preço, impostos e taxas de
loja do RevenueCat. Aplicar tier/regra vigente e bônus temporário
aprovado. Views/likes não geram comissão. Refund gera ajuste negativo.
Dinheiro em centavos inteiros, nunca float. Registrar evento, base,
percentual, valor, moeda e versão da regra.

Hipótese de tiers do blueprint:

-   Creator Sprint: 25% dos proceeds.
-   Partner: 30%.
-   Ambassador: 35%.
-   Elite: 40%.

Percentuais, critérios de promoção, eventos elegíveis, renovação e
política de refund são **pendentes de aprovação**. R\$ 24,90 e taxas
agregadas de 25% são apenas exemplo econômico.

**Aceite:** mesmo input e versão de regra produzem o mesmo cálculo;
nenhuma transação cria lançamentos duplicados; ajustes não apagam
histórico; mudança de tier não recalcula automaticamente passado.

### RF-08 --- Ledger e saldo (P0) {#rf-08--ledger-e-saldo-p0}

Saldo é soma de lançamentos, nunca campo mutável fonte da verdade.
Tipos: accrual, refund adjustment, bônus, payout e ajuste manual.
Estados: pendente, disponível, pago e revertido/ajustado. Ajuste manual
exige permissão, motivo e audit log.

**Aceite:** totalizadores batem com o ledger; correções são lançamentos
compensatórios; payout concluído não é contabilizado duas vezes;
histórico não expõe identidade de assinante.

### RF-09 --- Pagamento Pix (P0 manual; automação depois) {#rf-09--pagamento-pix-p0-manual-automação-depois}

MVP: cadastrar dados necessários, validar saldo e mínimo, solicitar
saque, aprovar/rejeitar, pagar manualmente e registrar
comprovante/reference. Evolução: Asaas API, referência externa
idempotente, webhooks de status e reconciliação antes de retry. Falha
não consome definitivamente saldo.

**Pendente:** mínimo de saque (R\$ 50/R\$ 100 são exemplos), calendário,
hold period, requisitos PF/MEI/PJ, retenções e aprovação dupla.

**Aceite:** não saca mais que saldo elegível; não compromete saldo duas
vezes; falha/rejeição segue regra definida; reprocessamento não duplica
Pix.

### RF-10 --- Visão Geral (P0) {#rf-10--visão-geral-p0}

Mostrar impacto, progresso de nível, novos corredores, assinantes
ativos, comissões, CTR/funil quando disponível, desempenho semanal,
conteúdo em destaque e missão sugerida. "Começou a correr" só pode
contar usuários que atendam à definição de corredor ativado.

**Aceite:** período/fonte claros; estado vazio e indisponibilidade
diferenciados; métricas agregadas não revelam indivíduos.

### RF-11 --- Performance por conteúdo (P1) {#rf-11--performance-por-conteúdo-p1}

Exibir views, engagement, cliques, trials, pagos, proceeds e comissão
atribuída quando há vínculo e fonte válidos. Filtros por período, série
temporal e exportação autorizada. Distinguir dados de API, manuais,
estimados e indisponíveis. "Installs" deve ser qualificado se for
aproximação.

### RF-12 --- Instagram/TikTok/YouTube (P1, dependente de aprovação) {#rf-12--instagramtiktokyoutube-p1-dependente-de-aprovação}

Instagram: conta profissional via Instagram Login, mídia/insights
permitidos, snapshots, polling para Stories quando aplicável, tokens
protegidos/renovados, fallback de screenshot revisado. TikTok: OAuth
oficial, scopes aprovados, vídeos próprios e métricas permitidas,
seleção manual do vídeo correto, tratamento de consentimento parcial e
token expirado. YouTube: estatísticas públicas em fase posterior;
analytics aprofundado fora do MVP. Sem scraping. Falha social não
bloqueia Money Loop. Exibir última sincronização e estado.

**Aceite:** conectar/desconectar funciona; falha não afeta
comissão/payout; token revogado solicita reconexão; provas manuais têm
estado e trilha de revisão.

### RF-13 --- My Link (P0) {#rf-13--my-link-p0}

Mostrar link, código, QR quando disponível, links por
origem/campanha/conteúdo e cliques por período. Explicar que clique não
equivale a install ou compra.

### RF-14 --- Content Hub (P1) {#rf-14--content-hub-p1}

Equipe publica ideias, hooks, estrutura, roteiro, CTA, tags, referências
e assets. Creator salva e marca como pretende produzir/produziu. Status
editorial pode ser experimental/promissor/comprovado/fatigado. Score
"alta conversão" só quando houver dados e metodologia; antes disso,
rotular como curadoria editorial. Assets devem ter licença comercial
apropriada.

### RF-15 --- Projetos privados (P1) {#rf-15--projetos-privados-p1}

Projetos com objetivo, status, prazo, briefing, assets, entregáveis,
feedback e conteúdo associado. Creator vê somente projetos destinados a
ele ou compartilhados explicitamente. Estados sugeridos: não iniciado,
em produção, enviado para revisão, aprovado, publicado/rejeitado.

### RF-16 --- Missões e recompensas (P1/P2) {#rf-16--missões-e-recompensas-p1p2}

Missão contém objetivo, janela, elegibilidade, brief, plataforma,
entregáveis, CTA, assets, recompensa e critério de conclusão. Pode
segmentar por tier/nicho/plataforma. Recompensa percentual deve virar
regra versionada com vigência; não alterar retroativamente comissão
consolidada sem ajuste auditável.

### RF-17 --- Ranking, tiers e conquistas (P1/P2) {#rf-17--ranking-tiers-e-conquistas-p1p2}

Ranking mensal por novos pagos/ativados, conforme regra aprovada;
leaderboard histórico opcional; Rising Creators para recém-ingressos;
conquistas com critérios e progresso visíveis. Não mostrar
receita/comissão de terceiros.

**Pendente:** definir métricas, janelas, promoção/rebaixamento,
desempate e prevenção de manipulação.

### RF-18 --- Impacto agregado (P1) {#rf-18--impacto-agregado-p1}

Agregar novos pagos, corredores ativados/ativos, treinos e quilômetros.
O creator recebe apenas contagens agregadas; não vê nome, e-mail,
localização ou histórico individual sem consentimento explícito e base
legal adequada. Definir exclusões, atrasos e treinos inválidos.

### RF-19 --- Backoffice marketing (P0 básico; P1 completo) {#rf-19--backoffice-marketing-p0-básico-p1-completo}

Listar creators, estado, tier, conexões, links, performance e alertas;
convidar/pausar/bloquear; administrar conteúdo/projetos/missões;
pipeline de recrutamento; filtros por período, tier, plataforma,
campanha e status.

### RF-20 --- Backoffice financeiro (P0) {#rf-20--backoffice-financeiro-p0}

Consultar ledger, refunds, saldos e pedidos; aprovar/rejeitar com
motivo; registrar comprovantes; permitir ajustes só a papéis
autorizados; exportar fechamento/reconciliação; alertar sobre eventos
duplicados e saques parados.

### RF-21 --- Notificações (P1) {#rf-21--notificações-p1}

Convites, missões, prazos, feedback, conquistas, mudanças de tier,
comissão disponível, status de payout e reconexão social. Preferências e
limites devem evitar excesso e vazamento de informação.

### RF-22 --- Saúde operacional (P0) {#rf-22--saúde-operacional-p0}

Monitorar último webhook RevenueCat, fila/retries, tokens, payouts sem
resposta e erros. Alertar integrações críticas sem eventos por período
aprovado. Diferenciar erro do provedor, autorização e falha interna.

### RF-23 --- Privacidade/exclusão (P0) {#rf-23--privacidadeexclusão-p0}

Termos e políticas devem refletir os fluxos reais. Explicar finalidade
das permissões sociais, permitir desconexão e processar solicitações de
exclusão. Definir retenção para cliques, tokens, snapshots, payloads e
dados financeiros. Cumprir obrigações de retenção legal quando
aplicável.

## 9. Requisitos não funcionais {#9-requisitos-não-funcionais}

-   **Segurança:** segredos/tokens somente server-side; tokens sociais
    criptografados; MFA para finance/admin; autorização server-side;
    rate limits; proteção contra CSRF/XSS/SQL injection; privilégio
    mínimo; ambientes separados.
-   **Integridade financeira:** centavos inteiros; ledger imutável;
    idempotência em eventos e transferências; reconciliação antes de
    retry ambíguo; regras versionadas.
-   **LGPD:** minimização, finalidade, retenção documentada, acesso
    restrito, suporte a solicitações de titulares e logs sem dados
    sensíveis.
-   **Confiabilidade:** persistir webhooks antes de processar; retry com
    backoff; jobs reexecutáveis com segurança; falhas sociais isoladas
    do Money Loop.
-   **Performance:** redirect rápido; telas responsivas; consultas
    agregadas/índices; metas numéricas de latência e volume definidas
    após piloto.
-   **Acessibilidade:** contraste, foco, teclado, formulários rotulados,
    mensagens compreensíveis e estados explícitos.
-   **Observabilidade:** logs estruturados, erros, métricas operacionais
    e audit log; nunca registrar tokens, chaves Pix completas ou
    CPF/CNPJ completo em logs comuns.

## 10. Regras de negócio centrais {#10-regras-de-negócio-centrais}

1.  RevenueCat fornece eventos financeiros; o sistema RunEasy define
    atribuição, regra, ledger e payout.
2.  Atribuição fica no backend e não muda em renovações após lock.
3.  Comissão é calculada no backend sobre proceeds elegíveis.
4.  Views e likes nunca geram comissão.
5.  Ledger é a fonte de verdade; correções são lançamentos
    compensatórios.
6.  Refund cria ajuste negativo conforme termos aprovados.
7.  Payout exige saldo elegível, aprovação e reconciliação.
8.  Evento/transação/transferência não pode ter efeito financeiro
    duplicado.
9.  Creator não vê identidade de assinantes.
10. Mudanças de tier/recompensa têm vigência e histórico.
11. Sem MMP, atribuição pós-instalação pode exigir código explícito;
    comunicar limitações.
12. Métricas sociais não são fonte da verdade financeira.

## 11. Telas e experiência {#11-telas-e-experiência}

Os screenshots são referência conceitual, não especificação
pixel-perfect.

-   **Login:** marca, e-mail/senha, recuperação, acesso por convite,
    termos/privacidade.
-   **Visão Geral:** impacto, progresso de nível, novos corredores,
    assinantes, ganhos, CTR/funil, conteúdo e missão.
-   **Performance:** período, views/cliques/trials/pagos/comissão,
    gráfico, funil e tabela por conteúdo.
-   **Content Hub/Ideia:** hook, estrutura, CTA, referência, tags,
    assets e ações salvar/produzir.
-   **Missões:** disponíveis/aceitas/concluídas, prazo, entregáveis,
    reward e progresso.
-   **Projetos:** briefing, assets, prazo, entregáveis, status e
    feedback privado.
-   **Ranking/Conquistas:** posição, métrica, critérios e progresso, sem
    ganhos de terceiros.
-   **Earnings:** pendente/disponível/pago, ledger, percentual,
    histórico e solicitação de pagamento.
-   **My Link:** links, código, QR e cliques por origem.
-   **Profile/Settings:** perfil, conexões, pagamento, notificações,
    privacidade e desconexão.
-   **Backoffice:** creators, pipeline, conteúdo, projetos, missões,
    performance, financeiro, regras, integrações e auditoria.

### Direção visual

Base branca/off-white, tipografia preta, ciano RunEasy (aprox.
`#00D4FF`), cards com bordas discretas e poucos efeitos, destaques
escuros pontuais, gráficos simples e layout responsivo. Corrigir
inconsistências de nomes de tiers e rótulos nos protótipos. Números
devem ter fonte real; score sem dados deve ser editorial; "installs" não
deve ser confundido com clique; saldo não deve ser chamado de carteira
financeira.

## 12. Roadmap {#12-roadmap}

### Fase 0 --- Protótipo e validação {#fase-0--protótipo-e-validação}

Validar fluxo com 3--5 creators; prototipar login, dashboard, profile,
link, earnings, projetos e ranking; testar entendimento de impacto e
comissões.

### Fase 1 --- Money Loop {#fase-1--money-loop}

Para piloto de 5--10 creators:

-   auth/roles e convites;
-   creators, tier, slug, código e links;
-   redirects e cliques;
-   atribuição;
-   webhook RevenueCat idempotente;
-   motor de comissão e ledger;
-   dashboard básico;
-   backoffice creator/finance;
-   payout manual Pix com comprovante;
-   segurança, alertas, auditoria e runbooks.

**Saída:** rastrear usuário → compra → comissão única → saldo correto →
pagamento registrado, sem divergência não explicada.

### Fase 2 --- Social Intelligence {#fase-2--social-intelligence}

Content Hub, projetos, vínculo de posts, screenshots de fallback,
Instagram/TikTok após aprovação, performance por conteúdo e jobs de
sincronização.

### Fase 3 --- Automação de payout {#fase-3--automação-de-payout}

Asaas, status/retry/reconciliação, validação de titularidade, exportação
contábil e limites de aprovação.

### Fase 4 --- Gamificação {#fase-4--gamificação}

Missões, recompensas, achievements, ranking, share cards e insights de
conteúdo com metodologia validada.

### Fase 5 --- Escala {#fase-5--escala}

Avaliar AppsFlyer/OneLink, YouTube Analytics e mudanças de capacidade
com base em volume e economia reais.

## 13. Critérios globais de pronto {#13-critérios-globais-de-pronto}

-   Critérios de aceite testados.
-   Permissões testadas com usuários autorizados e não autorizados.
-   Estados de carregamento, erro, vazio e sucesso tratados.
-   Testes de idempotência, retry, refund e falhas financeiras.
-   Logs e monitoramento sem segredos/dados sensíveis.
-   Integrações testadas em sandbox.
-   Audit trail em mudanças críticas.
-   Documentação de produto e operação atualizada.
-   Nenhuma exposição de dados pessoais de assinantes.
-   Comportamento consistente com PRD e arquitetura aprovados.

## 14. Riscos e mitigação {#14-riscos-e-mitigação}

  Risco                               Mitigação
  ----------------------------------- ---------------------------------------------------------------------------------------------
  Perda de atribuição na instalação   Código explícito, deep links quando possível, limitações claras, avaliar MMP após validação
  Webhook duplicado/fora de ordem     Inbox idempotente, constraints únicas, retry seguro e reconciliação
  Refund após payout                  Termos claros e ajuste compensatório auditável
  API social não aprovada             Money Loop independente e fallback manual
  Token expirado                      Renovação, alerta e reconexão
  Pix duplicado                       Referência externa, reconciliação e aprovação humana
  Fraude/autoindicação                Bloqueio, alertas e revisão de saques
  Vazamento de dados                  Minimização, RBAC, segregação, logs redigidos e agregação
  Tier pouco claro                    Regras publicadas e histórico de alterações
  Score sem dados                     Rótulo editorial até haver amostra/metodologia
  Incerteza fiscal/jurídica           Validação profissional antes de escalar payouts
  Dependência de poucos creators      Monitorar concentração e diversificar programa

## 15. Decisões pendentes {#15-decisões-pendentes}

### Bloqueiam Money Loop

1.  Confirmar correspondência entre `app_user_id` e identidade enviada
    ao RevenueCat, inclusive compra anônima pré-login.
2.  Aprovar regras de atribuição: código/link, janela de 30 dias, lock
    de 12 meses, usuários existentes e reatribuição.
3.  Aprovar tiers, base de proceeds, eventos elegíveis, renovação,
    trials, refunds e mudanças de tier.
4.  Definir hold period e momento de `pending` → `available`.
5.  Definir mínimo, calendário, aprovação e falha de payout.
6.  Validar documentos fiscais, PF/MEI/PJ, titularidade Pix e termos com
    contador/jurídico.
7.  Definir segregação e dupla aprovação financeira.

### Antes do piloto

8.  Definir critérios de tier, rebaixamento, janelas e desempate.
9.  Formalizar corredor ativado, corredor ativo, assinatura ativa,
    clique elegível e margem.
10. Definir política de refund após payout.
11. Definir retenção de cliques, payloads, snapshots, tokens, logs e
    provas.
12. Definir política de conteúdo e licenças.
13. Confirmar permissões e limitações reais das APIs sociais.
14. Definir canais de notificação e formato de exportação contábil.

### Após validação

15. Critério econômico para AppsFlyer.
16. Demanda para YouTube Analytics avançado.
17. Política de missões/recompensas.
18. Fórmula e amostra mínima de scores de conteúdo.
19. Critérios para automação de payout.

**Regra:** o agente de desenvolvimento deve registrar divergências e
solicitar decisão, não inventar valores comerciais, jurídicos ou
financeiros.

## 16. Dependências externas {#16-dependências-externas}

RevenueCat; app RunEasy e seus eventos agregados de treino; Supabase;
Asaas; Meta/Instagram; TikTok for Developers; YouTube Data API em fase
posterior; serviços de hospedagem e observabilidade definidos na
arquitetura; aprovação jurídica/contábil. O cronograma de revisão das
APIs sociais é incerto e não pode bloquear o Money Loop.

## 17. Dados e analítica {#17-dados-e-analítica}

Entidades de produto esperadas: creators/perfis/status/tiers; perfis de
payout; papéis; contas sociais; links/clicks; atribuições; eventos de
assinatura; regras e bônus; ledger; payouts; posts/snapshots/provas;
biblioteca; projetos/assets/entregáveis/mensagens; missões/progresso;
conquistas; agregados de impacto; inbox de webhooks; audit log.

Cada métrica precisa de definição, fonte, período, atualização,
limitações e tratamento de dados ausentes. O frontend não é fonte de
verdade financeira.

Instrumentar eventos como convite enviado/aceito, onboarding concluído,
link copiado, conexão social, conteúdo salvo, missão aceita/concluída,
entregável enviado e payout solicitado/concluído. Não enviar CPF/CNPJ,
chave Pix, tokens ou e-mails completos a ferramentas analíticas.

## 18. Estratégia de testes e piloto {#18-estratégia-de-testes-e-piloto}

Testar:

-   convite válido/inválido, autenticação e autorização;
-   código, link, conflito, usuário preexistente e autoindicação;
-   compra inicial, renovação, cancelamento, refund e evento duplicado;
-   mudança de tier, hold period e refund pós-payout;
-   saldo insuficiente, payout duplicado, rejeição, falha de API e
    webhook tardio;
-   acesso cruzado entre creators;
-   token social expirado/revogado e consentimento parcial.

Piloto recomendado: 5--10 creators, métricas sociais manuais no início e
conciliação de uma amostra de compras, comissões e pagamentos. Medir
divergências, tempo operacional e dúvidas antes de escalar.

## 19. Documentos complementares necessários {#19-documentos-complementares-necessários}

O PRD é necessário, mas não suficiente sozinho. Conjunto recomendado:

1.  **PRD:** objetivos, escopo, usuários, requisitos, aceite, métricas e
    decisões pendentes.
2.  **Blueprint:** visão, jornadas, conceitos, hipóteses e roadmap.
3.  **Arquitetura técnica:** stack, componentes, limites de confiança,
    dados, integrações e deploy.
4.  **Especificações funcionais por fluxo:** estados, erros, permissões
    e casos de borda.
5.  **Regras financeiras versionadas:** tiers, proceeds, refunds, hold
    period e payouts.
6.  **Design system/UI:** tokens, componentes, responsividade,
    acessibilidade e estados.
7.  **Modelo de dados/contratos:** entidades, constraints, eventos,
    payloads e idempotência.
8.  **Plano de segurança/privacidade:** RBAC, threat model, secrets,
    retenção, exclusão e auditoria.
9.  **Plano de testes/release:** cobertura, staging, rollback e
    checklist de produção.
10. **Runbooks:** RevenueCat, webhook, payout falho, token revogado e
    exclusão.

Não é obrigatório escrever tudo extensamente antes de qualquer código.
Mas regras que afetam dinheiro, atribuição, identidade, privacidade e
autorização precisam estar decididas antes de implementar os fluxos
correspondentes.

## 20. Critério de início {#20-critério-de-início}

Pode começar a implementação da Fase 0 quando:

-   PRD, blueprint e arquitetura estiverem versionados no repositório;
-   a hierarquia dos documentos estiver clara;
-   decisões críticas estiverem aprovadas ou explicitamente bloqueadas;
-   fluxos P0 tiverem critérios testáveis;
-   houver backlog inicial priorizado;
-   ambientes e gestão de segredos estiverem planejados;
-   houver processo de revisão, testes, segurança e aprovação humana.

Antes de produção financeira, concluir validação jurídica/contábil e
reconciliação em sandbox/staging.

## 21. Backlog inicial {#21-backlog-inicial}

  Épico                                   Prioridade
  --------------------------------------- -------------------------------
  Decisões de produto/financeiras         P0
  Identidade, convites e roles            P0
  Gestão de creators, tiers e códigos     P0
  Links, redirects e tracking             P0
  Atribuição e anti-self-referral         P0
  RevenueCat inbox/idempotência           P0
  Motor de comissão e ledger              P0
  Payout manual e backoffice financeiro   P0
  Dashboard básico e My Link              P0
  Segurança, observabilidade e runbooks   P0
  Content Hub e projetos                  P1
  Instagram/TikTok e fallback manual      P1
  Performance por conteúdo                P1
  Asaas payout automation                 P1 após validar payout manual
  Missões, ranking e conquistas           P2
  AppsFlyer/OneLink                       P2, condicionado à economia

## 22. Glossário {#22-glossário}

-   **Creator:** parceiro que divulga RunEasy.
-   **Proceeds:** valor da transação após impostos/taxas da loja
    conforme dados válidos do RevenueCat.
-   **Creator CAC:** custo alocado ao canal por novo assinante pago.
-   **Corredor ativado:** usuário atribuído que completou o primeiro
    treino, sujeito à definição final.
-   **Attribution lock:** período no qual a atribuição não pode ser
    alterada pela regra normal.
-   **Ledger:** registro de lançamentos; saldos são derivados.
-   **Payout:** pagamento de comissão ao creator.
-   **MMP:** ferramenta de atribuição de installs/deep links diferidos.
-   **Snapshot:** registro de métricas sociais em um momento.
-   **Idempotência:** reprocessar o mesmo evento sem duplicar efeitos.
-   **RBAC:** controle de acesso baseado em papéis.
-   **Money Loop:** atribuição → compra → comissão → painel → pagamento.

## Apêndice A --- Revisão dos screenshots {#apêndice-a--revisão-dos-screenshots}

A navegação visual contempla: Visão Geral, Performance, Content Hub,
Missões, Projetos, Ranking, Conquistas, Earnings, My Link, Profile,
Settings e suporte. Direção visual: branco/off-white, tipografia preta,
ciano RunEasy (aprox. `#00D4FF`), bordas discretas, poucos efeitos,
gráficos simples e destaques escuros pontuais.

Ajustes essenciais:

-   padronizar nomes dos tiers em todas as telas;
-   distinguir cliques, installs aproximados, trials e pagos;
-   "começou a correr" significa corredor ativado;
-   não exibir números fictícios como reais;
-   não revelar nomes/handles de assinantes;
-   score de conteúdo exige dados ou rótulo editorial;
-   bônus de missão precisam de regra versionada;
-   usar "saldo de comissões", não "carteira financeira";
-   mostrar estado de sync, conexão, pagamento e falha.

## Apêndice B --- Hierarquia de fontes {#apêndice-b--hierarquia-de-fontes}

1.  Decisões aprovadas e registradas.
2.  PRD aprovado para comportamento, escopo e aceite.
3.  Arquitetura aprovada para stack, limites, dados, segurança e
    integrações.
4.  Blueprint para visão e hipóteses ainda não substituídas.
5.  Screenshots como referência visual, não autoridade sobre regras
    financeiras.
6.  Código futuro deve ser reconciliado com os documentos, sem alterar
    silenciosamente regras aprovadas.

Em caso de conflito, registrar a divergência e pedir decisão. Não
inferir regra financeira, jurídica, de privacidade ou autorização apenas
de uma imagem.
