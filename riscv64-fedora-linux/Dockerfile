FROM ubuntu:18.04
RUN apt-get -y update
RUN apt-get install -y curl git make gcc g++ git vim

WORKDIR /root
RUN git clone --recursive https://github.com/riscv/riscv-qemu.git
WORKDIR riscv-qemu
RUN ./configure --target-list=riscv64-softmmu,riscv64-linux-user
RUN make -j8
RUN make install

WORKDIR ..
RUN wget http://fedora-riscv.tranquillity.se/kojifiles/work/tasks/7607/167607/Fedora-Developer-Rawhide-20190401.n.0-sda.raw.xz
RUN unxz Fedora-Developer-Rawhide-xxxx.n.0-sda.raw.xz
RUN wget https://artifact.ci.freebsd.org/snapshot/head/latest/riscv/riscv64/bbl.xz
RUN unxz bbl.xz
RUN qemu-system-riscv64 \
    -nographic \
    -machine virt \
    -smp 4 \
    -m 2G \
    -kernel bbl \
    -object rng-random,filename=/dev/urandom,id=rng0 \
    -device virtio-rng-device,rng=rng0 \
    -append "console=ttyS0 ro root=/dev/vda" \
    -device virtio-blk-device,drive=hd0 \
    -drive file=stage4-disk.img,format=raw,id=hd0 \
    -device virtio-net-device,netdev=usernet \
    -netdev user,id=usernet,hostfwd=tcp::10000-:22
