# Customizing the Build Process

When you setup LALRPOP, you create a `build.rs` file that looks something
like this:

```rust
fn main() {
    lalrpop::process_src().unwrap();
}
```

This `process_src()` call simply applies the default configuration:
so it will transform `.lalrpop` files into `.rs` files *in-place* (in
your `src` directory), and it will only do so if the `.lalrpop` file
has actually changed. But you can also use the
[`Configuration`][config] struct to get more detailed control.

[config]: https://docs.rs/lalrpop/*/lalrpop/struct.Configuration.html

For example, to **force** the use of colors in the output (ignoring
the TTY settings), you might make your `build.rs` file look like so:

```rust
fn main() {
    lalrpop::Configuration::new()
        .always_use_colors()
        .process_current_dir();
}
```

## Rerun Directives

Cargo will rerun the build script on each compilation even if the lalrpop file
has not changed. To disable this behavior, use the `emit_rerun_directives`
function when setting up your lalrpop `Configuration`.

```rust
fn main() {
    lalrpop::Configuration::new()
        .emit_rerun_directives(true)
        .process_current_dir();
}
```

By default, this is set to false in case other parts of the build script or
compilation code expects `build.rs` to be run unconditionally.

## Using the Legacy LALR Parser

**Note:** LALR support is deprecated and may be removed in a future release.
If you really need this feature, please file a github issue and let us know.

By default, LALRPOP uses the [lane table][]
algorithm which is LR(1) but creates much smaller tables. There is no longer
any clear benefit to using the previous LALR implementation but it is still
available.

[lane table]: https://github.com/lalrpop/lalrpop/blob/master/lalrpop/src/lr1/lane_table/README.md

To enable it, build with the `LALRPOP_LANE_TABLE=disabled` environment
variable by setting `std::env::set_var` in your `build.rs` and add the
`#[LALR]` attribute above the `grammar;` declaration in your lalrpop grammar
file.
