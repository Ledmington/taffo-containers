FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y
RUN apt-get upgrade -y
RUN apt-get install -y --no-install-recommends \
        wget \
        git \
        gcc \
        g++ \
        cmake \
        ninja-build \
        python3 \
        ca-certificates
RUN cd /tmp && \
    wget -q https://github.com/llvm/llvm-project/archive/refs/tags/llvmorg-18.1.8.tar.gz
RUN echo "09c08693a9afd6236f27a2ebae62cda656eba19021ef3f94d59e931d662d4856 /tmp/llvmorg-18.1.8.tar.gz" >> /tmp/llvm_hash.txt
RUN sha256sum --check /tmp/llvm_hash.txt
RUN tar xf /tmp/llvmorg-18.1.8.tar.gz
RUN cd /tmp/llvm-project-llvmorg-18.1.8 && \
    cmake -S llvm -B build \
        -G Ninja \
        -DCMAKE_BUILD_TYPE=Debug \
        -DCMAKE_INSTALL_PREFIX=/opt/llvm-18-d \
        -DLLVM_ENABLE_PROJECTS='clang;openmp' \
        -DLLVM_ENABLE_RUNTIMES='libcxx;libcxxabi;libunwind' \
        -DLLVM_TARGETS_TO_BUILD='X86' \
        -DLLVM_ENABLE_LIBCXX=ON \
        -DLLVM_INSTALL_UTILS=ON \
        -DLLVM_INCLUDE_EXAMPLES=OFF \
        -DLLVM_BUILD_LLVM_DYLIB=ON \
        -DLLVM_LINK_LLVM_DYLIB=ON \
        -DLLVM_OPTIMIZED_TABLEGEN=ON \
        -DLLVM_USE_LINKER=gold \
        -DLLVM_PARALLEL_LINK_JOBS=1 \
        -DCMAKE_CXX_FLAGS="-include cstdint -U_GLIBCXX_ASSERTIONS" \
        -DCMAKE_EXE_LINKER_FLAGS="-lstdc++" \
        -DLIBOMP_USE_STDCPPLIB=ON
RUN cmake --build build -j $(nproc) -- all
RUN cmake --build build -- install
RUN cd ${HOME} && \
    rm -rf /tmp/* && \
    apt-get remove -y wget && \
    apt-get autoremove -y && \
    apt-get clean -y
