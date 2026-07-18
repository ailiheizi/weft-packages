# Additive product packages

## One package is one product

A Weft package is a product. It lives in a normal directory and can be changed directly. Weft core does not download it, compare versions, verify artifacts, switch sources, resolve dependencies, or build a registry.

A product can expose hooks. Small companion packages can add behavior only through those hooks.

## Layout

A product keeps its own additions next to itself:

products/my-product/
  package.toml
  package.wasm
  addons/my-change/
    package.toml
    package.wasm

To change a product, edit or replace its directory. To add behavior, add one directory below addons. Removing that directory removes the addition.

## Minimal manifest idea

A product declares the hook names it exports. An add-on declares the product it belongs to, one hook name, a phase (before, around, or after), and its entry. A hook name includes its contract generation, such as my_product.turn.before_tools.v1. A breaking product change uses a new hook name.

There is no version matching language, dependency list, priority list, profile, catalog, checksum, package store, or package update protocol.

## Core responsibility

Core loads the selected product and its local add-ons, rejects an add-on that targets a hook the product did not export, and dispatches valid add-ons in stable package-name order. Everything else belongs to normal source control and normal product development.
