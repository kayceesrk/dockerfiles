FROM ubuntu:18.04
RUN apt-get -y update
RUN apt-get install -y curl git make gcc g++ autoconf automake \
			autotools-dev libmpc-dev libmpfr-dev libgmp-dev libusb-1.0-0-dev \
			gawk build-essential bison flex texinfo gperf libtool patchutils \
			bc zlib1g-dev device-tree-compiler pkg-config libexpat-dev \
			libglib2.0-dev libfdt-dev libpixman-1-dev zlib1g-dev git python vim

RUN mkdir -p /root/.ssh
ADD id_rsa /root/.ssh/id_rsa
RUN chmod 600 /root/.ssh/id_rsa
RUN echo "Host github.com\n\tStrictHostKeyChecking no\n" >> /root/.ssh/config
RUN touch /root/.ssh/known_hosts && \
		ssh-keyscan gitlab.com >> /root/.ssh/known_hosts

WORKDIR root
RUN git clone -j8 --depth=1 git@gitlab.com:kayceesrk/ocaml-riscv.git
WORKDIR ocaml-riscv
RUN git submodule update --jobs 8 --init --recursive

ENV RISCV /install
WORKDIR toolchain
RUN ./build.sh

WORKDIR ../ocaml-compiler
RUN ./configure -no-ocamldoc -no-debugger -prefix $RISCV && \
    make -j8 world.opt && make install
ENV PATH="$RISCV/bin:${PATH}"
RUN make clean && \
		./configure --target riscv64-unknown-linux-gnu -prefix $RISCV \
		-no-ocamldoc -no-debugger -target-bindir $RISCV/bin && \
		make -j4 world || /bin/true
RUN make -j4 opt
RUN cp $RISCV/bin/ocamlrun byterun
RUN make install

WORKDIR ..
RUN git clone --recursive https://github.com/riscv/riscv-qemu.git
WORKDIR riscv-qemu
RUN ./configure --prefix=$RISCV --target-list=riscv64-softmmu,riscv32-softmmu,riscv64-linux-user,riscv32-linux-user
RUN make -j8
RUN make install

WORKDIR ..
RUN apt install -y cmake unzip bubblewrap wget
RUN wget https://raw.githubusercontent.com/ocaml/opam/master/shell/install.sh
RUN echo | sh ./install.sh
RUN opam init

RUN cp prebuilt/example.o prebuilt/payload.o && mkdir build
WORKDIR build
RUN cmake .. && make
RUN qemu-system-riscv64 -machine spike_v1.10 -smp 1 -m 1G -serial stdio -kernel kernel
