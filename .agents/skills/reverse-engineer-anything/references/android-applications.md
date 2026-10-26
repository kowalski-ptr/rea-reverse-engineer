# Android application artifacts

Use the connected server's advertised Android tools — repository main and npm
4.1.0 include this family. Installing the skill alone can't add it to an
older server.

## Walk the package

Start with `inspect_android_package` on the caller's APK path: it returns
package identity, manifest declarations, coverage, and inline Evidence. From
there:

- **`search_android_classes`** finds candidates.
- **`inspect_android_class`** lists one class's members.
- **`inspect_android_method`** decompiles.
- **`trace_android_references`** finds incoming static references.

Each call takes its own APK path and explicit selectors — don't open a native
session first. When methods are ambiguous, pick an `overload_index` from the
class inventory instead of guessing.

## Bring your own JADX and Java

The provider needs a separately supplied headless JADX MCP JAR 0.7.1 and
Java 17 or newer. `REA_JADX_MCP_JAR` selects the absolute JAR path;
`JAVA_HOME` can select Java. Run `rea doctor --provider jadx --json` to check
the JADX configuration and get remediation for missing prerequisites. Java
and the headless JADX MCP JAR stay caller-supplied — REA setup never installs
or configures them. A clean configuration check doesn't establish that a
particular APK will analyze successfully.

## Host boundaries and deadlines

Linux and macOS use the POSIX owned-process boundary. Windows x64 requires
the matching bundled native controls and uses an owned Job Object with
bidirectional stdio. Every host needs a full JDK, including `jdk.compiler`;
on Windows, `JAVA_HOME` selects `bin\java.exe`. Consult the Android guide for
actual host verification coverage.

Calls use one worker and provider deadlines:

- `REA_JADX_HEAP_MIB` selects the JVM heap.
- `REA_JADX_OPERATION_TIMEOUT_MS` sets the per-operation deadline — integer
  milliseconds, 1 to 2147483647, default 120000.

To change that deadline on a running MCP server, set
`jadx.operation_timeout_ms` through `update_analysis_settings`, then retry
once work and cleanup settle: newly accepted operations pick up the override,
but work already accepted keeps its original budget. Changing the launch
environment instead requires a new process. The MCP client's own tool timeout
must also allow whichever deadline you select. The APK is never launched, and
no emulator is required.

## What this does and doesn't prove

Manifest entries and decompiled text are static observations, not execution
or policy enforcement. Incoming references are recovered static
relationships — reflection, dynamic loading, and unresolved calls remain
unknown. Preserve artifact digests, locations, diagnostics, and incomplete
coverage. For archive members rather than code analysis, use `open_binary`
and `inspect_artifact`.
