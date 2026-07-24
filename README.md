# Goshenite Lab Images — `glass-containers`

Distrobox / OCI images carrying the security tooling that **Fedora does not package**.
This is the other half of [Goshenite](https://github.com/Goshenite-Systems/glass)'s
architecture: the immutable host (`glass`) layers only the ~28% of Kali tools that exist
as Fedora RPMs; the other ~72% ship here, as Kali-based distrobox "labs".

## Why Kali-based?

Reverse-engineering Kali's own `kali-tools-*` metapackages showed **404 unique tools,
only 113 (28%) with a Fedora RPM**. Rather than rebuild 291 packages ourselves, each lab
is simply `FROM kalilinux/kali-rolling` + `apt install kali-tools-<bundle>`. Users get the
full Kali toolset per domain, while the host stays a clean Aurora/Bluefin image.

## Labs

20 images, one per bundle that needs containerized tooling (those whose Fedora RPM
coverage was too low to live on the host):

```
database  detect  exploitation  forensics  fuzzing  identify  osint  passwords
post-exploitation  protect  recover  respond  reversing  sniffing-spoofing
social-engineering  voip  vulnerability  web  windows  wireless
```

Each published as `ghcr.io/goshenite-systems/<lab>-lab:latest` and cosign-signed.

## Usage (from a Glass host)

```bash
# create / enter a lab
distrobox create -i ghcr.io/goshenite-systems/web-lab:latest -n web
distrobox enter web
# now sqlmap, nikto, wfuzz, dirsearch, etc. are available

# or one-shot
distrobox run -i ghcr.io/goshenite-systems/web-lab:latest -- sqlmap -u http://target
```

The `glass` bundle manifests declare these as `containers:` references, and the image
generator emits a `distrobox` assemble file (`/usr/share/goshenite/distrobox/<lab>.ini`)
so labs can be created on first use.

## Regenerating

`generate-labs.py` reads `glass-bundles/references/kali/glass-split.json` and writes all
20 `labs/<lab>/Dockerfile`s. Re-run after changing the taxonomy.

## License

[Apache-2.0](./LICENSE) — matching the Universal Blue ecosystem.
