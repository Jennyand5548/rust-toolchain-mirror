# rust-toolchain-mirror

Unmodified, GPG-signature-verified mirrors of the official Rust **standalone
installers** from `static.rust-lang.org`, republished as GitHub Releases.

**Why this exists:** some sandboxed/restricted environments can reach
`github.com` but not `static.rust-lang.org` (where `rustup` normally
downloads from). This repo re-hosts the exact same official files so they
can be fetched from GitHub instead.

## What's in each release

For every mirrored version, both a tarball and its detached signature are
published for every officially supported target:

```
rust-<version>-<target>.tar.gz
rust-<version>-<target>.tar.gz.asc
```

Nothing is rebuilt or modified — these are byte-for-byte the same files
Rust publishes at `static.rust-lang.org/dist/`, verified in CI against
Rust's official signing key (fingerprint `108F 6620 5EAE B0AA A8DD  5E1C
85AB 96E6 FA1B E5FE`) before being uploaded here. See
`.github/workflows/mirror-toolchain.yml` for the exact verification steps.

## How to use a downloaded tarball

**Important:** unlike a plain archive, extracting the tarball alone is
**not enough** to get a working `rustc`/`cargo`. The standalone installer
ships `rustc`, the standard library, and `cargo` as separate components
that must be merged together by its `install.sh` script. Just adding the
extracted `bin/` folders to `PATH` will fail with:

```
error[E0463]: can't find crate for `std`
```

Correct usage:

```bash
curl -LO https://github.com/Jennyand5548/rust-toolchain-mirror/releases/download/v<version>/rust-<version>-x86_64-unknown-linux-gnu.tar.gz
tar xzf rust-<version>-x86_64-unknown-linux-gnu.tar.gz
cd rust-<version>-x86_64-unknown-linux-gnu

# Install into a local, non-root prefix (no sudo needed):
./install.sh --prefix="$HOME/rust-local" --disable-ldconfig

export PATH="$HOME/rust-local/bin:$PATH"
export LD_LIBRARY_PATH="$HOME/rust-local/lib:$LD_LIBRARY_PATH"

rustc --version
cargo --version
```

## Verifying a download yourself

```bash
curl -LO https://static.rust-lang.org/rust-key.gpg.ascii
gpg --import rust-key.gpg.ascii
gpg --verify rust-<version>-x86_64-unknown-linux-gnu.tar.gz.asc \
             rust-<version>-x86_64-unknown-linux-gnu.tar.gz
```

## Updating

The workflow runs automatically once a month, and can also be triggered
manually from the **Actions** tab. It skips any platform/version already
mirrored, so re-runs are fast and don't create duplicate work.

## License

Rust itself is dual-licensed under [MIT](https://opensource.org/licenses/MIT)
and [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0). These
binaries are redistributed unmodified under those same terms. This repo
adds no additional license terms of its own — see the `LICENSE-MIT` and
`LICENSE-APACHE` files bundled inside each tarball for Rust's own license
text.
