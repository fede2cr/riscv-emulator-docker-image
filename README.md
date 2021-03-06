# Qemu RISC-V Debian Compilation Cluster

With one command you'll get a nice riscv-Debian with Icrecc (or Icecream) as compilation cluster.

## How to use

### Quick start:

```bash
# 1. Download the prebuilt image from docker hub, with Icecc ports and ssh on port 2222
docker run -d --publish 127.0.0.1:2222:2222/tcp --publish 0.0.0.0:8765:8765/tcp --publish 0.0.0.0:10245:10245/tcp fede2/qemu-riscv-debian-icecc
# 2. SSH directly into the QEMU RISC-V guest. (Might take a few minutes for guest to start)
ssh root@localhost -p 2222
# 3. Inside the cluster, if needed, register the IP of your icecc scheduler
nano /etc/icecc/icecc.conf && /etc/init.d/iceccd restart
```

**Important**: Start the service icecc-scheduler on **one** of the nodes, and configure the rest to use it, with: ```/etc/init.d/icecc-scheduler start```.

## How it was built

- Was built based on the steps in my blog post <https://blog.davidburela.com/2020/11/15/emulating-risc-v-debian-on-wsl2/>
- Utilising [Giovanni Mascellani’s prebaked Debian images](https://www.giovannimascellani.eu/dqib-debian-quick-image-baker.html)
