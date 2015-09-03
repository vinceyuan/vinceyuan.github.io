---
layout:     post
title:      Wrong info from proc_pidinfo
category:   blog
description: proc_pidinfo is a very useful function to get a Mac OS X process's info such as the size of resident memory, consumed CPU time. However, sometimes, the returned info is not correct.
---

proc\_pidinfo is a very useful function to get a Mac OS X process's info such as the size of resident memory, consumed CPU time.

However, sometimes, the returned info is not correct. For example, a little process's resident memory becomes 4GB. Apple does not provide any document about this function, and I did not see useful comments in the header files.

I am lucky enough to see code snippet in the Apple open source file: http://www.opensource.apple.com/source/lsof/lsof-28/lsof/dialects/darwin/libproc/dproc.c. When we call proc_pidinfo, we must check the returned value. If the returned value is not identical to the size of output data, the output data is wrong.

```
      nb = proc_pidinfo(pid, PROC_PIDTASKALLINFO, 0, &ti, sizeof(ti));
      if (nb <= 0) {
          if (errno == ESRCH)
              continue;
          if (!Fwarn) {
              (void) fprintf(stderr, "%s: PID %d information error: %s\n",
                  Pn, pid, strerror(errno));
          }
          continue;
      } else if (nb < sizeof(ti)) {
          (void) fprintf(stderr,
              "%s: PID %d: proc_pidinfo(PROC_PIDTASKALLINFO);\n",
              Pn, pid);
          (void) fprintf(stderr,
              "      too few bytes; expected %ld, got %d\n",
              sizeof(ti), nb);
          Exit(1);
      }
```

 checked the processes with correct info. They are all my processes (not system processes). Seems like my app runs in user mode and it does not have privileges to get info of system processes.
