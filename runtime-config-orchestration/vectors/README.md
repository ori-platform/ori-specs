# `runtime-config-orchestration/v1` vectors

One file, `set-transitions.json`: state-transition cases over the
configuration set. No case carries bytes — the artifacts' own contracts own
byte-level verification — so anchors are labels with opaque key material,
signatures are `valid` or `invalid`, and a binding event carries the verdict
its own contract returns.

## Reading a case

`anchors` maps a label to `class`, `generation` and `key`; two labels sharing
a `key` collide. `initial` is the durable set state: the accepted provisioning
document (generation, content, whether it declares actuating hardware), the
accepted binding (generation, `binding_seq`), any pending binding, whether an
entitlement is held, and whether the latch is set.

Events are `startup` with a `posture`, `provisioning`, `binding`,
`entitlement`, and `restart`. Each carries `expect`: the verdict, and the
state view a consumer must report afterwards — generations held, pending
binding, entitlement, latch, whether actuation is licensed, and whether the
set reports an inventory mismatch.

## Checking it

```bash
python3 scripts/check-runtime-config-orchestration-vectors
```

It runs as a pre-commit hook and as an explicit step in `validate.yml`. The
checker recomputes every verdict and state from the contract's rules and
refuses the corpus on disagreement, and checks that every verdict and startup
outcome the contract names has a case. It is a reader of the corpus against
the contract, not a second implementation.
