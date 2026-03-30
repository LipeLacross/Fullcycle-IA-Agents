# PRD — FullCycle Mercadinho

## 1. Visão Geral

| Campo | Descrição |
|-------|-----------|
| **Produto** | FullCycle Mercadinho |
| **Tipo** | E-commerce de varejo online |
| **Proposta de valor** | Catálogo variado de produtos com agilidade na entrega |
| **Modelo de negócio** | Venda direta ao consumidor (B2C) |
| **Meio de pagamento** | Stripe |
| **Versão** | 1.0 |
| **Data** | Março 2026 |

---

## 2. Problema

Consumidores buscam conveniência para comprar produtos variados sem sair de casa, mas muitos e-commerces oferecem catálogos limitados ou prazos de entrega longos. O FullCycle Mercadinho resolve isso combinando **variedade de produtos** com **logística ágil de entrega**.

---

## 3. Personas

| Persona | Descrição | Necessidade principal |
|---------|-----------|----------------------|
| **Comprador casual** | Qualquer pessoa que quer comprar online com praticidade | Encontrar produtos rápido, comprar sem fricção, receber rápido |
| **Comprador recorrente** | Cliente fidelizado que compra com frequência | Histórico de pedidos, endereços salvos, recompra fácil |
| **Administrador da loja** | Dono/gestor do e-commerce | Gerenciar produtos, acompanhar pedidos, controlar estoque |

---

## 4. Funcionalidades

### 4.1 Catálogo de Produtos

| Funcionalidade | Descrição | Prioridade |
|----------------|-----------|------------|
| Listagem de produtos | Exibir produtos com foto, nome, preço e botão de compra | Alta |
| Categorias | Organizar produtos por categorias navegáveis | Alta |
| Busca | Campo de busca por nome ou palavra-chave | Alta |
| Página de detalhe | Foto(s), descrição completa, preço, disponibilidade, botão "Adicionar ao carrinho" | Alta |
| Filtros | Filtrar por categoria, faixa de preço, disponibilidade | Média |
| Ordenação | Ordenar por preço (menor/maior), mais recentes, mais vendidos | Média |
| Paginação | Navegar entre páginas de resultados | Alta |
| Galeria de imagens | Múltiplas fotos por produto | Média |
| Preço promocional | Exibir preço original riscado e preço com desconto | Média |
| Indicador de estoque | Mostrar "em estoque", "poucas unidades" ou "esgotado" | Alta |

### 4.2 Conta do Cliente

| Funcionalidade | Descrição | Prioridade |
|----------------|-----------|------------|
| Cadastro | Criar conta com nome, email e senha | Alta |
| Login / Logout | Autenticação por email e senha | Alta |
| Recuperação de senha | Receber link de redefinição por email | Alta |
| Perfil | Editar nome, email, telefone | Alta |
| Endereços salvos | Cadastrar, editar e remover endereços de entrega | Alta |
| Histórico de pedidos | Listar pedidos anteriores com status e detalhes | Alta |
| Detalhe do pedido | Ver itens, valores, endereço e status de um pedido específico | Alta |

### 4.3 Carrinho de Compras

| Funcionalidade | Descrição | Prioridade |
|----------------|-----------|------------|
| Adicionar ao carrinho | Botão presente na listagem e na página de detalhe | Alta |
| Visualizar carrinho | Página com todos os itens, quantidades, subtotais e total | Alta |
| Alterar quantidade | Aumentar ou diminuir quantidade de cada item | Alta |
| Remover item | Excluir item do carrinho | Alta |
| Persistência | Carrinho mantido na sessão; migrado para a conta ao fazer login | Alta |
| Indicador no header | Badge com quantidade de itens visível em todas as páginas | Média |
| Validação de estoque | Impedir adicionar mais que o estoque disponível | Alta |

### 4.4 Checkout

| Funcionalidade | Descrição | Prioridade |
|----------------|-----------|------------|
| Exigir login | Redirecionar para login/cadastro antes de prosseguir | Alta |
| Endereço de entrega | Selecionar endereço salvo ou cadastrar novo | Alta |
| Resumo do pedido | Exibir itens, quantidades, subtotal, frete e total antes de pagar | Alta |
| Cálculo de frete | Valor de frete (fixo ou por faixa de CEP) | Alta |
| Confirmação | Botão "Pagar" que redireciona para o meio de pagamento | Alta |
| Página de sucesso | Confirmação pós-pagamento com número do pedido | Alta |
| Página de cancelamento | Mensagem caso o pagamento seja cancelado, com opção de tentar novamente | Alta |

### 4.5 Pagamento (Stripe)

| Funcionalidade | Descrição | Prioridade |
|----------------|-----------|------------|
| Redirecionamento para Stripe | Ao confirmar checkout, cliente é levado à página segura do Stripe | Alta |
| Cartão de crédito/débito | Aceitar pagamentos com cartão via Stripe | Alta |
| Confirmação automática | Webhook recebe confirmação do Stripe e atualiza o pedido para "Pago" | Alta |
| Atualização de estoque | Decrementar estoque automaticamente após pagamento confirmado | Alta |
| Segurança | Nenhum dado de cartão passa pelo servidor da loja — tudo via Stripe | Alta |

### 4.6 Área Administrativa

