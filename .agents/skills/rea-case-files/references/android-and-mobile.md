# Case file — Android & mobile

> **Scope:** your own software, a CTF/crackme, or a target you're otherwise
> authorized to study — see [DISCLAIMER.md](../../../../DISCLAIMER.md).

APK structure, Java/Kotlin↔native boundaries, storage/crypto review, and
packed-app triage. `inspect_android_package` is the entry point for code and
references; `inspect_apk_signature` handles signing identity separately —
see
[references/android-applications.md](../../reverse-engineer-anything/references/android-applications.md)
in the main skill.

### APK audit

```text
Analyze <PATH TO APK> with `inspect_android_package`. List declared
permissions, exported components, analytics/tracking SDKs, domains found in
code, and local-storage mechanisms. Show the class, method, or file behind
each finding, and don't conclude a domain is contacted just because it
appears as a string — distinguish "present in code" from "observed in
traffic."
```

### Tracing a feature across the JNI boundary

```text
Trace <FEATURE> end to end in <APK>: the UI entry point (Activity/Fragment/
Composable), the Java/Kotlin call chain, any JNI boundary
(RegisterNatives / native library loads), the .so functions involved, and
the final effect (network call, file write, database row). Give class,
method, and offset evidence at every hop.
```

### Storage, crypto, and secrets review

```text
Determine how <APK> stores <data>: SharedPreferences, SQLite, files, or
keystore. Recover any embedded crypto — hardcoded keys/IVs, custom
obfuscation — with the class/method using it. State whether the data is
encrypted at rest and in transit, with evidence for each, and list concrete
risks rather than generic ones.
```

### Obfuscated or packed app triage

```text
<APK> appears obfuscated or packed. Identify the packer/protector from
string or class fingerprints and the native-lib loader stub, map what's
statically visible versus deferred to runtime, and propose the minimal safe
steps to reach the real dex/native code for analysis — document the path,
don't attempt to defeat protections you haven't scoped.
```

### Signing identity and lineage

```text
Inspect the signing of <APK/AAB> with `inspect_apk_signature`. Report which
schemes are present (v1/v2/v3/v3.1), each signer's certificate SHA-256
fingerprint, any v3 key-rotation lineage, and whether every signed digest
still matches the bytes. Be explicit that this is identity and integrity,
not a trust verdict about who the signer actually is.
```
