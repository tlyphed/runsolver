# runsolver
runsolver by Olivier ROUSSEL


see: 
- http://www.cril.univ-artois.fr/~roussel/rech.php?lang=eng
- https://www.cril.univ-artois.fr/~roussel/runsolver/

Modified:
- Varfile contains retcode / signals


Note that runsolver has plenty of conceptual problems. Use cgroups if available on your system. Benchexec is a fairly 
good alternative there. However, setup is not easy and require sudo access. So there might still cases where you want
to use runsolver.  

Simple example (heavy memory load):
```bash 
#!/usr/bin/env bash
stress-ng --vm-bytes $(awk '/MemFree/{printf "%d\n", $2 * 0.9;}' < /proc/meminfo)k --vm-keep -m 1
```
The example results in a heavy process. runsolver scales back on sampling and it might take a couple
of additional seconds to detect problems. If your memory usage just peaks once then, high memory usage might be missed.


Simple example (don't mess with the children):
```bash 
#!/usr/bin/env bash
sleep 15
sleep 10
exit 20
```
Bash usually ignores a signal if children are running. Here the SIGTERM is send to the children first, bash itself
ignores it. Results in that after a timeout on sleep 15, sleep 10 will be startet. Even worse if it would happen during
the sleep 10, the exit code would be 20 instead of 143 as it should be by default when terminated. Conceptually, it does
not seem to  me very helpful to use different than standard unix behavior. Just let the process handle its children. 
If it does not respond, then kill the entire process hierarchy from the top. So that the ancestors do not run into
a wired behavior.