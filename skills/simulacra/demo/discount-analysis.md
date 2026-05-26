# Demo: applyDiscount Analysis

## Input Code

```typescript
function applyDiscount(cart) {
  if (cart.total >= 500) {
    cart.items.forEach(item => item.price *= 0.8);
  }
  return cart;
}
```

## Simulacra Output

### 🌪️ SIMULACRA ENGINE ACTIVE
### ⚠️ 3 Butterfly Effects Detected

---

### 👤 GHOST #1: "Reckless Rick"

**Perspective:** User who breaks things by normal (slow internet) behavior
**Hunting for:** Idempotency gaps in the discount flow
**Edge case:** Back-button re-invokes applyDiscount on already-discounted prices

**🔴 SIMULATION: Reckless Rick**

```
⏱️ T+0ms:    applyDiscount() called — cart.total = 630
⏱️ T+50ms:   cart.total >= 500 → true
⏱️ T+100ms:  forEach mutates item.price (×0.8) → total now 504
⏱️ T+200ms:  ⚠️ User hits browser Back button
⏱️ T+350ms:  applyDiscount() called AGAIN on mutated cart
⏱️ T+400ms:  💀 504 × 0.8 = 403 — prices drop AGAIN
⏱️ T+1200ms: 💀 Third Back press: 403 → 322
```

**What I did:** Hit Back twice during checkout (normal user behavior on slow connection)
**What broke:** `item.price *= 0.8` (line 3) — mutates price in place with no re-entry guard
**Evidence:** forEach runs on the same cart object reference each time. No `discountApplied` flag, no `originalPrice` preserved.
**Result:** 630 TL cart purchased for 322 TL. Company loses 308 TL per affected order.
**Bonus:** `originalPrice` is never stored. Accounting system asks "what was the real price?" — no answer. Tax audit risk.

**🔥 BUG:** IDEMPOTENCY + MUTATION — Double discount on back-navigation
**Severity:** 🔴 Critical

---

### 👤 GHOST #2: "Corporate Claire"

**Perspective:** Enterprise customer with complex business rules
**Hunting for:** Currency and business logic assumptions
**Edge case:** Multi-currency cart where threshold check uses wrong denomination

**🔴 SIMULATION: Corporate Claire**

```
⏱️ T+0ms:    applyDiscount() called
⏱️ T+30ms:   Cart contains EUR items — total 200 EUR (= 7000 TRY)
⏱️ T+50ms:   ❌ cart.total = 200 < 500 → false
⏱️ T+51ms:   ⚠️ Discount skipped — business logic gap
⏱️ T+300ms:  💀 Customer calls support: "Why no discount on 7000 TRY order?"
```

**What I did:** Placed a 200 EUR order (equivalent to 7000 TRY at current rates)
**What broke:** `cart.total >= 500` (line 2) — checks raw number without currency normalization
**Evidence:** No currency field referenced. Comparison is numeric only.
**Result:** High-value international customers never receive discounts. Revenue loss from churn.
**Bonus:** If you ever add GBP, the same 500 threshold means completely different things per currency.

**🔥 BUG:** BUSINESS-LOGIC — Multi-currency threshold comparison
**Severity:** 🟠 High

---

### 👤 GHOST #3: "OOM Reaper"

**Perspective:** Infrastructure under stress
**Hunting for:** Partial failure in the mutation loop
**Edge case:** Database/memory failure mid-forEach

**🔴 SIMULATION: OOM Reaper**

```
⏱️ T+0ms:    applyDiscount() called — 14 items
⏱️ T+50ms:   forEach begins iterating
⏱️ T+180ms:  7/14 items mutated (price × 0.8)
⏱️ T+200ms:  💥 Process memory spike / unhandled exception
⏱️ T+500ms:  💀 7 items at discounted price, 7 at original — cart is inconsistent
```

**What I did:** Nothing. The system ran out of memory during a traffic spike.
**What broke:** `cart.items.forEach(item => item.price *= 0.8)` (line 3) — no transaction boundary around the mutation loop
**Evidence:** forEach on a mutable array with no rollback. If interrupted at item 7 of 14, half the cart is discounted, half isn't.
**Result:** Inconsistent cart state. Customer sees mixed prices. No rollback mechanism.
**Bonus:** If this cart gets persisted to DB mid-loop, the corruption is permanent.

**🔥 BUG:** PARTIAL-FAILURE — No atomicity on cart mutation
**Severity:** 🔴 Critical

---

## Severity Summary

| # | Bug | Category | Severity | Ghost | Line |
|---|-----|----------|----------|-------|------|
| 1 | Double discount on back-nav | IDEMPOTENCY | 🔴 Critical | Reckless Rick | 3 |
| 2 | Multi-currency threshold | BUSINESS-LOGIC | 🟠 High | Corporate Claire | 2 |
| 3 | Partial mutation, no rollback | PARTIAL-FAILURE | 🔴 Critical | OOM Reaper | 3 |

## Resolution

### Tier 1: Quick Fix
```typescript
function applyDiscount(cart) {
  if (cart.discountApplied) return cart; // idempotency guard
  if (cart.total >= 500) {
    cart.items.forEach(item => {
      item.originalPrice = item.originalPrice ?? item.price;
      item.price = item.originalPrice * 0.8;
    });
    cart.discountApplied = true;
  }
  return cart;
}
```

### Tier 2: Robust Architecture
```typescript
function applyDiscount(cart: Cart): Cart {
  const normalizedTotal = normalizeCurrency(cart.total, cart.currency, 'TRY');
  if (normalizedTotal < 500 || cart.discountApplied) return cart;

  // Immutable — return new cart, don't mutate
  return {
    ...cart,
    discountApplied: true,
    items: cart.items.map(item => ({
      ...item,
      originalPrice: item.originalPrice ?? item.price,
      price: (item.originalPrice ?? item.price) * 0.8,
    })),
  };
}
```

```json
{
  "simulacra": {
    "version": "2.0",
    "code_language": "typescript",
    "complexity": "non-trivial",
    "butterfly_effects": 3,
    "ghosts": [
      {
        "id": 1,
        "name": "Reckless Rick",
        "type": "adversary",
        "bugs": [
          {
            "category": "IDEMPOTENCY",
            "name": "Double discount on back-navigation",
            "severity": "critical",
            "line": 3,
            "description": "forEach mutates item.price on every call with no re-entry guard",
            "fix_suggestion": "Add discountApplied flag + store originalPrice"
          }
        ]
      },
      {
        "id": 2,
        "name": "Corporate Claire",
        "type": "enterprise_edge",
        "bugs": [
          {
            "category": "BUSINESS-LOGIC",
            "name": "Multi-currency threshold comparison",
            "severity": "high",
            "line": 2,
            "description": "cart.total >= 500 compares raw number without currency normalization",
            "fix_suggestion": "Normalize to base currency before threshold check"
          }
        ]
      },
      {
        "id": 3,
        "name": "OOM Reaper",
        "type": "chaos_monkey",
        "bugs": [
          {
            "category": "PARTIAL-FAILURE",
            "name": "No atomicity on cart mutation",
            "severity": "critical",
            "line": 3,
            "description": "forEach mutates in place with no rollback; interruption leaves cart inconsistent",
            "fix_suggestion": "Use immutable map() returning new cart object"
          }
        ]
      }
    ],
    "total_bugs": 3,
    "critical_count": 2,
    "high_count": 1
  }
}
```
