# Create a virtual environment with all tools installed
# ref: https://hub.docker.com/_/centos
FROM centos:latest AS env
LABEL maintainer="mizux.dev@gmail.com"
# Install system build dependencies
ENV PATH=/usr/local/bin:$PATH
RUN dnf -y update \
&& dnf -y install git wget openssl-devel \
&& dnf -y groupinstall "Development Tools" \
&& dnf clean all \
&& rm -rf /var/cache/dnf
# Install CMake 3.17.2
RUN wget "https://cmake.org/files/v3.17/cmake-3.17.2-Linux-x86_64.sh" \
&& chmod a+x cmake-3.17.2-Linux-x86_64.sh \
&& ./cmake-3.17.2-Linux-x86_64.sh --prefix=/usr/local/ --skip-license \
&& rm cmake-3.17.2-Linux-x86_64.sh
CMD [ "/bin/sh" ]

FROM env AS devel
WORKDIR /home/project
COPY . .

FROM devel AS build
RUN cmake -version
RUN cmake -H. -Bbuild
RUN cmake --build build --target all
RUN cmake --build build --target install

FROM build AS test
RUN cmake --build build --target test

FROM env AS install_env
COPY --from=build /usr/local /usr/local/

FROM install_env AS install_devel
WORKDIR /home/sample
COPY ci/sample .

FROM install_devel AS install_build
RUN cmake -S. -Bbuild
RUN cmake --build build --target all -v

FROM install_build AS install_test
RUN cmake --build build --target test
