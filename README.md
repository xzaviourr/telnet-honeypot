# Telnet Honeypot with a Simulated Linux Filesystem

A compact Python honeypot that listens for Telnet connections, captures
submitted usernames, passwords, and commands in container logs, and responds
with a fake Linux-like filesystem backed by `pyfakefs`.

> **Security warning:** this is an archived educational prototype, not a
> hardened security control. Run it only on systems and networks you own or
> have explicit permission to monitor. Treat all captured data as sensitive.

## Behavior

- TCP listener on port 23
- prompts for a username and password;
- emulates a small command set (`ls`, `dir`, `cd`, `pwd`, `touch`, `cat`,
  `mkdir`, `rm`, and `rmdir`);
- isolates file operations in an in-memory fake filesystem;
- records connection and interaction events through Python logging; and
- disconnects sessions after roughly five minutes.

The parser uses dynamic command dispatch and assumes well-formed input. It is
not safe to expose directly to the public internet.

## Run with Docker

Docker is the intended environment because binding host port 23 commonly
requires elevated privileges.

```bash
git clone https://github.com/xzaviourr/telnet-honeypot.git
cd telnet-honeypot
docker build -t telnet-honeypot .
docker run --rm -p 127.0.0.1:2323:23 telnet-honeypot
```

Test from the same machine:

```bash
telnet 127.0.0.1 2323
```

Binding to loopback and a non-privileged host port is safer for a demonstration
than publishing port 23 on every interface.

## Logging and safe operation

View logs with `docker logs <container-id>`. Avoid relying on Docker's internal
storage paths, which vary by platform and logging driver.

- Use an isolated lab network and an unprivileged container runtime.
- Never reuse credentials typed into the honeypot.
- Apply retention, access-control, privacy, and incident-response policies to
  captured IP addresses, credentials, and commands.
- Do not retaliate against or interact with remote systems.

## Limitations

The protocol emulation is minimal, sessions are serialized by a global lock,
and malformed/unknown commands can terminate a handler. There are no tests,
rate limits, authentication safeguards, or production observability.

## Maintenance status

The last functional work dates to 2020. The repository is archived, receives no
security updates, and should be treated as a learning artifact.

No license is asserted because the repository has multiple contributors.
