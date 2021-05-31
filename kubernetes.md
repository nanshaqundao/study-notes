













































































































































































































#  Kubernetes

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

- useful link

  ​	[coolshell - docker linux space 上](https://coolshell.cn/articles/17010.html)



### Filesystem in container

#### chroot to mount namespace

- create a .c file

  ```c
  #define _GNU_SOURCE
  #include <sys/mount.h>
  #include <sys/types.h>
  #include <sys/wait.h>
  #include <stdio.h>
  #include <sched.h>
  #include <signal.h>
  #include <unistd.h>
  #define STACK_SIZE (1024 * 1024)
  static char container_stack[STACK_SIZE];
  char* const container_args[] = {
    "/bin/bash",
    NULL
  };
  
  
  int container_main(void* arg)
  {
    printf("Container - inside the container!\n");
    // 如果你的机器的根目录的挂载类型是shared，那必须先重新挂载根目录
    mount("", "/", NULL, MS_PRIVATE, "");
    mount("none", "/tmp", "tmpfs", 0, "");
    execv(container_args[0], container_args);
    printf("Something's wrong!\n");
    return 1;
  }
  
  int main()
  {
    printf("Parent - start a container!\n");
    int container_pid = clone(container_main, container_stack+STACK_SIZE, CLONE_NEWNS | SIGCHLD , NULL);
    waitpid(container_pid, NULL, 0);
    printf("Parent - container stopped!\n");
    return 0;
  }
  ```

- compile and execute - now inside the container

  ```bash
  nash@DESKTOP-EN4NK5U:~/tool/kubernetes$ gcc -o ns ns.c
  nash@DESKTOP-EN4NK5U:~/tool/kubernetes$ sudo ./ns
  Parent - start a container!
  Container - inside the container!
  root@DESKTOP-EN4NK5U:/home/nash/tool/kubernetes#
  
  ```

- run ls /tmp - blank

  ```bash
  nash@DESKTOP-EN4NK5U:~/tool/kubernetes$ gcc -o ns ns.c
  nash@DESKTOP-EN4NK5U:~/tool/kubernetes$ sudo ./ns
  Parent - start a container!
  Container - inside the container!
  root@DESKTOP-EN4NK5U:/home/nash/tool/kubernetes#
  
  ```

- check how /tmp is mount - note the "none on /tmp type tmpfs (rw,relatime)"

  ```bash
  root@DESKTOP-EN4NK5U:/home/nash/tool/kubernetes# mount -l | grep tmpfs
  none on /dev type devtmpfs (rw,nosuid,relatime,size=13085936k,nr_inodes=3271484,mode=755)
  tmpfs on /sys/fs/cgroup type tmpfs (rw,nosuid,nodev,noexec,relatime,mode=755)
  none on /run type tmpfs (rw,nosuid,noexec,noatime,mode=755)
  none on /run/lock type tmpfs (rw,nosuid,nodev,noexec,noatime)
  none on /run/shm type tmpfs (rw,nosuid,nodev,noatime)
  none on /run/user type tmpfs (rw,nosuid,nodev,noexec,noatime,mode=755)
  tmpfs on /mnt/wsl type tmpfs (rw,relatime)
  none on /mnt/wsl/docker-desktop/shared-sockets/guest-services type tmpfs (rw,nosuid,noexec,noatime,mode=755)
  none on /mnt/wsl/docker-desktop/shared-sockets/host-services type tmpfs (rw,nosuid,noexec,noatime,mode=755)
  tmpfs on /mnt/wsl/docker-desktop-data/tarcache/entries/docker.tar/bdd0432aeda8095f15c9608e4ad374f5970c65729a34f212d5e967bec3416874/containers/services/docker/tmp type tmpfs (rw,relatime)
  none on /tmp type tmpfs (rw,relatime)
  root@DESKTOP-EN4NK5U:/home/nash/tool/kubernetes#
  ```

- A better way than manually alter these mount - to remount the whole root / before the container process starts

  ```bash
  
  $ mkdir -p $HOME/test
  $ T=$HOME/test
  $ mkdir -p $HOME/test/{bin,lib64,lib}
  $ mkdir -p $HOME/test/lib/x86_64-linux-gnu
  $ cd $T
  
  
  $ cp -v /bin/{bash,ls} $HOME/test/bin
  
  
  $ list="$(ldd /bin/ls | egrep -o '/lib.*\.[0-9]')"
  $ for i in $list; do cp -v "$i" "${T}${i}"; done
  
  $ list="$(ldd /bin/bash | egrep -o '/lib.*\.[0-9]')"
  $ for i in $list; do cp -v "$i" "${T}${i}"; done
  
  
  $ chroot $HOME/test /bin/bash
  
  root@DESKTOP-EN4NK5U:/home/nash/tool/kubernetes# chroot $HOME/test /bin/bash
  bash-5.0# ls /
  bin  lib  lib64
  bash-5.0#
  ```

- 实际上，Mount Namespace 正是基于对 chroot 的不断改良才被发明出来的，它也是 Linux 操作系统里的第一个 Namespace。而这个挂载在容器根目录上、用来为容器进程提供隔离后执行环境的文件系统，就是所谓的“容器镜像”。它还有一个更为专业的名字，叫作：rootfs（根文件系统）。

#### for docker user

Docker 项目来说，它最核心的原理实际上就是为待创建的用户进程：

- 启用 Linux Namespace 配置；
- 设置指定的 Cgroups 参数；
- 切换进程的根目录（Change Root）。

实际上，同一台机器上的所有容器，都共享宿主机操作系统的内核。









