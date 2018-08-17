# multicore

```
> Sys.getpid()
[1] 1505
> parallel::m
parallel::mcMap             parallel::mclapply          parallel::mcaffinity        parallel::mcmapply          parallel::mccollect         
parallel::makePSOCKcluster  parallel::mcparallel        parallel::makeCluster       parallel::mc.reset.stream   parallel::makeForkCluster   
> parallel::mcparallel(Sys.sleep(100))
$pid
[1] 2758

$fd
[1] 3 6

attr(,"class")
[1] "parallelJob"  "childProcess" "process"     

> multicore::mcparallel(Sys.sleep(100))                                                                                                                                                                             
 parallelJob: processID=2830

liqg@cycad:~/soft/R/R-3.3.2/library$ ps aux |grep liqg |grep  R                                                                 
liqg      1505  1.2  0.0 2232836 38304 pts/13  S+   18:26   0:06 /usr/lib/R/bin/exec/R
liqg      2758  0.0  0.0 2230104 32272 pts/13  S+   18:34   0:00 /usr/lib/R/bin/exec/R
liqg      2830  0.0  0.0 2232836 32568 pts/13  S+   18:35   0:00 /usr/lib/R/bin/exec/R

liqg@cycad:~/soft/R/R-3.3.2/library$ kill -9 1505
liqg@cycad:~/soft/R/R-3.3.2/library$ ps aux |grep liqg |grep  R
liqg      2758  0.0  0.0 2230104 32272 pts/13  S    18:34   0:00 /usr/lib/R/bin/exec/R
```

## Hack R package: parallel 
* download source code of R (R-3.3.2) , decompress and change dir
```
tar xf R-3.3.2.tar.gz & cd xf R-3.3.2
```
* change src/library/parallel/src/fork.c by adding a head file and set `prctl(PR_SET_PDEATHSIG, SIGTERM)`:
```
#include <sys/prctl.h>

    if (pid == 0) { /* child */ # line 275 of origin file
        int r_prctl = prctl(PR_SET_PDEATHSIG, SIGTERM); // adding line 
        if (r_prctl == -1) { perror(0); exit(1); } // adding line 

```
* Comple R source code
```
./configure && make
```
* Copy to R library
```
sudo cp -r parallel /usr/lib/R/library
```

