# Thread dumps and deadlocks (Tier 1)

```bash
jcmd <pid> Thread.print              # preferred; includes deadlock report
jcmd <pid> Thread.print -l           # also lists ownable synchronizers/locks
jstack <pid>                         # equivalent older tool
kill -3 <pid>                        # prints to the JVM's stdout (no attach needed)
```

Take **3 dumps, 5-10 seconds apart**, to see what changes. A single dump is a snapshot, not a trend. Thread dumps include stack frames and thread names, which may contain request data; do not share publicly.

## High CPU: map hot threads

```bash
top -H -p <pid>                      # find hot native thread ids (decimal)
printf '%x\n' <tid>                  # convert to hex, match "nid=0x..." in the dump
```

Check whether the hot thread is application code, GC threads (`GC Thread`, `G1 Conc`), or JIT compiler threads (`C2 CompilerThread`).

## Deadlock

`Thread.print` ends with "Found one Java-level deadlock" and lists the lock cycle. Otherwise look for many threads `BLOCKED` on the same monitor (`waiting to lock <0x...>`) whose owner is itself waiting. Thread starvation shows pools with all threads `WAITING`/`TIMED_WAITING` in the same call (e.g., a connection pool `getConnection`, a blocking HTTP call) while the queue grows.

## Virtual threads (JDK 21+)

`Thread.print` does not list virtual threads. Use:

```bash
jcmd <pid> Thread.dump_to_file -format=json threads.json
jcmd <pid> Thread.dump_to_file threads.txt
```

Pinned virtual threads (blocked inside `synchronized` on older JDKs, or native frames) can be investigated with the JFR event `jdk.VirtualThreadPinned` (see [jfr](jfr.md)); `-Djdk.tracePinnedThreads=full` was a debug option on JDK 21-23 and is not available on newer releases, so verify it against your JDK.

## Reading tips

- States: `RUNNABLE` (on CPU or in native I/O), `BLOCKED` (monitor), `WAITING`/`TIMED_WAITING` (park, sleep, wait).
- `RUNNABLE` in `socketRead0` is usually waiting on the network, not using CPU.
- Group threads by identical top frames to find the dominating pattern.
