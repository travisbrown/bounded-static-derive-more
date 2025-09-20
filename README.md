# Bounded Static Derive More

This is an experimental fork of [Bounded Static Derive](https://github.com/fujiapple852/bounded-static/tree/master/bounded-static-derive).

This crate provides a `ToStatic` macro which can be used to derive implementations of
the [`ToBoundedStatic`](https://docs.rs/bounded-static/0.8.0/bounded_static/trait.ToBoundedStatic.html) and
[`IntoBoundedStatic`](https://docs.rs/bounded-static/0.8.0/bounded_static/trait.IntoBoundedStatic.html) traits for all `struct`and `enum`
that can be converted to a form that is bounded by `'static`.

## License

`bounded-static-derive-more` is distributed under the terms of the Apache License (Version 2.0).

See [LICENSE](LICENSE) for details.