| Funcionalidade | Descrição | Prioridade |
|----------------|-----------|------------|
| Dashboard | Visão geral de pedidos recentes e métricas básicas | Média |
| Gestão de produtos | Criar, editar, ativar/desativar, excluir produtos | Alta |
| Upload de imagens | Adicionar foto principal e galeria de cada produto | Alta |
| Gestão de categorias | Criar, editar, ordenar e excluir categorias | Alta |
| Gestão de estoque | Visualizar e atualizar quantidade em estoque por produto | Alta |
| Gestão de pedidos | Listar pedidos, filtrar por status, ver detalhes | Alta |
| Atualização de status | Alterar status do pedido (pendente → pago → enviado → entregue) | Alta |
| Gestão de clientes | Visualizar lista de clientes cadastrados | Média |

### 4.7 Notificações (Email)

| Funcionalidade | Descrição | Prioridade |
|----------------|-----------|------------|
| Confirmação de cadastro | Email de boas-vindas ao criar conta | Média |
| Confirmação de pedido | Email com resumo do pedido após pagamento aprovado | Alta |
| Atualização de status | Email quando pedido for enviado ou entregue | Média |
| Recuperação de senha | Email com link para redefinir senha | Alta |

---

## 5. Jornadas do Usuário

### Jornada 1 — Primeira compra

1. Visitante acessa a home e navega pelo catálogo
2. Encontra um produto (via busca ou categoria)
3. Abre a página de detalhe e clica "Adicionar ao carrinho"
4. Vai ao carrinho, revisa os itens
5. Clica "Ir para checkout"
6. É solicitado a criar conta (ou fazer login)
7. Preenche endereço de entrega
8. Revisa resumo do pedido com frete
9. Clica "Pagar" → é redirecionado ao Stripe
10. Preenche dados do cartão no Stripe
11. Pagamento aprovado → redirecionado à página de sucesso
12. Recebe email de confirmação com número do pedido

### Jornada 2 — Compra recorrente

1. Cliente logado acessa a loja
2. Adiciona produtos ao carrinho
3. Vai ao checkout → endereço já salvo é pré-selecionado
4. Confirma e paga
5. Consulta status no histórico de pedidos

### Jornada 3 — Gestão pelo administrador

1. Admin faz login no painel administrativo
2. Cadastra categorias e produtos com fotos e preços
3. Monitora pedidos recebidos
4. Atualiza status dos pedidos (pago → enviado → entregue)
5. Ajusta estoque conforme necessário

---

## 6. Regras de Negócio

| # | Regra |
|---|-------|
| RN01 | Não é possível comprar produto com estoque zerado |
| RN02 | O checkout exige que o cliente esteja autenticado |
| RN03 | O pedido só muda para "Pago" após confirmação real do Stripe (webhook) |
| RN04 | O estoque só é decrementado após pagamento confirmado |
| RN05 | Um pedido cancelado no Stripe não altera estoque |
| RN06 | O carrinho de visitante é migrado para o usuário ao fazer login |
| RN07 | Cada produto deve ter pelo menos uma imagem |
| RN08 | Preços são sempre em BRL (R$) |
| RN09 | Quantidade mínima por item no carrinho é 1 |
| RN10 | Não é possível adicionar ao carrinho mais unidades que o estoque disponível |

---

## 7. Métricas de Sucesso

| Métrica | Descrição |
|---------|-----------|
| Taxa de conversão | % de visitantes que completam uma compra |
| Ticket médio | Valor médio por pedido |
| Taxa de abandono de carrinho | % de carrinhos criados que não chegam ao pagamento |
| Tempo médio de entrega | Dias entre pagamento e entrega confirmada |
| Taxa de recompra | % de clientes que fazem mais de uma compra |
| NPS | Satisfação do cliente pós-compra |

---

## 8. Fora de Escopo (v1)

- Marketplace (múltiplos vendedores)
- Sistema de cupons/vouchers de desconto
- Programa de fidelidade / pontos
- Avaliações e reviews de produtos
- Wishlist / lista de desejos
- Chat ou suporte ao vivo
- App mobile nativo
- Integração com marketplaces externos (Mercado Livre, Shopee)
- Múltiplas moedas / internacionalização

---

## 9. Requisitos Não-Funcionais

| Requisito | Descrição |
|-----------|-----------|
| **Performance** | Páginas do catálogo devem carregar em menos de 3 segundos |
| **Segurança** | Dados de pagamento nunca trafegam pelo servidor — Stripe hospeda o checkout |
| **Responsividade** | Layout funcional em desktop, tablet e mobile |
| **Disponibilidade** | Sistema acessível 24/7 |
| **Escalabilidade** | Arquitetura permite crescimento de catálogo e volume de pedidos |

---

## 10. Riscos e Mitigações

| Risco | Impacto | Mitigação |
|-------|---------|-----------|
| Falha no webhook do Stripe | Pedido pago não atualizado | Implementar retry e reconciliação manual |
| Venda acima do estoque | Experiência negativa do cliente | Validar estoque no momento do pagamento |
| Fraude em pagamentos | Prejuízo financeiro | Usar Stripe Radar (proteção antifraude nativa) |
| Indisponibilidade do Stripe | Impossibilidade de pagamento | Monitorar status do Stripe; comunicar cliente |
