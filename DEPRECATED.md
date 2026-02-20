# ⚠️ DEPRECATED

This branch has been superseded by [`fix/always-create-client-values`](https://github.com/DrudgeDance/leptos/tree/fix/always-create-client-values).

## Why this branch was wrong

This branch (`fix/defensive-plus-rebuild`) added **defensive handling** in `On::attach()` to gracefully handle `None` callbacks. While it prevented the panic, it **masked the root cause** — the callbacks were still being created as `None` when `cfg!(feature = "ssr")` was true.

## The correct fix

The root cause is in the `on()`, `directive()`, and `prop()` **constructors**, which conditionally create values based on `cfg!(feature = "ssr")`:

```rust
// BEFORE (broken): skips creation when ssr feature is active
cb: (!cfg!(feature = "ssr")).then(|| SendWrapper::new(cb))

// AFTER (fixed): always creates the value
cb: Some(SendWrapper::new(cb))
```

The correct branch (`fix/always-create-client-values`) contains:
1. Core fix: always create client-side values (events, directives, properties)
2. `compile_error!` guards for csr+ssr, csr+hydrate, ssr+hydrate
3. Actionable diagnostic error messages
4. 15 WASM regression tests
5. CI feature-safety workflow

→ **Use [`fix/always-create-client-values`](https://github.com/DrudgeDance/leptos/tree/fix/always-create-client-values) instead.**
