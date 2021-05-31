minikube on wsl2 windows

follow link

- [WSL+Docker: Kubernetes on the Windows Desktop | Kubernetes](https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/)
- [/usr/sbin/daemonize: No such file or directory : bashonubuntuonwindows (reddit.com)](https://www.reddit.com/r/bashonubuntuonwindows/comments/gc8lle/usrsbindaemonize_no_such_file_or_directory/)
- [Running Snaps on WSL2 (Insiders only for now) - snapd - snapcraft.io](https://forum.snapcraft.io/t/running-snaps-on-wsl2-insiders-only-for-now/13033)
- 

use this

```bash
https://github.com/DamionGans/ubuntu-wsl2-systemd-script/blob/master/README.md
```



run this

```bash
minikube start --driver=docker
```



The link:

[WSL+Docker: Kubernetes on the Windows Desktop | Kubernetes](https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/)

only works for kind but not too good for minikube - need to use the above two links

```bash
k8suser@DESKTOP-EN4NK5U:~$ kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
Name:         admin-user-token-r6qqw
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: fe2b57d5-d9ec-4f60-8eb5-83cbc395185a

Type:  kubernetes.io/service-account-token

Data
====
namespace:  20 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Im40YmZJc2h3RHJSdHlJcmJEckVTcUpyVEdUTUlJRGZ5emtyUUMxSVRuQ1EifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLXI2cXF3Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJmZTJiNTdkNS1kOWVjLTRmNjAtOGViNS04M2NiYzM5NTE4NWEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.EyDPmE1A6g8xBLQlXvdtbXwIwi65LbTS2ulNfqMyDijXcOt039Z2Zlrz2m46J1v4-bpqpNWXujroEbk-w4wNMCV_lz-LNVv0VBcryw4WuB02Ob2DZS8cpa5t0tgxLeVDcYI4Rwg5HDkPI3IBr1-BBwTUYeCkVyu0dDyNgG3t2zd3muaK8dBfsuPOZBuliiG8pP_d2trUlLGYx8E36aBmUu6pX5bsyVUny4H7-7ZppSfTlJXGd6EAl5Qb60GSfDTzfGGcJQZ4frwD0IOo8hYxEKCxaJB0fRYp_3M6qw-a7FHT12aXI02iNsAX4g_ERB1a_8tLiEnyASgCjJ40T8MCvQ
ca.crt:     1025 bytes
k8suser@DESKTOP-EN4NK5U:~$
```



