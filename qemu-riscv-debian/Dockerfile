FROM debian:sid as qemu-build

ENV DEBIAN_FRONTEND noninteractive
# Install all needed packages
RUN apt-get update && \
apt-get install -y --no-install-recommends ca-certificates git build-essential ninja-build libglib2.0-dev libpixman-1-dev checkinstall

# Download and configure QEMU
WORKDIR "/root"
RUN git clone https://github.com/qemu/qemu
#RUN cd /root/qemu && git submodule init && git submodule update 
RUN mkdir /root/qemu/build  && cd /root/qemu/build && \
# build and install
../configure --target-list=riscv64-softmmu 
RUN cd /root/qemu/build && make -j6 
RUN cd /root/qemu/build && make install 
RUN cd /root/qemu/build && checkinstall -y --pkgversion 1 --pkgname qemu-git

FROM debian:sid
EXPOSE 2222
EXPOSE 10245
EXPOSE 8765

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -qy wget xz-utils u-boot-qemu libpixman-1-0 libglib2.0-0

COPY --from=qemu-build /root/qemu/build/qemu-git_1-1_amd64.deb /root

RUN dpkg -i /root/*.deb
RUN rm -v /root/*.deb

# clean up the temp files
RUN apt-get autoremove -y && \
apt-get clean && \
rm -rf /var/lib/apt/lists/*

# Get RISC-V Debian image
WORKDIR "/root"
#RUN wget https://gateway.pinata.cloud/ipfs/Qmdi7cGXxyiipHzaZ5vBVzSWqFUb6U3JtH8Ti4Dwhb356i -O artifacts.tar.xz && \
RUN wget http://10.42.30.5/vms/artifacts.tar.xz -O artifacts.tar.xz && \
tar xJvf artifacts.tar.xz && rm artifacts.tar.xz

CMD qemu-system-riscv64 -smp 6 -m 6G -cpu rv64 -nographic -machine virt -kernel /usr/lib/u-boot/qemu-riscv64_smode/uboot.elf -device virtio-blk-device,drive=hd -drive file=artifacts/image.qcow2,if=none,id=hd -device virtio-net-device,netdev=net -netdev user,id=net,hostfwd=tcp::2222-:22,hostfwd=tcp::10245-:10245,hostfwd=tcp::8765-:8765 -object rng-random,filename=/dev/urandom,id=rng -device virtio-rng-device,rng=rng -append "root=LABEL=rootfs console=ttyS0"
