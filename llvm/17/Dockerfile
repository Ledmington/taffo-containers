FROM ubuntu:24.04 AS builder

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        wget \
        git \
        gcc \
        g++ \
        cmake \
        ninja-build \
        python3 \
        ca-certificates && \
    # Setup OR-Tools
    cd /tmp && \
    wget -q https://github.com/google/or-tools/releases/download/v9.15/or-tools_amd64_ubuntu-24.04_cpp_v9.15.6755.tar.gz && \
    mkdir -p /opt/or-tools && \
    tar -xzf or-tools_amd64_ubuntu-24.04_cpp_v9.15.6755.tar.gz -C /opt/or-tools --strip-components=1 && \
    # Setup LLVM
    cd /tmp && \
    wget -q https://github.com/llvm/llvm-project/archive/refs/tags/llvmorg-17.0.6.tar.gz && \
    echo "81494d32e6f12ea6f73d6d25424dbd2364646011bb8f7e345ca870750aa27de1 /tmp/llvmorg-17.0.6.tar.gz" >> /tmp/llvm_hash.txt && \
    sha256sum --check /tmp/llvm_hash.txt && \
    tar xzf /tmp/llvmorg-17.0.6.tar.gz && \
    cd /tmp/llvm-project-llvmorg-17.0.6 && \
    cmake -S llvm -B build \
        -G Ninja \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/opt/llvm-17 \
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
        -DLIBOMP_USE_STDCPPLIB=ON && \
    cmake --build build -j $(nproc) -- all && \
    cmake --build build -- install && \
    # Cleanup
    cd ${HOME} && \
    rm -rf /tmp/* && \
    apt-get remove -y wget && \
    apt-get autoremove -y && \
    apt-get clean -y

FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        ca-certificates \
        python3 \
        cmake \
        ninja-build && \
    apt-get autoremove -y && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/or-tools /opt/or-tools
COPY --from=builder /opt/llvm-17 /opt/llvm-17
