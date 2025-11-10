# Module 10: Interview Readiness

Goal: Translate hands‑on skill into crisp, structured explanations and scenario-based reasoning under time constraints.

---
## Domains & Representative Concepts

| Domain | Must-Know Nouns | Action Verbs | Typical Interview Hooks |
|--------|-----------------|-------------|-------------------------|
| OS Internals | Kernel, process, PID, inode | Inspect, trace, prioritize | "What happens when you run a command?" |
| Filesystem | Hierarchy, permissions, links | Navigate, audit, secure | "Explain hard vs symbolic link" |
| Packaging | Repository, dependency, signature | Install, verify, rollback | "apt vs dpkg difference" |
| Services | Unit, cgroup, target | Enable, diagnose, log-read | "Service won't start—walk me through" |
| Scripting | Exit codes, quoting, pipefail | Automate, sanitize, validate | "Why set -euo pipefail?" |
| Text Processing | grep, awk, sort, uniq | Filter, aggregate, correlate | "Find top errors in logs" |
| Networking | DNS, TCP handshake, routing | Resolve, connect, trace | "Slow SSH connection root causes?" |
| Security | Principle of least privilege, key auth | Harden, restrict, audit | "SSH hardening steps?" |
| Automation | Idempotence, convergence | Enforce, orchestrate, verify | "What does idempotent mean in Ansible?" |

---
## Answer Frameworks

| Framework | Structure | Use Case |
|-----------|----------|----------|
| Concept Decomposition | Definition → Why → Example → Edge/Pitfall → Mitigation | Explaining fundamentals |
| Troubleshooting Ladder | Clarify → Reproduce → Observe → Isolate → Fix → Prevent | Scenario failure analysis |
| STAR (Behavioral) | Situation → Task → Action → Result | Past experience questions |
| Comparative | Criteria list → Option A → Option B → Decision rationale | Tool/process comparisons |
| Security Review | Asset → Threat → Control → Residual Risk | Hardening discussions |

Example (Comparative: `apt install` vs `dpkg -i`):
Criteria: dependency resolution, repository metadata, verification.
- `apt install`: Resolves dependencies, uses repo index, handles updates.
- `dpkg -i`: Raw package unpack; no dependency fetching.
Decision: use `dpkg -i` for locally built or manually fetched `.deb`; else `apt` for managed dependency chain.

---
## Scenario Drills
1. Service Fails to Start:
	- Clarify: Which service? Expected behavior?
	- Reproduce: `systemctl start demo`.
	- Observe: `systemctl status`, `journalctl -u demo --since 5m`.
	- Isolate: Environment mismatch vs binary failure vs dependency ordering.
	- Fix: Adjust unit `Environment=` or correct path.
	- Prevent: Add CI test or health check.

2. High CPU Process:
	- Clarify: Sustained or spike? Impacted services?
	- Observe: `top`, `ps -o pid,cmd,%cpu -p <PID>`.
	- Deep Dive: `strace -p <PID>` (short window) or `lsof -p`.
	- Mitigate: Restart? Throttle via cgroup? Root cause code path.

3. Permissions Denied Deploy:
	- Clarify: Operation attempted? User identity?
	- Inspect: `ls -l`, ownership, special bits.
	- Expand: `getfacl` (if ACLs in play).
	- Resolve: Adjust `chown`, refine group membership; avoid world-write.

4. Log Noise Overload:
	- Goal: Extract actionable error pattern.
	- Pipeline: `journalctl -u app -p err --since "1h" | awk '{print $5}' | sort | uniq -c | sort -nr | head`.
	- Outcome: Top recurring error token guides focus.

5. Slow SSH Login:
	- Hypotheses: DNS reverse lookup delay, network latency, auth method ordering.
	- Check: `UseDNS no` in sshd config, test ping, examine `/var/log/auth.log`.
	- Mitigation: Disable DNS lookups, prioritize key auth.

---
## Concise Definitions (Practice Pack)
| Term | Crisp Definition |
|------|------------------|
| Kernel | Core mediator between hardware & processes |
| Inode | Metadata record referencing file content blocks |
| PID | Numeric identifier for a process instance |
| cgroup | Mechanism to track & limit resource usage |
| Idempotent (CM) | Repeated application yields stable identical state |
| Exit Code | Numeric status summarizing command outcome |
| Link (Hard) | Directory entry pointing to same inode |
| Link (Symbolic) | Path pointer referencing another name |
| Package Manager | Tool orchestrating dependencies & updates |
| systemd Unit | Declarative definition of managed entity lifecycle |

---
## Building Strong Answers
Checklist while answering:
1. Start with a crisp definition (10–15 words).
2. Tie to practical relevance (why it matters in ops).
3. Provide minimal example or command.
4. Mention a common pitfall & mitigation.
5. Conclude with extension or boundary.

Time Box: Aim for 45–60 seconds; deeper follow-ups if asked.

---
## Practice Checklist
Foundational:
1. Create 15 flash cards (concept → explanation pattern).
2. Verbally explain strict mode in Bash using decomposition framework.

Intermediate:
3. Simulate failure scenario & narrate troubleshooting ladder while performing commands.
4. Compose a comparative answer: containers vs virtual machines (criteria list).
5. Develop a pipeline summarizing top 5 error codes from logs.

Advanced:
6. Whiteboard a delivery flow (Module 09) narrating each stability lever.
7. Produce a security review for SSH (asset, threat, control, residual risk).
8. Answer a behavioral question using STAR (past production incident). Capture notes.

Stretch:
9. Teach a peer one concept; request feedback on clarity.
10. Record yourself answering 3 random flash cards; refine wording.

---
## Reflection Questions
1. Which framework do you default to? Is it always appropriate?
2. Do your examples demonstrate genuine experience or vague generalities?
3. Which domain feels weakest under time constraint?
4. How can you quantify impact in behavioral answers?
5. Are you balancing precision vs accessibility for non-expert interviewers?

---
## Readiness Rubric
| Dimension | Novice | Progressing | Strong | Excellent |
|----------|--------|------------|--------|-----------|
| Concept Clarity | Definitions ramble | Partial precision | Clear & concise | Teaches effortlessly |
| Structure | Ad-hoc | Some framework use | Consistent frameworks | Adaptive & fluent |
| Scenario Reasoning | Jumps to fix | Linear steps | Structured ladder | Anticipates/prevents |
| Technical Depth | Surface commands | Adds options | Connects layers (cgroup/logs) | Systems view & tradeoffs |
| Communication | Filler words | Mostly clear | Engaging & paced | Persuasive & tailored |

Use rubric to mark each practice session; target “Strong” across board, uplift weak rows.

---
## Exit Criteria
You can:
| Capability | Demonstrated By |
|------------|-----------------|
| Define core terms | Deliver 10 crisp 15‑word explanations |
| Compare tools | Structured pros/cons & rationale (apt vs dpkg) |
| Troubleshoot verbally | Narrate ladder with relevant commands |
| Build pipeline reasoning | Present log aggregation example with rationale |
| Apply frameworks | Use decomposition, comparative, STAR fluidly |
| Reflect & iterate | Identify improvement target from rubric |

---
Next: Recycle modules for gaps or extend into containers/Kubernetes and IaC (Terraform) to broaden scope.