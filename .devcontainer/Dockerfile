FROM ubuntu:25.04

ARG TZ=Europe/Zurich

# install a few tools for more convenient developing
RUN  apt-get update; \
    LANG="en_US.UTF-8" LANGUAGE="en_US:en" LC_ALL="en_US.UTF-8" DEBIAN_FRONTEND=noninteractive apt-get -y install --fix-missing \
    # FIXME: NO! gcc-14 g++-14 libstdc++-14-dev gdb \
    curl bash-completion vim tree less \
    bash build-essential git wget lcov sudo libssl-dev \
    graphviz python3-pip python-is-python3 doxygen plantuml pipx \
    clang-20 libc++-20-dev clang-tools-20 cppcheck zip afl++ ccache \
    # qemu qemu-system-arm gcc-arm-linux-gnueabihf g++-arm-linux-gnueabihf libc6-dev-armhf-cross qemu-user \
    # XXX QT runtime prerequisites \
    # XXX libgl1-mesa-dev libglu1-mesa-dev '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev \
    # XXX libxkbcommon-dev libxkbcommon-x11-dev \
    rpm

RUN pip install --break-system-packages clang-tidy clang-format gcovr pytest pytest-depends pylint conan gersemi black pre-commit pip-tools

RUN pipx install pre-commit && pipx install cmake && pipx install ninja

RUN groupadd -r builder && useradd --create-home --gid builder --groups dialout,plugdev,audio,tty --shell /usr/bin/bash builder &&  mkdir -p /etc/sudoers.d && echo 'builder ALL=NOPASSWD: ALL' > /etc/sudoers.d/builder

# Download version 3.31 of CMake
# RUN wget https://github.com/Kitware/CMake/releases/download/v3.31.8/cmake-3.31.8-linux-x86_64.sh
# RUN head cmake-3.31.8-linux-x86_64.sh
# RUN chmod +x ./cmake-3.31.3-linux-x86_64.sh && ./cmake-3.31.8-linux-x86_64.sh --skip-license --prefix=/usr/local

USER builder
WORKDIR /home/builder

# Download and install vcpkg
RUN cd ~ && curl -OL https://github.com/microsoft/vcpkg/archive/refs/tags/2025.10.17.tar.gz
RUN mkdir -p ~/.local/opt/vcpkg && mkdir -p ~/.local/bin/ && tar -xvf ~/2025.10.17.tar.gz --strip-components=1 -C ~/.local/opt/vcpkg && rm ~/2025.10.17.tar.gz
RUN ~/.local/opt/vcpkg/bootstrap-vcpkg.sh && ln -s ~/.local/opt/vcpkg/vcpkg ~/.local/bin/vcpkg

# install git shell extension
RUN curl -L https://raw.github.com/git/git/master/contrib/completion/git-prompt.sh > ~/.bash_git && echo "source ~/.bash_git" >> ~/.bashrc
RUN sed -Ei 's/(PS1=.*)(\\\[\\033\[00m\\\]\\\$.*)/\1\\[\\033[01;33m\\]$(__git_ps1)\2/p' ~/.bashrc

# if CC is null, set it to 'gcc-15' (or leave as is otherwise).
ENV CC=clang-20
ENV CXX=clang++-20
# FIXME: NO! ENV CC=${CC:-"gcc-15"}
# FIXME: NO! ENV CXX=${CXX:-"g++-15"}
