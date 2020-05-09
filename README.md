# Dev Machine Setup on Ubuntu 20.04

This should probably be a script or something but I thought I'd document the setup of my fresh dev machine

## Thrift 0.9.1 Compilation

```
sudo apt install automake bison flex g++ git libboost-all-dev libevent-dev libssl-dev libtool make pkg-config libcrypto++-dev curl
wget https://archive.apache.org/dist/thrift/0.9.1/thrift-0.9.1.tar.gz
tar xvf thrift-0.9.1.tar.gz
cd thrift-0.9.1
wget http://archive.ubuntu.com/ubuntu/pool/universe/t/thrift-compiler/thrift-compiler_0.9.1-2.debian.tar.xz
tar xvf thrift-compiler_0.9.1-2.debian.tar.xz
cd compiler
patch -p1 < ../debian/patches/thrift-2247-binary-as-string-keys.patch
patch -p1 < ../debian/patches/gcc6.patch
cd ../
./configure --prefix /opt/thrift-0.9.1 --without-python --without-cpp --without-nodejs -without-py3
make
sudo make install
sudo ln -s /opt/thrift-0.9.1/bin/thrift /usr/local/bin/
thrift --version
```
