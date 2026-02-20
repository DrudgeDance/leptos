# ⚠️ DEPRECATED

This branch has been superseded by [`fix/always-create-client-values`](https://github.com/DrudgeDance/leptos/tree/fix/always-create-client-values).

## Why this branch was wrong

This branch (`fix/non-optional-cb`) attempted to make `On::cb` non-optional via cfg-gated struct definitions. While technically sound, it was **overly complex** and didn't address the same pattern in `directive.rs` and `property.rs`. It also didn't add compile-time guards for feature conflicts.

## The correct fix

The root cause is simpler than this branch assumed. The `on()`, `directive()`, and `prop()` constructors conditionally create values based on `cfg!(feature = "ssr")`:

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
