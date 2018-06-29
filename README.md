# ownCloud Desktop Client 
## Build for Windows

````
git clone https://github.com/ITUBIDB/kovan-desktop-client.git
cd kovan-desktop-client/
git submodule update
cd ..
docker cp compile_client.sh client_build:/root/
docker cp kovan-desktop-client/ client_build:/root/
cd /root/
bash compile_client.sh kovan-desktop-client
osslsigncode -pkcs12 kovan.pfx -h sha256   -pass *******  -n "Kovan"  -i "http://bidb.itu.edu.tr" -in Kovan-2.4.1.0-setup.exe -out Kovan-2.4.1.0-setup-signed.exe
````

## build for MacOS
````
#!/bin/bash
export PATH=/Users/sistemdestek/Qt5.6.2/5.6/clang_64/bin/:$PATH
export OPENSSL_ROOT_DIR=$(brew --prefix openssl)

# Cleanup
cd ~
sudo rm -rf build-mac
sudo rm -rf kovan-desktop-client
sudo rm -rf install
sudo rm -rf qtkeychain
git clone --recursive https://github.com/itubidb/kovan-desktop-client.git
cd kovan-desktop-client
git submodule update --recursive

# Build qtkeychain
cd ~/kovan-desktop-client/src/3rdparty/
git clone https://github.com/frankosterfeld/qtkeychain.git
cd qtkeychain
git checkout v0.8.0
cmake -DCMAKE_OSX_SYSROOT="/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.13.sdk" -DCMAKE_OSX_DEPLOYMENT_TARGET=10.9 -DCMAKE_INSTALL_PREFIX=/Users/sistemdestek/install -DCMAKE_PREFIX_PATH=/Users/sistemdestek/Qt5.6.2/5.6/clang_64/ .
sudo make -j1 install

# Build the client
cd ~
cp dsa_pub.pem kovan-desktop-client/admin/osx/sparkle/
rm -rf build-mac
mkdir build-mac
cd build-mac
cmake -DCMAKE_OSX_SYSROOT="/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.13.sdk" -DCMAKE_OSX_DEPLOYMENT_TARGET=10.9 -DCMAKE_INSTALL_PREFIX=/Users/sistemdestek/install -DCMAKE_PREFIX_PATH=/Users/sistemdestek/Qt5.6.2/5.6/clang_64/ -D SPARKLE_INCLUDE_DIR=/Users/sistemdestek/Library/Frameworks/Sparkle.framework/ -D SPARKLE_LIBRARY=/Users/sistemdestek/Library/Frameworks/Sparkle.framework/  -DWITH_CRASHREPORTER=ON -DNO_SHIBBOLETH=1 -DMIRALL_VERSION_BUILD=1 ../kovan-desktop-client
make -j2
sudo make -j1 install

# The magic string here is SHA1 hash of your Developer ID Installer certificate
sudo ~/build-mac/admin/osx/create_mac.sh ../install/ . "Developer ID Installer: xxx"

# Generate a sparkle signature for the tbz
openssl dgst -sha1 -binary < ~/install/*.tbz | openssl dgst -dss1 -sign ~/dsa_priv.pem | openssl enc -base64 > ~/sig.txt
sudo mv ~/sig.txt ~/install/signature.txt
