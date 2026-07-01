# Digital forensics game "Rhino Hunt"

Linear game for [CyberRangeCZ Platform](https://docs.platform.cyberrange.cz/).

A network administrator's IDS flags illegal traffic. Trainees must analyze
three network packet captures and a raw USB drive image to recover hidden
evidence, crack passwords, break steganography, and correlate findings
across sources — building a complete forensic case.

## Game Levels Summary

- network forensics: extracting credentials from a `telnet` stream (Wireshark/tshark)
- carving files out of FTP and HTTP data streams
- cracking a password-protected ZIP archive (`fcrackzip`)
- raw disk image (`dd`) file carving with `foremost`/`scalpel`
- reading a recovered document for case-relevant facts
- detecting and breaking JPHide steganography (`stegdetect`/`stegbreak`/`jpseek`)
- correlating evidence across sources using MD5 hashes

## Topology summary

| Host        | Image | Flavor         |
| ----------- | ----- | -------------- |
| workstation | kali  | standard.small |

## Evidence

Evidence is downloaded automatically during provisioning from the NIST
CFReDS archive (`DFRWS2005-RODEO.zip`) into `/home/user/evidence` on the
`workstation` host. No manual upload of evidence is required.

## Provisioning structure

Matches the real convention used by `library-secret-laboratory` /
`library-locust-3302`: a `playbook.yml` with (1) a shared `hosts` role
applied to all hosts, (2) a per-host play using `user-access` + a role
named after the host + `chrony`, and (3) a final `sandbox-logging` play.

```
provisioning/
├── playbook.yml                   # entry point, 4-part structure (see below)
├── requirements.yml                # ⚠️ PLACEHOLDER — see "Open items" below
└── roles/
    └── workstation/                # role named after the host, per convention
        ├── tasks/main.yml          # installs forensic tools, downloads/extracts evidence
        └── vars/main.yml           # aliases (host → IP), routings
```

**Open items that still need to be verified against your CyberRangeCZ
Platform instance before this is production-ready:**

1. **`requirements.yml` is a placeholder.** The playbook references four
   shared roles (`hosts`, `user-access`, `chrony`, `sandbox-logging`) that
   are not defined in this repo — same as in the sample games. They must be
   pulled in via `requirements.yml`, but the real source (Galaxy collection
   name, or internal Git URL) has not yet been confirmed. **Provisioning
   will fail until this is fixed.**
2. **Exact image name.** `kali-2020.4` is the example given in the official
   Topology Definition docs; confirm the real available image name/version
   on your platform and update `topology.yml`.
3. **Outbound internet access during provisioning.** The evidence-download
   task in `roles/workstation/tasks/main.yml` requires reaching
   `cfreds-archive.nist.gov` over the WAN during provisioning. If outbound
   internet is restricted on your sandboxes, mirror the evidence archive
   somewhere reachable and update the `get_url` task.
4. **Tool availability on the base image.** If your `kali` image already
   ships with the forensic tools listed in the role, the `apt install` task
   is harmless (idempotent) but can be trimmed for faster provisioning.

## License

This repository uses a dual licensing approach:

- The code (Ansible, topology, and custom code) is licensed under the terms
  of the MIT License (<https://opensource.org/license/mit>).
- The game design is licensed under a Creative Commons Attribution 4.0
  International License (CC BY 4.0).

See [ATTRIBUTION.txt](./ATTRIBUTION.txt) for the full attribution of the
underlying forensic dataset.

**Adapted for CyberRangeCZ Platform by:** [your name / team]
