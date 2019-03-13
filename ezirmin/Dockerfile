From ocaml/opam2
RUN sudo apt update
RUN sudo apt install -y m4 perl libgmp-dev pkg-config zlib1g-dev
RUN opam remote add upstream https://opam.ocaml.org && \
    opam update && opam upgrade
RUN opam switch 4.05
WORKDIR "$HOME"
RUN git clone https://github.com/kayceesrk/ezirmin/
WORKDIR ezirmin
RUN opam pin add -k path -n ezirmin .
RUN opam install irmin-unix.0.12.0 ezirmin
RUN opam install utop
RUN sudo apt install -y tmux locales
RUN echo "LC_ALL=en_US.UTF-8" | sudo tee --append /etc/environment && \
    echo "en_US.UTF-8 UTF-8"  | sudo tee --append /etc/locale.gen && \
    echo "LANG=en_US.UTF-8"   | sudo tee --append /etc/locale.conf && \
    sudo locale-gen en_US.UTF-8
