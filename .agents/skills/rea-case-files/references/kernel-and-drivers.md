# Case file — kernel & drivers

> **Scope:** your own software or hardware, a CTF/crackme, a vendor-authorized
> bug bounty, or incident-response work you're permitted to do. This
> project's authorized-use policy ([DISCLAIMER.md](../../../../DISCLAIMER.md))
> applies to everything below. Never against a system, account, or session
> you don't own or weren't given explicit permission to test.

From a first static read of a driver to validating whether a CVE's fix
actually closes every path. This is reachability analysis: proving a
mechanism exists and writing down exactly how it works, for a driver's own
author, a bug-bounty program, or a CTF challenge — not building anything to
run against a system you don't control. Scope every template below to a
driver you own or are explicitly authorized to assess: your own driver
under review, a CVE you're validating for a bug bounty, a CTF kernel
challenge, or a hardening review. Static analysis only unless stated
otherwise — none of these load the driver. `inspect_kernel_module` reads
Linux `.ko` metadata without a kernel or `modinfo`; see the
[kernel module guide](https://github.com/morluto/rea/blob/main/docs/kernel-modules.md).

### Driver inspection (Windows `.sys` / Linux `.ko`)

```text
Analyze <driver binary> with REA. Identify the format and target OS
version, the entry/init routine, the device object and dispatch table,
every IOCTL code with its input/output buffer validation, the
callbacks/notifications it registers, and any memory it reads or writes
outside its own allocations. Map each finding to function/address evidence.
Static analysis only — do not load the driver.
```

### IOCTL attack-surface enumeration

```text
Enumerate every control code <driver> handles. For each: the handler
function, how it validates input/output buffers (probe methods, size
checks, IRQL assumptions), and any copy_to_user/copy_from_user or memcpy
with attacker-controlled length. Rank the handlers by risk, with the
evidence behind the ranking — not just a gut-feel order.
```

### Kernel subsystem or syscall behavior

```text
Explain how <subsystem/syscall> in <kernel binary or module> processes
<input>. Trace from the syscall/dispatch entry through the parsing
functions, show bounds checks (or their absence), the data structures
populated, and the error paths. Cite addresses for each step.
```

### Vulnerable-driver primitive analysis (your own driver)

```text
Document the read/write primitive in <driver .sys> that I own or am
authorized to assess. Identify which IOCTL gives arbitrary physical or
virtual memory R/W, exactly how (or whether) the handler validates the
input buffer, the struct layout it expects, and the shortest reproducible
sequence that demonstrates the primitive against my own test buffer. Give
handler addresses and name the missing check that makes it possible.
```

### CVE reachability validation on a patched build

```text
Check whether the fix for <CVE-ID> is complete in <kernel binary/module
version X>. Locate the patched function, verify the added check covers
every caller path that reaches it, then search the rest of the binary for
the same unchecked pattern elsewhere (same struct, different entry point).
Report fix present/absent, any uncovered path found, and any sibling
location still missing the check — each explicitly labeled hypothesis or
confirmed, with addresses.
```

### Race condition / TOCTOU hunt

```text
Find handlers in <driver> that touch shared state (global buffers, device
extension fields, linked lists) from multiple IRQLs or concurrent IOCTLs.
Identify where a check and its later use are separated without a lock or
atomic reference, or where a use-after-free window opens between
close/unload and a pending operation. For each candidate: the two racing
paths with addresses, the shared object, and the smallest controlled test
that would confirm or refute the race.
```

### Reference-count / lifetime bug hunt (UAF candidates)

```text
For every object type <driver/kernel module> allocates and later frees,
trace whether any path can use the object after its last release — a
missing reference before storing a pointer, decrement-then-use ordering, a
callback registered without a hold. For each candidate: the
alloc/free/use address triple, the path reaching the stale use, and the
smallest controlled test that would confirm it. Label every finding a
hypothesis until it's actually validated.
```

### Kernel attack-surface ranking

```text
Enumerate every user-reachable entry point in <kernel module/driver>
(IOCTLs, syscalls, netlink/socket handlers, minifilter callbacks). For
each, record input-validation depth (probe method, size checks, pointer
dereference), the state it touches, and its privilege context. Rank the
entry points by risk with evidence behind the ranking, and name the top
three worth a deeper look.
```
