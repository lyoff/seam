# SEAM Example — `storefront.seam.md`

> A worked example of SEAM applied to a fictional e-commerce SaaS.
> This shows what a real map looks like after bootstrap and one sprint of human curation.

For the protocol spec, see **[SEAM.md](SEAM.md)**. For the blank starter, copy **[TEMPLATE.seam.md](TEMPLATE.seam.md)**.

---

# storefront.seam.md
seam-version: 0.1.0
last-reviewed: 2026-03-16
owner: john.doe@example.com

---

## Domain: `cart`

---

### cart.total.contract

```
status: owned
risk: high
why: coupon stacking caused negative totals in v2.1 — a $12k refund incident
contract: total = sum(lineItems * qty) + tax - discount, result always >= 0, never null
symbols:
  - CartService.calculateTotal()
  - CartService.applyDiscount()
  - OrderValidator.assertTotalPositive()
file: src/lib/cart/CartService.js
```

---

### cart.inventory.reserve

```
status: owned
risk: high
why: overselling happened twice during flash sales — now we reserve on add-to-cart
contract: inventory must be reserved atomically; if reserve fails, item cannot be added
symbols:
  - InventoryService.reserve()
  - InventoryService.release()
  - CartItem.onAdd()
file: src/lib/inventory/InventoryService.js
```

---

### cart.session.persistence

```
status: watch
risk: medium
why: cart loss on session expiry caused support tickets — behavior is intentional but fragile
contract: cart persists for 72h for authenticated users; anonymous carts expire with session
symbols:
  - CartStore.persist()
  - SessionMiddleware.onExpiry()
file: src/middleware/session.js
review: requested — alex please confirm the 72h TTL is still correct per product spec
```

---

## Domain: `auth`

---

### auth.gate.api

```
status: owned
risk: high
why: we had an unauthenticated endpoint in v1 — never again
contract: every API route must pass through AuthGuard; no exceptions without explicit @public decorator
symbols:
  - AuthGuard.validate()
  - AuthGuard.extractToken()
  - PublicDecorator
file: src/middleware/auth/AuthGuard.js
```

---

### auth.permissions.3pl

```
status: draft
risk: high
why: 3PL clients have scoped access — they must only see their own inventory
contract: 3PL user token must carry clientId claim; all queries must be scoped to that clientId
symbols:
  - TokenService.extract3PLClaims()
  - InventoryRepository.scopeToClient()
file: src/auth/TokenService.js
review: requested — this was ai-drafted, needs human verification before owned
```

---

## Domain: `orders`

---

### orders.submission.idempotency

```
status: owned
risk: high
why: double-submit bug caused duplicate orders in v3 — customer paid twice
contract: order submission must be idempotent on clientOrderId; second submission returns existing order
symbols:
  - OrderService.submit()
  - OrderRepository.findByClientOrderId()
  - IdempotencyKey.generate()
file: src/lib/orders/OrderService.js
```

---

### orders.fulfillment.webhook

```
status: watch
risk: high
why: fulfillment webhooks from 3PL partners arrive out of order — status regressions possible
contract: fulfillment status can only move forward (pending -> processing -> shipped -> delivered); never backward
symbols:
  - WebhookHandler.onFulfillmentUpdate()
  - OrderStatus.canTransitionTo()
file: src/webhooks/fulfillment.js
```

---

### orders.export.netsuite

```
status: gap
risk: high
why: (not yet mapped — agent detected unanchored NetSuite sync code)
contract: unknown — needs human review
symbols: (agent could not resolve — multiple candidates)
file: src/integrations/netsuite/sync.js
review: requested — this is a gap, someone needs to own this before next release
```

---

## Domain: `billing`

---

### billing.stripe.webhook

```
status: owned
risk: high
why: payment confirmation must survive Stripe retries — we were double-crediting accounts
contract: stripe webhook events must be deduplicated on stripe event id before processing
symbols:
  - StripeWebhookHandler.handle()
  - PaymentEvent.findOrCreate()
  - StripeWebhookHandler.validateSignature()
file: src/billing/StripeWebhookHandler.js
```

---

## Emergency Index

*When production breaks, start here.*

| Symptom | Domain | First symbol to check |
|---------|--------|----------------------|
| Wrong order total | `cart` | `CartService.calculateTotal()` |
| Oversell / negative inventory | `cart` | `InventoryService.reserve()` |
| Unauthorized access | `auth` | `AuthGuard.validate()` |
| Duplicate orders | `orders` | `OrderService.submit()` |
| Payment double-credited | `billing` | `StripeWebhookHandler.handle()` |
| 3PL seeing wrong inventory | `auth` | `TokenService.extract3PLClaims()` |

---

## Map Stats

```
Total nodes:     9
owned:           5   (56%)
watch:           2   (22%)
draft:           1   (11%)
gap:             1   (11%)

Comprehension coverage: 56% (target: 80%+ owned before next major release)
```

---

*This example is fictional but structurally realistic.
A production SEAM map for a system this size would typically have 15-30 nodes.*
