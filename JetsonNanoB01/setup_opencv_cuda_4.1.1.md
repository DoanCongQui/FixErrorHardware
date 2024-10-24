Tạo 1 file `Opencv.sh`
```
#!/usr/bin/env bash
# Modified by [Your Name] for OpenCV 4.1.1 with Python 3.8 on Jetson Nano

set -e

# Change default constants here:
readonly PREFIX=/usr/local  # Installation directory (can be ~/.local for user install)
readonly DEFAULT_VERSION=4.1.1  # Default OpenCV version is set to 4.1.1
readonly CPUS=$(nproc)  # Determines the number of jobs based on CPU count

# Better board detection. If it has 6 or more CPUs, it probably has enough RAM too
if [[ $CPUS -gt 5 ]]; then
    JOBS=$CPUS
else
    JOBS=1  # Use 1 job for systems with limited RAM
fi

cleanup () {
    while true; do
        echo "Do you wish to remove temporary build files in /tmp/build_opencv? "
        if ! [[ "$1" -eq "--test-warning" ]]; then
            echo "(Doing so may make running tests on the build later impossible)"
        fi
        read -p "Y/N " yn
        case ${yn} in
            [Yy]* ) rm -rf /tmp/build_opencv; break;;
            [Nn]* ) exit ;;
            * ) echo "Please answer yes or no." ;;
        esac
    done
}

setup () {
    cd /tmp
    if [[ -d "build_opencv" ]]; then
        echo "It appears an existing build exists in /tmp/build_opencv"
        cleanup
    fi
    mkdir build_opencv
    cd build_opencv
}

git_source () {
    echo "Getting version '$1' of OpenCV"
    git clone --depth 1 --branch "$1" https://github.com/opencv/opencv.git
    git clone --depth 1 --branch "$1" https://github.com/opencv/opencv_contrib.git
}

install_dependencies () {
    echo "Installing build dependencies."
    sudo apt-get update
    sudo apt-get dist-upgrade -y --autoremove
    sudo apt-get install -y \
        build-essential \
        cmake \
        git \
        gfortran \
        libatlas-base-dev \
        libavcodec-dev \
        libavformat-dev \
        libavresample-dev \
        libcanberra-gtk3-module \
        libdc1394-22-dev \
        libeigen3-dev \
        libglew-dev \
        libgstreamer-plugins-base1.0-dev \
        libgstreamer-plugins-good1.0-dev \
        libgstreamer1.0-dev \
        libgtk-3-dev \
        libjpeg-dev \
        libjpeg8-dev \
        libjpeg-turbo8-dev \
        liblapack-dev \
        liblapacke-dev \
        libopenblas-dev \
        libpng-dev \
        libpostproc-dev \
        libswscale-dev \
        libtbb-dev \
        libtbb2 \
        libtesseract-dev \
        libtiff-dev \
        libv4l-dev \
        libxine2-dev \
        libxvidcore-dev \
        libx264-dev \
        pkg-config \
        python3-dev \
        python3-numpy \
        python3-matplotlib \
        v4l-utils \
        zlib1g-dev
}

configure () {
    local CMAKEFLAGS="
        -D BUILD_EXAMPLES=OFF
        -D BUILD_opencv_python3=ON
        -D CMAKE_BUILD_TYPE=RELEASE
        -D CMAKE_INSTALL_PREFIX=${PREFIX}
        -D CUDA_ARCH_BIN=5.3
        -D CUDA_FAST_MATH=ON
        -D CUDNN_VERSION='8.0'
        -D EIGEN_INCLUDE_PATH=/usr/include/eigen3 
        -D ENABLE_NEON=ON
        -D OPENCV_DNN_CUDA=ON
        -D OPENCV_ENABLE_NONFREE=ON
        -D OPENCV_EXTRA_MODULES_PATH=/tmp/build_opencv/opencv_contrib/modules
        -D OPENCV_GENERATE_PKGCONFIG=ON
        -D WITH_CUBLAS=ON
        -D WITH_CUDA=ON
        -D WITH_CUDNN=ON
        -D WITH_GSTREAMER=ON
        -D WITH_LIBV4L=ON
        -D WITH_OPENGL=ON
        -D PYTHON3_EXECUTABLE=$(which python3)
        -D PYTHON3_INCLUDE_DIR=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())")
        -D PYTHON3_LIBRARY=$(python3 -c "import distutils.sysconfig as sysconfig; print(sysconfig.get_config_var('LIBDIR'))")"

    if [[ "$1" != "test" ]]; then
        CMAKEFLAGS="
        ${CMAKEFLAGS}
        -D BUILD_PERF_TESTS=OFF
        -D BUILD_TESTS=OFF"
    fi

    echo "cmake flags: ${CMAKEFLAGS}"

    cd opencv
    mkdir build
    cd build
    cmake ${CMAKEFLAGS} .. 2>&1 | tee -a configure.log
}

main () {

    local VER=${DEFAULT_VERSION}

    # Parse arguments
    if [[ "$#" -gt 0 ]]; then
        VER="$1"  # Override the version
    fi

    if [[ "$#" -gt 1 ]] && [[ "$2" == "test" ]]; then
        DO_TEST=1
    fi

    # Prepare for the build:
    setup
    install_dependencies
    git_source ${VER}

    if [[ ${DO_TEST} ]]; then
        configure test
    else
        configure
    fi

    # Start the build
    make -j${JOBS} 2>&1 | tee -a build.log

    if [[ ${DO_TEST} ]]; then
        make test 2>&1 | tee -a test.log
    fi

    # Avoid using sudo make install (and root owned files in ~) if $PREFIX is writable
    if [[ -w ${PREFIX} ]]; then
        make install 2>&1 | tee -a install.log
    else
        sudo make install 2>&1 | tee -a install.log
    fi

    cleanup --test-warning
}

main "$@"
```
Cấp quyên cho file thực thi 
```
sudo chmoad +x Opencv.sh
```
Chạy file `./Opencv.sh`
* Lưu ý: Thời gian build của Jetson Nano Kit khá lâu khoảng 5-6h