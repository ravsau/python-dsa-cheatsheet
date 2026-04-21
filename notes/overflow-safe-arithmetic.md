# Overflow-safe arithmetic (32-bit)

**TL;DR:** When a problem says "no 64-bit", check overflow **before** the multiplication, not after. The check itself must use only values ≤ INT_MAX.

**Where it matters:**
- LC 7 Reverse Integer — `result * 10 + digit` may overflow
- LC 8 String to Integer (atoi) — same
- LC 29 Divide Two Integers — `abs(INT_MIN)` overflow edge
- LC 50 Pow(x, n) — `abs(n)` with `n == INT_MIN`

**Patterns:** bounded-arithmetic, digit-building

**Why "no 64-bit" is a real constraint:** In C/C++/Java the obvious escape hatch is `long long result` then check. The constraint bans that — forces you to reason about 32-bit overflow with only 32-bit intermediates. Python ints don't overflow, but LC graders enforce the bound → you simulate it.

**The check (before multiplying):**
```python
INT_MAX = 2**31 - 1        # 2_147_483_647
# INT_MAX // 10 = 214_748_364, INT_MAX % 10 = 7

if result > INT_MAX // 10:
    return 0                                   # *10 alone overflows
if result == INT_MAX // 10 and digit > INT_MAX % 10:
    return 0                                   # *10+digit overflows
result = result * 10 + digit                   # safe
```

**Why two cases:**
- `result > 214_748_364` → `result * 10 ≥ 2_147_483_650` → overflow for *any* digit.
- `result == 214_748_364` → `result * 10 == 2_147_483_640` → digits 0..7 fit, 8..9 overflow.

**For the negative side:** strip sign first, check only against INT_MAX. The `abs(INT_MIN)` one-off edge rarely matters for LC 7 (reversed INT_MIN overflows anyway) but bites on LC 29 / LC 50.

**Gotcha:** The naive "compute then check" (`if result > INT_MAX: return 0`) technically works in Python but **fails the interview signal**. The interviewer is grading whether you can reason about overflow in a bounded environment. Write the 32-bit-safe version even when Python doesn't force you.
