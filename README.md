# nagios-check-gluster
Nagios Check for GlusterFS v3.7

## Usage
```None
[root@storage ~]# /usr/lib64/nagios/plugins/check_gluster -h
Usage: check_gluster [options]
    -c, --checks bitrot,heal,nfs     Checks to run. Default is bitrot,heal,nfs
    -s, --state-file /path/to/file   State storage file. Default is /var/run/check_gluster_state.yaml
    -t, --ttl seconds                State storage TTL. Default is 1200
[root@storage ~]# 
```
## Internals
It requires root privileges. By default this nagios check:
* Verifies that glusterd is running
* Runs `peer status` and verifies that peers are connected
* Gets list of volumes and for each volume
  * Runs `volume info` and verifies that volume is started
  * Runs `volume status` and by default verifies that
    * Bricks processes are running
    * Self-heal Daemon is running if `heal` check is enabled
    * NFS Server processes is running if `nfs` check is enabled
    * Bitrot Daemon and Scrubber Daemon are running if `bitrot` check is enabled
    
    This behavior can be adjusted using `--checks bitrot,heal,nfs` command line option
  * Runs `volume heal info` if `heal` check is enabled and reports alert for entries older than `--ttl`. State cache can be disabled by setting `--ttl 0`
  * Runs `volume bitrot scrub status` if `bitrot` check is enabled and there are any errors.
