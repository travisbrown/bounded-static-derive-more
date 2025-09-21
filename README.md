# Bounded Static Derive More

This is an experimental fork of [Bounded Static Derive](https://github.com/fujiapple852/bounded-static/tree/master/bounded-static-derive).

This crate provides a `ToStatic` macro which can be used to derive implementations of
the [`ToBoundedStatic`](https://docs.rs/bounded-static/0.8.0/bounded_static/trait.ToBoundedStatic.html) and
[`IntoBoundedStatic`](https://docs.rs/bounded-static/0.8.0/bounded_static/trait.IntoBoundedStatic.html) traits for all `struct`and `enum`
that can be converted to a form that is bounded by `'static`.

## Motivation

This fork differs from the original implementation in that it provides instances for struct types that
have non-`ToStatic` fields as long as those fields can be cloned and have no lifetime parameters.

For example:

```rust
#[derive(bounded_static_derive_more::ToStatic)]
struct Response<'a> {
    url: std::borrow::Cow<'a, str>,
    category: Category,
    timestamp: chrono::DateTime<chrono::Utc>,
    data: serde_json::Value,
}

#[derive(Clone, Copy)]
enum Category {
    Partial,
    Full,
}
```

This would not compile with the original Bounded Static Derive, since the last three fields do not have `ToStatic` instances.
We could use that library to derive an instance for `Category`, but that still doesn't help us with the other two fields,
which we didn't define, and can't provide instances for.

The `IntoBoundedStatic` instances for all three of these types is just identity, and the `ToBoundedStatic` instance would be
`clone`. This fork uses those operations directly in the derived instances for `Response` instead of requiring the `ToStatic`
constraints.

## Details

Any field where the type is not does not contain either a (non-static) lifetime or any of the struct's type parameters will
receive the implementations described in the previous section. This means the field's type must have a `Clone` instances,
and the implementation will be provided but will not compile if it does not.

## Less derivation

This fork also provides fewer derivations than the original project in one respect. If a struct has no lifetime parameters or
type parameters, adding the `ToStatic` derivation will cause compilation to fail. This isn't necessary in any sense, but I
decided to add it to keep things clean and clear, since those derived instances would not be used by other derived instances,
anyway.

## Missing pieces

There are several possible additions that are not (yet) provided here:

1. **Extended derivation for enums.** Currently only structs are supported. Supporting enums would be trivial, but I don't need it at the moment, and prefer to keep the diff small.
2. **Opting out via attribute.** I guess we could support using the `ToStatic` instances in cases where the `Clone` treatment
   would be applied. It feels to me like we _should_ do this, but I don't know what it would actually accomplish, and I don't care enough to do it.
3. **Decoupling the two implementations.** We could provide `IntoBoundedStatic` alone for structs with non-`Clone` fields (where `ToBoundedStatic` isn't possible using this
   approach). Again, I don't need that at the moment, and don't really care that it's missing.
4. **Generic type parameters.** The new parts of this fork don't touch fields that contain any of the struct's generic type parameters, but the base implementation will
   still add `ToStatic` constraints on the derived instance's parameters for these. That means that if you have a struct with a `T` type parameter, instantiating that with
   `serde_json::Value`, for example, will not give you `ToStatic` instances. This limitation is kind of annoying, but it's not a big deal for me at the moment,
   and I haven't really thought about whether it could be fixed.

## License

`bounded-static-derive-more` is distributed under the terms of the Apache License (Version 2.0).

See [LICENSE](LICENSE) for details.
