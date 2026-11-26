# homebrew-mumei

Homebrew tap for the [Mumei](https://github.com/mumei-lang/mumei) formal verification toolchain.

## Install

```sh
brew tap mumei-lang/mumei
brew install mumei
```

The formula currently packages Mumei `0.6.23` for supported macOS and Linux architectures. It installs:

- the `mumei` executable;
- the verified standard-library sources under `share/mumei/std`;
- packaged proof certificates when present;
- the aggregate standard-library proof bundle when present;
- `etc/mumei/env.sh` with `MUMEI_STD_PATH` and proof-artifact environment variables.

Z3 and LLVM 17 are declared as Homebrew dependencies.

## Use the packaged standard library

Either source the installed environment file:

```sh
source "$(brew --prefix)/etc/mumei/env.sh"
```

or set the variables in your shell profile as shown by `brew info mumei` / the installation caveats.

A packaged module certificate can be re-verified with:

```sh
mumei verify-cert "$MUMEI_PROOF_CERTS/<module>.proof.json" "$MUMEI_STD_PATH/<module>.mm" --strict
```

Certificate verification checks the packaged evidence against the packaged source. It does not by itself prove compiler correctness, runtime/ABI behavior, producer authority, or properties outside the certificate's declared semantics and scope.

## Validate this tap

The repository validation path is:

```sh
brew ruby -- -c Formula/mumei.rb
if brew tap | grep -qx 'mumei-lang/mumei'; then
  HOMEBREW_NO_AUTO_UPDATE=1 brew untap mumei-lang/mumei
fi
HOMEBREW_NO_AUTO_UPDATE=1 brew tap mumei-lang/mumei "$PWD"
HOMEBREW_NO_AUTO_UPDATE=1 brew trust mumei-lang/mumei
HOMEBREW_NO_AUTO_UPDATE=1 brew audit --strict --online mumei-lang/mumei/mumei
HOMEBREW_NO_AUTO_UPDATE=1 brew fetch --force mumei-lang/mumei/mumei
```

`brew test` can fail on older Linux distributions when a release artifact requires newer glibc symbols than the host provides; treat that as a platform compatibility result rather than a formula syntax failure.

## Repository layout

- `Formula/mumei.rb` — generated Homebrew formula for the current release artifacts.

The formula is normally updated by the Mumei release workflow. Manual version or checksum edits should be verified against the published release artifacts before opening a PR.
