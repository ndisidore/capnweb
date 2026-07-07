---
"capnweb": patch
---

Harden error deserialization and session teardown against malformed peer input:

- Deserializing an error no longer resolves a wire-supplied type name to an inherited `Object.prototype` member. Previously a name like `constructor` resolved to `Object` and produced a `String` wrapper instead of an `Error` (bypassing `instanceof Error`), and a name like `toString` threw. Unknown names now fall back to `Error`.
- An error's own-property bag now skips inherited `Object.prototype` keys (and `toJSON`), matching plain-object deserialization, so keys such as `__proto__`, `toString`, and `valueOf` are never written onto the deserialized error.
- The `abort` message handler now passes error handlers the unwrapped reason rather than the internal payload wrapper, matching the `reject` handler.
- Over-long WebSocket close reasons are truncated on a UTF-8 code-point boundary, so aborting a session with a long reason no longer makes `close()` throw.
