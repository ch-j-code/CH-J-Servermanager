# CH-J Server Manager

CH-J Server Manager is a desktop SSH and SFTP client for Linux server administration, with Windows server support in selected tools, and a local file hash calculator and checksum verification tool for Windows, macOS, and Linux. It combines server profiles, an interactive terminal, file management, monitoring, log inspection, user administration, NGINX management, and 49 hashing algorithms in one Electron application.

## Download beta

| Platform | Architecture | Package |
| --- | --- | --- |
| macOS | Apple Silicon (`arm64`) | [Download DMG for macOS](https://sm.ch-j.eu/download.php?channel=beta&platform=mac&arch=arm64&lang=en) |
| Windows 11 | `x64` | [Download for Windows](https://sm.ch-j.eu/download.php?channel=beta&platform=win&arch=x64&lang=en) |
| Ubuntu 24.04 | `x64` | [Download for Ubuntu](https://sm.ch-j.eu/download.php?channel=beta&platform=ubuntu&arch=x64&lang=en) |

**Beta `0.1.0`:** back up important data before installation or updates.

[Browse source code](https://sm.ch-j.eu/en/source/) · [Releases](https://sm.ch-j.eu/en/releases/) · [Documentation](https://sm.ch-j.eu/en/documentation/)

## Development status

The project is currently in **beta**. It is suitable for testing, but some planned features and production distribution requirements are not complete yet. The internal name "Core" refers to the current application architecture; the product name remains CH-J Server Manager.

The public desktop catalog checked on **October 24, 2026** lists version `0.1.0` in the **beta** channel, build `core-20261024T121943Z-f4fc7ca3b8b2`, as the default download and the default source code. Earlier `0.0.1` builds remain in the alpha channel; the update channel is selected in the app settings. Check the [release catalog](https://sm.ch-j.eu/en/releases/) for current build IDs and packages. A release package format does not establish stable status.

Version 0.1.0 fixes Windows servers stalling when several tools (for example System Monitor and Users) worked on the same SSH session at once, reports update checks correctly when they finish, and was functionally tested end to end on Ubuntu and Windows 11 Pro. It contains the Disk Manager for Linux servers, a resizable left sidebar with full, compact, two-column and hidden modes and icons, legacy DSA keys inside the packaged app and a password generator in Key Generator. It also contains privileged file operations through the shared sudo executor and resumable transfer queues. Earlier builds added separate Linux and Windows System Monitor collectors with memory and virtual memory management, root SSH access management in Users, the Server Diagnostics workspace (CS/DE/EN), Monaco remote text editing with Core conflict checks and recovery safeguards, and separate SSH response-time and ICMP measurements. See [remote editor and latency details](https://sm.ch-j.eu/en/documentation/ssh-and-sftp/) and [diagnostics capabilities and limits](https://sm.ch-j.eu/en/documentation/server-diagnostics/).

The current desktop installers have detached OpenPGP signatures. The release [SHA512SUMS](https://sm.ch-j.eu/files/apps/builds/core-20261024T121943Z-f4fc7ca3b8b2/SHA512SUMS) and its [signature](https://sm.ch-j.eu/files/apps/builds/core-20261024T121943Z-f4fc7ca3b8b2/SHA512SUMS.asc) cover the DMG, EXE and DEB. Source archives and their release metadata are available in the source catalog.

### Current limitations

The following features are planned but not yet available:

- migration of data from older application versions;
- remote archive extraction;
- SSH jump hosts and port forwarding;
- Safe Mode and additional tools.

This list is not exhaustive. Behavior and data formats may still change during the beta phase.

## Supported operating systems

| Operating system | Current target | Public beta package |
| --- | --- | --- |
| Windows | Windows 11, `x64` | NSIS `.exe` installer |
| macOS | Apple Silicon, `arm64` | `.dmg` |
| Linux | Ubuntu 24.04 LTS, `amd64` | Debian-format `.deb` and signed APT alpha repository |

The current catalog has no Intel Mac installer. Compatibility with other Linux distributions and older Windows versions is not verified; a Debian package format alone does not establish support for every Debian-based system.

## Available features

- encrypted local vault using scrypt and AES-256-GCM;
- server profiles with password and private-key SSH authentication;
- profile import/export in desktop JSON/CSV, with shared version 1 JSON for SM Mobile; transferred fields exclude credentials and trusted host keys;
- mandatory SHA-256 SSH host-key verification and explicit handling of changed host keys;
- multiple SSH sessions and an interactive terminal;
- file browsing, editing, upload, download, deletion, and ZIP/TAR/TAR.GZ export through a restricted SFTP interface;
- Czech, German, and English user interfaces;
- Key Generator: SSH keys (Ed25519, RSA, ECDSA and explicitly confirmed legacy DSA/SSH1 RSA) and a password generator (4–70 characters, A–Z, a–z, 1–9 and `-@)¨§'=+`, optional dash after every four characters), copied through the main process clipboard;
- bundled first-party tools for System Monitor, Key Generator, Log Viewer, Users, File Manager, NGINX Manager, and Disk Manager, with separately distributed plugin updates;
- Disk Manager (`chj.disks`, Linux servers only): disk map, partitions, file systems, LVM and LUKS layers, automatic expansion after a disk was enlarged, and a reviewed queue of changes that runs as a server-side job (see `docs/disks-plugin.md` for supported operations and limits);
- System Monitor and Users for remote Linux servers and Windows servers reached through Windows OpenSSH (see the platform notes below);
- integrated Server Diagnostics: ICMP, traceroute, DNS, HTTP/1.1/2/3 capability tests, server timings, compression, TLS, TCP ports, local history and JSON/CSV/TXT exports;
- a bundled local Hash & Checksum tool in the sidebar for calculation, verification, comparison, and checksum manifests;
- sandboxed plugin windows with capability-based access to Core services;
- dark title bars on Windows and Linux, plugin windows kept above the main window, and a bottom bar for collapsed plugins;
- alpha, beta, and stable update-channel selection; the current desktop release `0.1.0` is published in the beta channel (new installations check the beta channel);
- application updates protected by size checks, SHA-512, and mandatory detached OpenPGP signatures.

### System Monitor and virtual memory

System Monitor detects the remote operating system from system data (`uname`, or the Windows registry / `Win32_OperatingSystem`) and uses a separate Linux or Windows collector. On Windows, it works with `cmd.exe` and PowerShell as the default SSH shell. It does not show a load average on Windows, because Windows has none, and shows the processor queue length instead. When a network driver reports no byte counters, System Monitor shows packet counts.

The **Memory and virtual memory** section shows a RAM breakdown in which no value is counted twice. It also shows RAM and swap history and lists every swap area or page file with its type, path, priority, persistence, and swap-in/swap-out activity. Reading metrics never requires sudo.

- **Linux swap files (root or sudo):** you can create, activate, deactivate, resize, reprioritize, persist, and delete swap files in `/swapfile*`, `/swap/<name>`, or `/var/lib/swap/<name>`, sized from 64 MiB to 64 GiB.
  - Supported file systems are ext2/3/4, XFS, Btrfs, and F2FS.
  - A swap area is not deactivated when its contents would not fit into the available memory, which keeps a reserve, plus the free space in other swap areas.
  - `/etc/fstab` is backed up before every change.
  - Swap partitions can only be deactivated or reprioritized. zram and zswap are read-only.
- **Windows page files (elevated administrator SSH session, UAC is never bypassed):** you can switch automatic management, set custom or system-managed sizes, and add or remove page files.
  - Changes take effect only after a restart. The application shows "restart pending" and **never restarts Windows**.

The terminal status bar measures **ICMP ping** (local system `ping`, with median, packet loss, and a reason such as timeout or blocked) and **SSH response time** (round trips over the existing SSH transport) separately. A blocked ICMP does not mark a server as offline.

### Users: root SSH access

Users protects the root account: you cannot delete it or change its UID/GID, groups, shell, or home directory. You can manage its SSH keys and SSH access through a root session or sudo. This includes `PermitRootLogin` (`no`, `prohibit-password`, which is recommended, `forced-commands-only`, `yes`, or inherited server settings). Enabling password login for root requires an extra typed confirmation.

### Verified remote server systems

The following server operating systems were used for tests on isolated test machines in October 2026. Results from other versions are not verified:

| Family | Tested versions |
| --- | --- |
| Ubuntu | 20.04.6 LTS, 22.04.5 LTS, 24.04.5 LTS, 25.04, 26.04.1 LTS |
| Debian | 12 (bookworm), 13 (trixie) |
| Rocky Linux | 10.2 (Red Quartz); Rocky Linux 9 in disposable containers |
| Alpine Linux | 3.24.2 |
| Kali Linux | Rolling 2026.1 (cloud image 2026.2) |
| Proxmox VE | 9.2 (Debian 13 based) |
| Windows | Windows 11 Pro, Windows 11 Enterprise Evaluation 26H2 |
| Windows Server | Windows Server 2022 Standard Evaluation 21H2 (Desktop Experience), Windows Server 2025 Standard Evaluation 24H2 (Desktop Experience and Server Core) |

Which tool was tested on which system:

| Tool | Verified remote systems |
| --- | --- |
| System Monitor metrics, latency and swap files | Test VMs: Debian 13, Ubuntu 24.04 and 26.04, Rocky Linux 10.2 (XFS), Alpine Linux 3.24, Proxmox VE 9.2. Disposable containers: Debian 13, Ubuntu 24.04, Rocky Linux 9 (ext4, XFS, Btrfs) |
| System Monitor metrics, latency and page file settings | Windows 11 Pro and Windows Server 2025 Core, with no restart performed |
| Disk Manager (changes only on dedicated loop-device test images) | Test VMs: Ubuntu 20.04, 22.04, 24.04, 25.04 and 26.04, Debian 12 and 13, Rocky Linux 10.2, Alpine Linux 3.24, Kali Linux; Proxmox VE 9.2 read-only. Windows 11 Enterprise and Windows Server 2022/2025 are detected and refused as unsupported |
| Users, including root SSH access | Test VMs and containers listed above |

On Proxmox VE, root's `authorized_keys` is a link into the cluster file system, so Users shows it but does not change it. On Alpine Linux, a locked root account cannot log in with SSH, even with a key.

### Server Diagnostics

Open **Server Diagnostics** between **Plugins** and **Hash & Checksum**. Enter a hostname, IPv4, IPv6 or HTTP(S)/WS(S) URL, choose Auto / IPv4 / IPv6 / Both, and run the overview or an individual tool. No SSH connection or external plugin is required. Results update while probes run, and individual tools or the entire run can be stopped.

The module includes per-packet ICMP graphs/statistics (interval 0.05–10 s, total packet count, payload size, TTL and don't-fragment settings, no root required), progressive traceroute, twelve DNS record types with TTL and consistency observations, independently negotiated HTTP versions, request phase timings and independent connections for each HTTP sample, verified gzip/deflate/Brotli (and runtime-supported zstd), TLS 1.2/1.3 certificate inspection, bounded TCP port tests, an SSH host-key handshake without authentication, security-header/redirect checks and an optional WebSocket handshake. It supports Czech, German and English and local light/dark/system themes.

HTTP/1.1, HTTP/2 and HTTP/3 use the pinned bundled curl client, with strict certificate verification and no system curl dependency. HTTP/3 uses `--http3-only` and requires a completed QUIC transfer on the target HTTPS port; otherwise the UI reports unavailable, unsupported or error with a reason. `Alt-Svc` and HTTP/2 fallback never establish HTTP/3 support. DNSSEC is explicitly **not validated**; DS/DNSKEY records are observations. ICMP failures do not label a server offline.

History stays in the application's local data directory. JSON/CSV/TXT reports omit bodies, cookies, authentication metadata and URL queries. Import JSON, reopen or compare saved runs from History. No new npm dependencies are required; OS ping/traceroute tools provide ICMP/route capabilities. The HTTP client and its CA store are bundled; no system curl or first-run download is required. Each HTTP sample starts an independent connection; connection reuse is not measured.

See [implementation, IPC, limits and verification](https://sm.ch-j.eu/en/documentation/server-diagnostics/). Run `npm test` for deterministic network tests and `npm run test:diagnostics:ui` for the Electron UI smoke test (requires a graphical desktop).

### Hash & Checksum

Calculate file hashes, verify checksums, and compare local files with 49 algorithms, including SHA-256, SHA-512, SHA-3, BLAKE3, and xxHash. The built-in hashing tool supports HEX and Base64 output and GNU, BSD, and SFV checksum manifests.

Open **Hash & Checksum** from the left sidebar, directly below **Server Diagnostics**, after unlocking the vault. The tool is bundled with the application, opens in its own window, and does not appear in the installed-plugin list.

The interface follows the application's saved language setting: **Czech, German, or English**. Saving a language change updates an already open Hash window, including controls, progress, result statuses, errors, and native file-dialog labels, while preserving selected files, algorithms, parameters, and results.

Available functions:

- **Calculate:** hash a single file, multiple files, or a directory, optionally including subdirectories. Use the recommended selection (SHA-256, SHA-512, and BLAKE3), select individual algorithms, or select all 49 at once. Directory symlinks are skipped.
- **Output and parameters:** display digests as lowercase HEX, uppercase HEX, or Base64. Configure output length for SHAKE and KangarooTwelve, seeds for xxHash and MurmurHash3, and hexadecimal keys for SipHash and HighwayHash. An empty key field uses an all-zero key.
- **Verify:** compare a file with an expected HEX or Base64 digest using a selected fixed-length algorithm. HEX comparison ignores letter case and surrounding whitespace. Suggested algorithms based on HEX length are hints, not definitive identification.
- **Compare Files:** compare the calculated digests of two files using one or more fixed-length algorithms.
- **Manifests:** generate and verify GNU, BSD, and SFV checksum lists with relative paths. Generation requires a fixed-length algorithm without a key or seed; SFV requires CRC32. Verification supports automatic algorithm detection or a manual override when the digest length is ambiguous.
- **Progress and results:** view processed bytes, percentage, throughput, elapsed time, estimated remaining time, and per-file statuses; cancel a running job; copy an individual digest or all results; export results to a text file.

#### Supported algorithms

The complete list of **49 supported algorithms** is shown below. Algorithm IDs match the result table and exported results.

| Algorithm | ID | Output size (bits) |
| --- | --- | --- |
| SHA-224 | `sha224` | 224 |
| SHA-256 | `sha256` | 256 |
| SHA-384 | `sha384` | 384 |
| SHA-512 | `sha512` | 512 |
| SHA-512/224 | `sha512-224` | 224 |
| SHA-512/256 | `sha512-256` | 256 |
| SHA3-224 | `sha3-224` | 224 |
| SHA3-256 | `sha3-256` | 256 |
| SHA3-384 | `sha3-384` | 384 |
| SHA3-512 | `sha3-512` | 512 |
| SHAKE128 | `shake128` | Variable (default 256) |
| SHAKE256 | `shake256` | Variable (default 512) |
| BLAKE2b-512 | `blake2b-512` | 512 |
| BLAKE2s-256 | `blake2s-256` | 256 |
| BLAKE3 | `blake3` | 256 |
| KangarooTwelve | `kangaroo-twelve` | Variable (default 256) |
| RIPEMD-160 | `ripemd160` | 160 |
| Whirlpool | `whirlpool` | 512 |
| Tiger | `tiger` | 192 |
| Tiger2 | `tiger2` | 192 |
| MD5 | `md5` | 128 |
| SHA-1 | `sha1` | 160 |
| XXH32 | `xxh32` | 32 |
| XXH64 | `xxh64` | 64 |
| XXH3-64 | `xxh3-64` | 64 |
| XXH3-128 | `xxh3-128` | 128 |
| MurmurHash3 x86 32 | `murmur3-x86-32` | 32 |
| MurmurHash3 x86 128 | `murmur3-x86-128` | 128 |
| MurmurHash3 x64 128 | `murmur3-x64-128` | 128 |
| CityHash32 | `cityhash32` | 32 |
| CityHash64 | `cityhash64` | 64 |
| CityHash128 | `cityhash128` | 128 |
| FarmHash32 | `farmhash32` | 32 |
| FarmHash64 | `farmhash64` | 64 |
| FarmHash128 | `farmhash128` | 128 |
| HighwayHash64 | `highwayhash64` | 64 |
| HighwayHash128 | `highwayhash128` | 128 |
| HighwayHash256 | `highwayhash256` | 256 |
| SipHash-2-4 | `siphash-2-4` | 64 |
| FNV-1 32 | `fnv1-32` | 32 |
| FNV-1 64 | `fnv1-64` | 64 |
| FNV-1a 32 | `fnv1a-32` | 32 |
| FNV-1a 64 | `fnv1a-64` | 64 |
| CRC-16/CCITT-FALSE | `crc16-ccitt-false` | 16 |
| CRC-32/ISO-HDLC | `crc32` | 32 |
| CRC-32C/Castagnoli | `crc32c` | 32 |
| CRC-64/ECMA-182 | `crc64-ecma` | 64 |
| CRC-64/XZ | `crc64-xz` | 64 |
| Adler-32 | `adler32` | 32 |

SHAKE128, SHAKE256, and KangarooTwelve allow an output length of **16–1024 bytes**. Their defaults are 32, 64, and 32 bytes respectively. BLAKE3 currently produces a fixed 256-bit digest.

MD5 and SHA-1 are included only for legacy compatibility. xxHash, MurmurHash3, CityHash, FarmHash, HighwayHash, SipHash, FNV, CRC, and Adler-32 are not offered as cryptographic integrity proofs.

#### Plugin API

The Plugin API in the current source tree is `1.7.0`. Plugins requiring `^1.0.0` through `^1.6.0` remain compatible. Version 1.6 adds the `disks.read` and `disks.manage` permissions for the bundled Disk Manager; version 1.7 adds the Key Generator password generator (`keys.password`, `keys.copyPassword`) under the existing `keys.generate` permission. Version 1.4 adds the `system.memory.manage` permission for controlled swap and page-file changes. Version 1.5 adds `files.privileges`, optional sudo options for File Manager operations and the resumable `files.transfers.*` Transfer Manager API (existing `files.transfer` permission). Hash & Checksum is a bundled first-party plugin and uses only the narrow `local.hash` permission.

Core owns every native file, directory, manifest, and export dialog. It gives the sandboxed plugin opaque, plugin-owned selection tokens rather than raw paths, rejects tokens belonging to another plugin, and keeps filesystem access out of the renderer. Hash jobs run in worker threads, stream bounded chunks, support progress and cancellation, and verify stable file identity before returning results. Directory enumeration skips symlinks. Manifest verification rejects symlink escapes, traversal, absolute paths, drive paths, and UNC paths.

Hashing is local-only: the plugin has no network, SSH, or SFTP hashing capability and does not upload file contents or digests. It supports individual files, batches, recursive directories, file comparison, and GNU, BSD, and SFV manifests across 49 algorithms. MD5 and SHA-1 are retained only for legacy compatibility; fast hashes and checksums are explicitly non-cryptographic.

## Download and installation

Use the [download links at the top of this README](#download-beta) or the official [release catalog](https://sm.ch-j.eu/en/releases/).

The [CH-J Server Manager release catalog](https://sm.ch-j.eu/en/releases/) lists the packages and channels that are actually available.

Beta distribution notes:

- macOS builds currently target Apple Silicon only. Test builds may be ad hoc signed and can require manual approval in **Privacy & Security** until Developer ID signing and notarization are enabled.
- Windows builds use an NSIS installer; Windows production code signing is not yet confirmed.
- Ubuntu/Debian builds use a `.deb` package. For an initial installation, use `sudo apt install ./<downloaded-file>.deb` if the graphical software center rejects the package as coming from an unknown publisher.
- Back up important connection details before testing a beta update.

### Ubuntu APT repository

Ubuntu amd64 users can install and update through the signed [CH-J APT alpha repository](https://sm.ch-j.cz/apt/). See [key verification, repository setup and installation](https://sm.ch-j.eu/en/documentation/updates-and-gpg/). APT installations display operational guidance and require license acceptance when the application first starts and after updates. Use APT for updates to repository installations and avoid starting the application updater during an APT transaction.

### First launch

On first launch, choose the interface language, review the operational guidance, accept the license and acknowledge the data-loss and backup notice, then create a vault master password. Then add a profile under **Servers** and connect from **Terminal**.

The vault password cannot be recovered. **Forgot password / reset vault** deletes encrypted server profiles, saved SSH passwords, trusted host fingerprints, and other encrypted data. Update settings and installed plugins are preserved.

Private-key passphrases are intentionally not stored and must be entered for each connection. An SSH account password can optionally be stored in the encrypted vault.

## Security, encryption, backups and license

Update verification is performed in the Electron main process and fails closed. The application downloads the selected artifact and its detached `.asc` signature, checks the expected size and SHA-512 hash, and verifies the signature with the bundled CH-J public key. Immediately before installation, it re-checks the same private-cache files to reduce time-of-check/time-of-use risk. The renderer cannot supply an artifact path, public key, fingerprint, or a forged verification result.

The long-term update trust anchor is the primary OpenPGP fingerprint:

```text
0D92 778A D8EC F85C 80E3  9248 48F2 433A D9CD F453
```

To verify a download manually, obtain the bundled [public key](https://sm.ch-j.eu/ch-j-signing-public.asc), check that its primary fingerprint matches the value above, and import it with `gpg --import ch-j-signing-public.asc`. Verify the downloaded manifest with `gpg --verify SHA512SUMS.asc SHA512SUMS`, then check the files with `shasum -a 512 -c SHA512SUMS` (macOS) or `sha512sum -c SHA512SUMS` (Linux). Windows PowerShell provides `Get-FileHash -Algorithm SHA512 <file>`. Each installer also has a detached `.asc` signature that can be checked with `gpg --verify <file>.asc <file>`.

Valid signing subkeys may be rotated as long as they remain cryptographically bound to this primary key and are valid for signing. Revoked, expired, unknown, malformed, or otherwise invalid keys and signatures block installation.

The beta service is still undergoing distribution hardening. In particular, standard CA verification for the explicitly allowlisted update host is temporarily relaxed in test mode, macOS production signing/notarization is not yet enabled, and Windows production code signing is not yet confirmed. OpenPGP verification remains mandatory for application update artifacts, but alpha builds should not be treated as production releases.

When connecting to a server for the first time, verify the displayed SSH host-key fingerprint through another trusted channel. If a known host key changes, verify both the old and new fingerprints before accepting the replacement.

### Vault and platform security

Optional Touch ID, Windows Hello (including PIN) and Ubuntu fingerprint unlock integrate with the existing encrypted Vault, with master-password fallback, CS/DE/EN settings, inactivity locking and optional focus locking. Windows and Linux provide convenience unlock; macOS seals the Vault key to a Touch ID-gated Secure Enclave key. **Locking disconnects SSH sessions and closes plugins.** Native hardware authentication still needs interactive verification. See [setup, dependencies, platform security differences and verification](https://sm.ch-j.eu/en/documentation/security-and-backups/).

On macOS, `EHOSTUNREACH` for local SSH servers may require allowing **CH-J Server Manager** under **System Settings → Privacy & Security → Local Network**, then restarting the app. This build declares the usage reason and explains the error. Ubuntu packages include standard icon sizes and a matching desktop/window identity.

### Backups and data loss

Create and test appropriate backups of remote data and connection information before risky operations or beta updates. Verify that restoration works. The application does not guarantee that backups exist or are recoverable. Profile export transfers connection metadata only; it is not a backup of the encrypted Vault, saved credentials or remote files.

### License and mandatory acceptance

This edition of CH-J Server Manager is expressly distributed under the **CH-J Proprietary Software License, version 1.14** (October 11, 2026). See the [complete license](https://sm.ch-j.eu/en/license/). Commercial and non-commercial use are free of license fees indefinitely under its terms. Third-party materials retain their separate licenses and notices; this application license does not replace them.

The license integration described here applies to newly built packages. Existing published downloads retain their original contents and licensing information.

Website downloads start immediately with the Download button, without a confirmation page. License and operational guidance remain available on the website and in the app. Interactive Windows NSIS installation displays the guidance before the complete license and requires license acceptance. macOS DMG, Debian/Ubuntu and silent installation defer guidance and license acceptance to first launch; the application displays the guidance before license acceptance at first launch. Every platform requires per-user acceptance before using a new or updated build, including updates with the same version number. The required checkbox combines license consent with acknowledgment of data-loss risks, the need for appropriate backups before risky operations, and the fact that the application does not guarantee backup existence or recoverability. Declining exits the application. The acceptance record is local; no activation or reporting is required. See [license acceptance and packaging](https://sm.ch-j.eu/en/license/).

The application bundles [documentation](https://sm.ch-j.eu/en/documentation/), including [Provozní bezpečnost a zálohování (česky)](https://sm.ch-j.cz/cs/source/?path=docs%2Fprovozni-bezpecnost-a-zalohovani.md), [Operational Safety and Backup Guidance (English)](https://sm.ch-j.eu/en/source/?path=docs%2Foperational-safety-and-backup-guidance.md) and [Betriebssicherheit und Datensicherung (Deutsch)](https://sm.ch-j.de/de/source/?path=docs%2Fbetriebssicherheit-und-datensicherung.md). Displaying these recommendations is mandatory before proceeding; following them remains optional. They are informational and non-binding, are not part of the license and do not require separate acceptance. The application displays them again before license acceptance after every update, or if the bundled guidance changes. They are available in the application under **Settings → Licenses and documentation** and on the [website license page](https://sm.ch-j.eu/en/license/).

## Documentation and source catalog

[Browse the application source](https://sm.ch-j.eu/en/source/) on the CH-J website, with directories, file views, line links and a complete source archive. Source availability does not grant rights beyond the [CH-J Proprietary Software License 1.14](https://sm.ch-j.eu/en/license/). The [GitHub repository](https://github.com/ch-j-code/CH-J-Servermanager) contains only this README; the source and release downloads are hosted on the CH-J website.

- [Getting started and documentation](https://sm.ch-j.eu/en/documentation/)
- [SSH, SFTP and remote editing](https://sm.ch-j.eu/en/documentation/ssh-and-sftp/)
- [Server Diagnostics](https://sm.ch-j.eu/en/documentation/server-diagnostics/)
- [Security and backups](https://sm.ch-j.eu/en/documentation/security-and-backups/)
- [Updates, signatures and APT setup](https://sm.ch-j.eu/en/documentation/updates-and-gpg/)
- [Desktop source catalog — Czech](https://sm.ch-j.cz/cs/source/)
- [Desktop source catalog — English](https://sm.ch-j.eu/en/source/)

The source browser provides directories, file views, line links and release archives. Development commands below apply to an authorised source archive downloaded from that catalog. This GitHub repository contains only `README.md`; application paths mentioned in this document refer to the downloaded sources.

## CH-J Ecosystem — Desktop & Mobile

Two independent applications share the CH-J server-management focus. Each connects directly to servers; neither requires the other application or a CH-J account. SM Mobile is a separate Flutter application for phones and tablets.

| | CH-J Server Manager | SM Mobile |
| --- | --- | --- |
| Environment | Electron desktop application | Flutter / Dart mobile application |
| Platforms | Windows, macOS and Linux; current package targets are listed above | Android 11+ and iOS/iPadOS 15+; phone and tablet layouts |
| Server access | Multiple SSH sessions, interactive terminal, SFTP and Monaco remote editing | One active SSH session, PTY terminal and SFTP browsing/transfers; no remote text editor |
| Administration | Diagnostics, local Hash & Checksum and first-party management tools | Foreground Linux monitoring and IPv4/IPv6 DNS/TCP diagnostics; no desktop plugin system or local hashing tool |
| Profile exchange | JSON version 1 and desktop CSV | Shared JSON version 1; no CSV |
| Status | Beta `0.1.0` | Development alpha `0.0.1`; Android packages and unsigned iOS development artifacts on the official website |
| Project README | [Desktop on GitHub](https://github.com/ch-j-code/CH-J-Servermanager) | [SM Mobile on GitHub](https://github.com/ch-j-code/SM-Mobile) |
| Official website | [Desktop](https://sm.ch-j.cz/cs/) | [Mobile](https://sm.ch-j.cz/cs/?edition=mobile) |
| Source catalog | [Desktop sources](https://sm.ch-j.cz/cs/source/) | [Mobile sources](https://sm.ch-j.cz/cs/source/?edition=mobile) |

The shared `chj-server-profiles` JSON format, version 1, transfers only profile name, host, username and port. Passwords, private keys, passphrases and host-key fingerprints are excluded. Configure authentication and verify host keys after import. The two Vault formats are separate and cannot be exchanged.

SM Mobile's tablet layout is implemented, while physical iPad validation remains outstanding. Its diagnostics do not provide ICMP or traceroute, and monitoring stops outside the foreground. The website lists a signed Android APK for installation and an AAB for distribution tooling. The iOS IPA requires your own Apple signing and provisioning; the simulator ZIP cannot be installed on a phone. See the [mobile README](https://github.com/ch-j-code/SM-Mobile#readme) for capabilities, distribution and validation limits.

## Official websites

- [CH-J Server Manager — Czech](https://sm.ch-j.cz/cs/)
- [CH-J Server Manager — German](https://sm.ch-j.de/de/)
- [CH-J Server Manager — English](https://sm.ch-j.eu/en/)
- [SM Mobile — official website](https://sm.ch-j.cz/cs/?edition=mobile)
- [SM Mobile — downloads and build details](https://sm.ch-j.cz/cs/releases/?edition=mobile)

## Project and development

The desktop source archive contains a complete, self-contained application source tree. It can be copied into an otherwise empty working directory and used for dependency installation, testing, development startup, and supported platform builds without any sibling source projects.

The application communicates with an update service through its public HTTPS contract and supports independently distributed plugin packages through Plugin API 1.7. External services, plugin sources, and release tooling are not runtime, test, or build dependencies. Bundled first-party plugins in `src/main/firstPartyPlugins/` and the internal plugin framework in `src/main/plugins/` are included in this application tree.

### Development from the official sources

Requirements:

- Node.js 24 LTS (24.18 or newer);
- npm;
- the native toolchain required by Electron dependencies on the host platform.

Use an authorised copy of the supplied application sources and a separate `node_modules` installation on each operating system:

```bash
cd /path/to/supplied/app
npm ci
npm run native:build
npm test
npm start
```

The application sources use one shared `package.json` and one shared `package-lock.json` for all supported platforms. Build on the target operating system with:

```bash
npm run build:mac
npm run build:win
npm run build:linux
```

The configured outputs are a macOS DMG (`arm64`), a Windows NSIS installer (`x64`), and a Debian package (`x64`). The macOS DMG is used for website installation and manual/application updates; it contains the complete application and an Applications shortcut. No internal updater ZIP is required by the custom updater. Generated packages are written to `dist/` within the extracted application tree. The release preparation step synchronises the license and guidance, generates third-party notices and assigns a new build ID.

### Topics

[#ssh](https://github.com/topics/ssh) · [#sftp](https://github.com/topics/sftp) · [#server-management](https://github.com/topics/server-management) · [#hash-calculator](https://github.com/topics/hash-calculator) · [#checksum](https://github.com/topics/checksum) · [#file-integrity](https://github.com/topics/file-integrity) · [#sha256](https://github.com/topics/sha256) · [#sha512](https://github.com/topics/sha512) · [#sha3](https://github.com/topics/sha3) · [#blake3](https://github.com/topics/blake3) · [#xxhash](https://github.com/topics/xxhash) · [#electron](https://github.com/topics/electron) · [#linux](https://github.com/topics/linux) · [#macos](https://github.com/topics/macos) · [#windows](https://github.com/topics/windows)
