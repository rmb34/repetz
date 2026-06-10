# Análise de Melhorias — Aquisição de Usuários (Repetz)

> Objetivo: tornar a proposta do Repetz irresistível para o público-alvo (pet shops, banho e tosa, grooming móvel, hotel/creche, dog walking), aumentando aquisição e conversão **sem regressão** funcional ou visual.
>
> **Base da análise:** README do produto (funcionalidades, stack, modelo comercial) e benchmark do mercado brasileiro de sistemas para pet shop. O código-fonte do produto e o site não estavam acessíveis nesta sessão — as recomendações de nível de código devem ser validadas em uma auditoria com acesso ao repositório principal.

---

## 1. Diagnóstico: onde a venda é ganha ou perdida

O funil típico de um SaaS para pet shop no Brasil é:

```
Descoberta (Google/Instagram/indicação)
  → Landing page (decisão em ~30 segundos)
    → Trial 14 dias sem cartão
      → Ativação (primeiro valor real percebido)
        → Conversão paga
          → Indicação / expansão
```

O Repetz já tem dois trunfos raros no segmento e que a concorrência (Sispet, OnePet, ZettaPET, PetMaster) explora mal:

1. **Página pública de agendamento sem login** — é um canal de aquisição viral embutido no produto.
2. **Automação de WhatsApp via Meta Cloud API oficial** — a maioria dos concorrentes usa integrações frágeis ou não tem.

A estratégia central desta análise: **transformar esses dois diferenciais no motor de aquisição**, e remover toda fricção entre "conheci" e "já estou usando com meus clientes reais".

---

## 2. Melhorias priorizadas

Classificação: **Impacto** (na aquisição) × **Esforço** × **Risco de regressão**.

### 2.1 Topo de funil — fazer o produto se vender sozinho

| # | Melhoria | Impacto | Esforço | Risco |
|---|---|---|---|---|
| A1 | Selo "Agende online com Repetz" na página pública de agendamento, com link de cadastro para lojistas | Alto | Baixo | Nulo |
| A2 | SEO programático: páginas `repetz.com.br/sistema-para-[banho-e-tosa\|pet-shop\|hotel-pet\|creche-pet\|dog-walker]` e por cidade | Alto | Médio | Nulo |
| A3 | Páginas comparativas honestas ("Repetz vs. Sispet", "Repetz vs. OnePet") — quem busca isso está pronto para comprar | Alto | Baixo | Nulo |
| A4 | Programa de indicação: 1 mês grátis para quem indica e para o indicado, com link rastreável | Alto | Médio | Baixo |
| A5 | Calculadora pública de faturamento perdido (no-show + clientes inativos) que entrega o resultado e oferece o trial como solução | Médio | Baixo | Nulo |

**A1 é a melhoria de maior alavancagem da lista.** Cada tutor que agenda banho pelo link público é um potencial dono de pet shop ou conhece um. Custo quase zero, nenhuma mudança em fluxo existente.

### 2.2 Landing page — visual e conversão

Princípio: o dono de pet shop não compra "sistema de gestão"; compra **menos no-show, agenda cheia e caixa fechando certo**. Cada seção deve falar disso.

