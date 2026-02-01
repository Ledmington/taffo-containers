FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        wget \
        gcc \
        g++ \
        cmake \
        ninja-build && \
    wget https://github.com/llvm/llvm-project/archive/refs/tags/llvmorg-18.1.8.tar.gz && \
    tar xvf llvmorg-18.1.8.tar.gz && \
    cd llvmorg-18.1.8 && \
    cmake -S llvm -B build \
        -GNinja \
        -DCMAKE_INSTALL_PREFIX=/opt/llvm-18-d \
        -DCMAKE_BUILD_TYPE=Debug \
        -DLLVM_ENABLE_PROJECTS='clang;openmp' \
        -DLLVM_ENABLE_RUNTIMES='libcxx;libcxxabi' \
        -DLLVM_ENABLE_LIBCXX=ON \
        -DLLVM_INSTALL_UTILS=ON \
        -DLLVM_INCLUDE_EXAMPLES=OFF \
        -DLLVM_BUILD_LLVM_DYLIB=ON \
        -DLLVM_LINK_LLVM_DYLIB=ON \
        -DLLVM_OPTIMIZED_TABLEGEN=ON \
        -DLLVM_USE_LINKER=gold \
        -DLLVM_PARALLEL_LINK_JOBS=1 && \
    cmake --build build -j $(nproc) -- install
