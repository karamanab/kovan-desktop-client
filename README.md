# Kovan Desktop Client

## Introduction

Kovan Desktop Client is a tool to synchronize files from İTÜ Kovan Server
with your computer.

## Download


### Source code

The Kovan Desktop Client is developed in Git. Since Git makes it easy to
fork and improve the source code and to adapt it to your need, many copies
can be found on the Internet, in particular on GitHub. However, the
authoritative repository maintained by the developers is located at
https://github.com/ITUBIDB/kovan-desktop-client.

## Building the source code

## MacOS Building steps (Tested with OS X Catalina)

### Building the client (After fresh OS install)
* Install Xcode from AppStore
* Install Xcode `xcode-select –-install`
* Install Brew `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"`
* Install Packages from http://s.sudre.free.fr/Software/Packages/about.html
* Clone repo `git clone https://github.com/ITUBIDB/kovan-desktop-client/`
* Add ownCloud repo `brew tap owncloud/owncloud`
* Install qt5 using `brew install qt5`
* Install ownCloud dependencies using `brew install $(brew deps owncloud-client)`
* Install qtkeychain `brew install qtkeychain`
* Install cmake `brew install cmake`
* Init submodules using `cd kovan-desktop-client && sudo git submodule init && sudo git submodule update`
* Create build folder `mkdir client-build && cd client-build`
* Configure cmake options `cmake -DCMAKE_PREFIX_PATH=/usr/local/Cellar/qt/5.15.0 -DCMAKE_INSTALL_PREFIX=~/kovan-desktop-client/build ../`
* Start build `make`

### Packaging to client
* Kovan.app will be under the folder ./client-build/bin or could be executed using `./Kovan` under ./Kovan.app/Contents/MacOS
* Run `./admin/osx/create_mac.sh ./bin/ .` to generate pkg, it will be under build folder that's created on cmake configuration step

## Windows Building steps (Tested with Windows 10 x64)

### Building the client (After fresh OS install)
* Install both Python 2 and Python 3 latest from python.org
* Install Visual Studio 2019 with "Desktop Development with C++"
* Open Powershell as Admin and change execution policy `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`
* Install KDE Craft with default settings. `iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/KDE/craft/master/setup/install_craft.ps1'))`
* Launch the KDE Craft Environment `C:\CraftRoot\craft\craftenv.ps1`
* Add kovan craft blueprint repository: `craft --add-blueprint-repository https://github.com/ITUBIDB/craft-blueprints-owncloud.git`
* Build the client: `craft --buildtype Release owncloud-client`

### Packaging to client
* Install nsis: `craft --buildtype Release nsis`
* There is mismatch folder name in dependencies. To fix, run following command in powershell
  `Rename-Item C:\CraftRoot\build\libs\runtime\image-RelWithDebInfo-16\ image-Release-16`
* Create package for kovan: `craft --buildtype Release --package owncloud-client`
* Rename setup exe `Get-ChildItem -Path "C:\CraftRoot\tmp\" | Where-Object { $_.Name -like "owncloud-client-kovan*.exe" } | %{ Rename-Item -LiteralPath $_.FullName -NewName "Kovan-2.6.3.exe" }`

### Signing the package (Requires a code signing certificate, following example is for Global Sign)
* Open "Developer Command Prompt" in Visual Studio from Tools -> Command Line
* Sign the package with this command: `signtool sign /a /tr http://rfc3161timestamp.globalsign.com/advanced /td SHA256 "C:\CraftRoot\tmp\Kovan-2.6.3.exe"`

## Reporting issues and contributing

If you find any bugs or have any suggestion for improvement, please
file an issue at https://yardim.itu.edu.tr Do not
contact the authors directly by mail, as this increases the chance
of your report being lost.

If you created a patch, please submit a [Pull
Request](https://github.com/ITUBIDB/kovan-desktop-client/pulls).

## Maintainers and Contributors

The current maintainers of this repository are:

* ITU-BIDB <sistemdestek@itu.edu.tr>

Kovan Desktop Client is developed by the ownCloud community and branded by ITU BIDB for usage of Istanbul Technical University.

## License

    This program is free software; you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation; either version 2 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful, but
    WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY
    or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License
    for more details.