| # | Melhoria | Impacto | Esforço | Risco |
|---|---|---|---|---|
| B1 | Headline orientada a dor/resultado: ex. *"Reduza faltas em até 40% com lembrete automático no WhatsApp"* em vez de descrever funcionalidades | Alto | Baixo | Nulo |
| B2 | Demo interativa ou vídeo de 60–90s mostrando o fluxo real: cliente agenda pelo link → lembrete no WhatsApp → pet pronto → notificação. É o "momento uau" do produto | Alto | Médio | Nulo |
| B3 | Prova social concreta: fotos e nomes de pet shops reais usando, número de agendamentos processados, banhos realizados ("+X mil banhos agendados pelo Repetz") | Alto | Baixo | Nulo |
| B4 | Preço 100% transparente na home com âncora no custo de 1 no-show/dia (ex.: "menos que 2 banhos por mês") + destaque do plano anual | Alto | Baixo | Nulo |
| B5 | CTA secundário "Falar no WhatsApp" ao lado do "Teste grátis" — o público-alvo decide conversando, não preenchendo formulário | Alto | Baixo | Nulo |
| B6 | Seção por segmento (banho e tosa fixo / móvel / hotel / dog walking) — o visitante precisa se enxergar no produto em 5 segundos | Médio | Baixo | Nulo |
| B7 | FAQ de objeções: "preciso de cartão?", "consigo migrar minha planilha?", "funciona no celular?", "e se eu cancelar?", "meus dados são meus?" | Médio | Baixo | Nulo |
| B8 | Performance e mobile: Core Web Vitals verdes, página leve — o tráfego desse público é majoritariamente Instagram → celular | Alto | Médio | Baixo |

### 2.3 Trial e onboarding — encurtar o tempo até o primeiro valor

A taxa de conversão trial→pago é decidida nos **primeiros 10 minutos**. Meta: o usuário sair do primeiro acesso com um agendamento real criado e um lembrete de WhatsApp configurado.

| # | Melhoria | Impacto | Esforço | Risco |
|---|---|---|---|---|
| C1 | Checklist de ativação no dashboard (4 passos: cadastrar serviço → criar agendamento → ativar lembrete WhatsApp → compartilhar link público) com barra de progresso | Alto | Médio | Baixo |
| C2 | Workspace pré-populado com dados de exemplo claramente marcados (e botão "limpar exemplos") — ninguém avalia um sistema vazio | Alto | Baixo | Baixo |
| C3 | Importação assistida de clientes via planilha (CSV/Excel) com mapeamento de colunas — a migração da planilha é a maior objeção prática | Alto | Médio | Baixo |
| C4 | Sequência de e-mails do trial (Resend já está na stack): dia 0 boas-vindas, dia 2 "compartilhe seu link de agendamento", dia 7 caso de sucesso, dia 12 oferta de conversão | Alto | Baixo | Nulo |
| C5 | Alerta de fim de trial **dentro do produto** com resumo do valor gerado: "Você criou 23 agendamentos e enviou 41 lembretes neste teste" | Alto | Baixo | Baixo |
| C6 | Oferta de onboarding humano (call de 20 min via WhatsApp) para trials com mais de N clientes cadastrados — alto valor, baixo custo enquanto a base é pequena | Médio | Baixo | Nulo |

### 2.4 Produto — remodelagens que viram argumento de venda

| # | Melhoria | Impacto | Esforço | Risco |
|---|---|---|---|---|
| D1 | PWA instalável / experiência mobile impecável no painel — tosador e recepção operam pelo celular; "funciona no seu celular, sem baixar nada" é argumento de fechamento | Alto | Médio | Médio |
| D2 | Lembrete de retorno inteligente: "o Thor toma banho a cada 15 dias, faz 20 que não vem" → sugestão de mensagem de reengajamento pronta (já existe base disso na automação de inativos) | Alto | Médio | Baixo |
| D3 | Confirmação de agendamento pelo próprio tutor via WhatsApp (responder "1" confirma) — fecha o ciclo anti-no-show e é demo-friendly | Alto | Médio | Médio |
| D4 | Relatório mensal automático "Seu pet shop em números" enviado por e-mail/WhatsApp ao dono — retenção + material que o cliente mostra para colegas | Médio | Baixo | Baixo |
| D5 | Galeria antes/depois do banho e tosa anexada ao pet, com envio da foto "pet pronto" pelo WhatsApp — recurso emocional, altamente compartilhável e raro na concorrência | Médio | Médio | Baixo |

### 2.5 Correções de confiança — remover o medo de comprar

Para um lojista, trocar de sistema é arriscado. Eliminar o risco percebido converte mais que adicionar funcionalidade.

