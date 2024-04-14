# aur-uboot-rockpro64-foss
AUR PKGBUILD for uboot-rockpro64-foss

This package is designed to be built on the RockPro64 itself. It is easiest to build it on ManjaroARM (where `arm-none-eabi-gcc` is in the repo), but it is also possible to compile this on ArchLinuxARM. This package includes working support for SATA boot on the RockPro64, device tree overlay support, and optional (disabled by default) RAM underclocking, which can stabilize a RockPro64 if you are experiencing random program crashes. If you want to try out the RAM underclock, uncomment the 'patch' line in the 'prepare' block below to enable this patch.

Note that more frequencies can be used. See the comment at the top of the PKGBUILD file for details.

If building on ArchLinuxARM, follow the instructions below to build the GCC toolchain.

1. clone the following PKGBUILD repos from Manjaro:
    - https://gitlab.manjaro.org/manjaro-arm/packages/community/arm-none-eabi-gcc.git
    - https://gitlab.manjaro.org/manjaro-arm/packages/community/arm-none-eabi-binutils.git
    - https://gitlab.manjaro.org/manjaro-arm/packages/community/arm-none-eabi-newlib.git
2. Build and install the `arm-none-eabi-binutils` package. This can be built and installed as any AUR package as it has no dependency on the following packages.
3. To bootstrap this process, see here https://stackoverflow.com/questions/72440601/arm-none-eabi-toolchain-compile-from-source
    - cd into the `arm-none-eabi-gcc` folder. Extract the contents of this package (`makepkg --nobuild`), make a folder in 'src' called 'build-gcc-test' (`mkdir src/build-gcc-test`).
    - cd into that new directory (`cd src/build-gcc-test`). Configure the makefile (the `--with-multilib=rmprofile` compiles this for more ARM architectures so newlib can be built for all those architectures): `../gcc-12.1.0/configure --target=arm-none-eabi --prefix=/opt/gnuarm --without-headers --with-newlib --with-gnu-as --with-gnu-ld --enable-languages='c' --enable-frame-pointer=no --with-multilib-list=rmprofile`
    - Then make the compiler: `make -j 6 all-gcc`
    - Install the compiler (to the `/opt` folder): `sudo mkdir /opt/gnuarm; sudo chown <your user name here>:<your username here> /opt/gnuarm. Then install it there with: make install-gcc`
    - Set the PATH environment: `export PATH=$PATH:/opt/gnuarm/bin/`
4. Now with the compile bootstrapped, compile the `arm-none-eabi-newlib` package (if you switch shells or reboot before this step, be sure to reset the PATH environment variable as in the last step in part 3). It should compile and install without errors.
5. Now, go back and delete the 'src' directory in `arm-none-eabi-gcc` to clear out our temp compiler from step 3. Build and install this package as normal. Once installed, remove the `/opt/gnuarm` folder we installed the bootstrap compiler to earlier. Remove the `/opt/gnuarm/bin/` from your PATH environment variable (or create a new shell and exit out of the old one). We want the system to now use the "proper" compiler.
6. I am not sure if this is necessary, but I went back and recompiled (and reinstalled) `arm-none-eabi-newlib` using the "proper" compiler. I then went back and did a recompile (and reinstall) on `arm-none-eabi-gcc`. Both of these should require nothing extra - run `makepkg -f` and then `sudo pacman -U <package name here to build and install>`.




# User comments while this PKGBUILD was in the AUR:
kohlerjl commented on 2023-08-26 18:00 (UTC)
Thanks for this excellent PKGBUILD. I can confirm that the binary AUR package 'gcc-arm-none-eabi-bin' also works for building this package on ArchLinuxARM, as an alternative to building the arm-none-eabi toolchain from Manjaro.
