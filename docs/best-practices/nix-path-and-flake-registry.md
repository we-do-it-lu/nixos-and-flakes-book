# Custom NIX_PATH and Flake Registry 

## Introduction to NIX_PATH {#nix-path-introduction}

The Nix search path is controlled by the environment variable `NIX_PATH`, which follows the same format as the Linux `PATH` environment variable, consisting of multiple paths separated by colons.

Paths in Nix expressions that look like `<name>` are resolved using the path named `name` from the `NIX_PATH`.

This usage pattern is no longer recommended under the Flakes feature because it results in Flake builds depending on a mutable environment variable `NIX_PATH`, compromising reproducibility.

However, in certain scenarios, we still need to use `NIX_PATH`, such as when we frequently use the command `nix repl '<nixpkgs>'`, which utilizes the Nixpkgs found through `NIX_PATH` search.

## Introduction to Flakes Registry {#flakes-registry-introduction}

The Flakes Registry is a center for Flake registration that assists us in using shorter IDs instead of lengthy flake repository addresses when using commands like `nix run`, `nix shell`, and more.

By default, Nix looks up the corresponding GitHub repository address for this ID from <https://github.com/NixOS/flake-registry/blob/master/flake-registry.json>.

For instance, if we execute `nix run nixpkgs#ponysay hello`, Nix will automatically retrieve the GitHub repository address for `nixpkgs` from the aforementioned JSON file. It then downloads the repository, locates the `flake.nix` within, and runs the corresponding `ponysay` package.

## Custom NIX_PATH and Flake Registry {#custom-nix-path-and-flake-registry-1}

The roles of `NIX_PATH` and the Flake Registry have been explained earlier.
In daily use, we typically want the `nixpkgs` used in commands like `nix repl '<nixpkgs>'`, `nix run nixpkgs#ponysay hello` to match the system's `nixpkgs`. This requires us to customize the `NIX_PATH` and Flake Registry.

In your NixOS configuration, adding the following module will achieve the mentioned requirements:

```nix
{
  # Make `nix run nixpkgs#nixpkgs` use the same nixpkgs as the one used by this flake.
  nix.registry.nixpkgs.flake = nixpkgs;

  # Make `nix repl '<nixpkgs>'` use the same nixpkgs as the one used by this flake.
  environment.etc."nix/inputs/nixpkgs".source = "${nixpkgs}";
  nix.nixPath = ["/etc/nix/inputs"];
}
```

## References

- [Chapter 15. Nix Search Paths - Nix Pills](https://nixos.org/guides/nix-pills/nix-search-paths.html)