| # | Melhoria | Impacto | Esforço | Risco |
|---|---|---|---|---|
| E1 | Página de privacidade/LGPD clara + frase "seus dados são seus, exporte tudo quando quiser" (com exportação real em CSV) | Alto | Médio | Baixo |
| E2 | Garantia explícita: "cancele quando quiser, sem fidelidade, sem multa" em destaque no checkout | Alto | Baixo | Nulo |
| E3 | Migração assistida gratuita de outro sistema/planilha como benefício anunciado | Alto | Baixo | Nulo |
| E4 | Página de status/uptime pública (mesmo simples) — sinaliza maturidade | Baixo | Baixo | Nulo |

---

## 3. Como executar sem regressão

O README indica 335 testes (Jest, node + jsdom) e infraestrutura Vercel — a base certa já existe. Processo recomendado para todas as mudanças acima:

1. **Separar mudanças de marketing de mudanças de produto.** Tudo da seção 2.1/2.2 (landing, SEO, páginas comparativas) não toca o app autenticado — risco de regressão praticamente nulo; priorizar essas primeiro.
2. **Feature flags para tudo que toca fluxo existente** (C1–C3, D1–D3): lançar desligado, ativar para o próprio tenant de testes, depois para 10% dos tenants, depois geral. Multi-tenant facilita rollout gradual por tenant.
3. **Testes de regressão nos fluxos críticos antes de qualquer mudança neles:** checkout Stripe, abertura/fechamento de caixa, criação de agendamento, envio de WhatsApp. Se ainda não há testes e2e (Playwright), criar suíte mínima cobrindo esses 4 fluxos é pré-requisito para mexer neles.
4. **Métricas guard-rail por release:** taxa de erro (Sentry ou similar), latência p95, taxa de sucesso de webhook Stripe e de envio WhatsApp. Qualquer degradação → rollback (instantâneo na Vercel).
5. **A/B test apenas na landing** (headline, CTA, preço com âncora). No produto autenticado, com base de clientes ainda pequena, A/B não tem poder estatístico — usar rollout gradual + feedback direto.
6. **Nunca alterar visual de fluxo operacional (PDV, caixa) sem aviso prévio aos clientes atuais** — para quem opera loja, mudança surpresa no caixa é regressão percebida mesmo sem bug.

---

## 4. Métricas para saber se está funcionando

- **North star:** agendamentos criados por semana (soma de todos os tenants) — captura aquisição, ativação e uso real.
- Funil: visitantes → inícios de trial (meta inicial: 5%+) → trials ativados (criou agendamento real: 60%+) → conversão paga (meta inicial: 15–25%) → churn mensal (<5%).
- Aquisição viral: cadastros originados do selo na página pública (A1) e do programa de indicação (A4).

---

## 5. Roadmap sugerido

**Semanas 1–2 (zero risco, alto retorno):**
A1 (selo na página pública) · B1, B3, B4, B5, B7 (landing) · E2, E3 (garantias) · C4 (e-mails de trial).

**Semanas 3–6:**
C1, C2, C3 (onboarding e importação) · B2 (vídeo/demo) · A3 (comparativos) · C5 (resumo de valor no fim do trial) · suíte e2e mínima dos 4 fluxos críticos.

**Semanas 7–12:**
A2 (SEO programático) · A4 (indicação) · D1 (PWA/mobile) · D2, D3 (ciclo anti-no-show completo) · D5 (foto antes/depois) · E1 (LGPD + exportação).

---

## 6. Próximo passo recomendado

Esta análise foi feita sem acesso ao código do produto e ao site (restrições do ambiente). Para transformá-la em backlog executável com segurança:

1. Adicionar o repositório do código a uma sessão futura para auditoria concreta de UX do onboarding, performance (Core Web Vitals), SEO técnico e cobertura de testes dos fluxos críticos.
2. Instrumentar o funil (ex.: PostHog/Plausible) **antes** das mudanças, para medir o efeito de cada uma.
