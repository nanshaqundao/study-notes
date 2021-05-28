# Kubernetes

## Introduction

### Container vs Virtual Machine

- Container (docker) is just a ***special*** process

### Namespace and Control Group

- namespace is used to isolate resources for container

  - sample

    ```bash
    nash@DESKTOP-EN4NK5U:~$ ls -l /proc/$$/ns
    total 0
    lrwxrwxrwx 1 nash nash 0 May 28 11:56 cgroup -> 'cgroup:[4026531835]'
    lrwxrwxrwx 1 nash nash 0 May 28 11:56 ipc -> 'ipc:[4026532187]'
    lrwxrwxrwx 1 nash nash 0 May 28 11:56 mnt -> 'mnt:[4026532185]'
    lrwxrwxrwx 1 nash nash 0 May 28 11:56 net -> 'net:[4026531992]'
    lrwxrwxrwx 1 nash nash 0 May 28 11:56 pid -> 'pid:[4026532188]'
    lrwxrwxrwx 1 nash nash 0 May 28 11:56 pid_for_children -> 'pid:[4026532188]'
    lrwxrwxrwx 1 nash nash 0 May 28 11:56 user -> 'user:[4026531837]'
    lrwxrwxrwx 1 nash nash 0 May 28 11:56 uts -> 'uts:[4026532186]'
    ```

- control group (Cgroups) to limit the quota of resources for container

  - sample

  - ```bash
    nash@DESKTOP-EN4NK5U:/sys/fs/cgroup/cpu/container$ mount -t cgroup
    cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
    cgroup on /sys/fs/cgroup/cpu type cgroup (rw,nosuid,nodev,noexec,relatime,cpu)
    cgroup on /sys/fs/cgroup/cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct)
    cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
    cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
    cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
    cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
    cgroup on /sys/fs/cgroup/net_cls type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls)
    cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
    cgroup on /sys/fs/cgroup/net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_prio)
    cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
    cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
    cgroup on /sys/fs/cgroup/rdma type cgroup (rw,nosuid,nodev,noexec,relatime,rdma)
    ```

  - ```bash
    nash@DESKTOP-EN4NK5U:~$ docker run -it --cpu-period=100000 --cpu-quota=20000 8e85dd5c3255 /bin/bash
    root@DESKTOP-EN4NK5U:/sys/fs/cgroup/cpu/docker/3a8414301048b1701f7e00af1f56f88ed533c0eee9e39e33bf2bb86a3e6becac# cat cpu.cfs_period_us
    100000
    root@DESKTOP-EN4NK5U:/sys/fs/cgroup/cpu/docker/3a8414301048b1701f7e00af1f56f88ed533c0eee9e39e33bf2bb86a3e6becac# cat cpu.cfs_quota_us
    20000
    
    ```

