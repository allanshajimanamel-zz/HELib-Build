- How to build the custom toolchain.

Run the below commands in order.

Toolchain build commands:
	1.) export NDK_PATH=/home/allan/Android/Ndk/android-ndk-r10e

	2.) $NDK_PATH/build/tools/make-standalone-toolchain.sh --platform=android-15 --stl=libcxx --llvm-version=3.5 --toolchain=arm-linux-androideabi-4.8 --arch=arm --ndk-dir=$NDK_PATH --system=linux-x86_64

The toolchain will be built and zipped in the /tmp/ folder as arm-linux-androideabi-4.8.zip

- How to build the gmp library.

Download gmp-6.1.0 from the internet and extract the contents to a location of your choice. 
Extract the toolchain into the gmp folder.
Run the below commands in order.

GMP build commands:

	1.) ./configure --prefix=$HOME/android -host=arm-none-linux-gnueabi CC="/home/allan/Desktop/gmp-6.1.0/arm-linux-androideabi-4.8/bin/arm-linux-androideabi-clang++ --sysroot=/home/allan/Desktop/gmp-6.1.0/arm-linux-androideabi-4.8/sysroot" LD="/home/allan/Desktop/gmp-6.1.0/arm-linux-androideabi-4.8/bin/arm-linux-androideabi-ld" AR="/home/allan/Desktop/gmp-6.1.0/arm-linux-androideabi-4.8/bin/arm-linux-androideabi-ar" RANLIB="/home/allan/Desktop/gmp-6.1.0/arm-linux-androideabi-4.8/bin/arm-linux-androideabi-ranlib" CFLAGS="-lstdc++ -latomic"


	2.) make


	3.) make install
	
GMP test:
Create a new folder and extract the toolchain into it. 
Copy over the built folders for gmp (i.e. lib, include, share).
Download the test program from the below link
	https://bitbucket.org/allanshajimanamel/helib-build-test/downloads/testgmp.cpp

Build the testgmp.cpp using the below commands	
	/home/allan/Desktop/gmptest/arm-linux-androideabi-4.8/bin/arm-linux-androideabi-clang++ --sysroot=/home/allan/Desktop/gmptest/arm-linux-androideabi-4.8/sysroot/ -Llib/ -Iinclude/ testgmp.cpp -lgmp -lstdc++ -latomic  -o gmptest
	
Connect a unlocked rooted android mobile to the computer in debugging mode.
push the gmptest executable to the mobile using the commands below, along with  the libraries in the lib folder of gmp.
	adb push gmptest /data/local/tmp/
	adb push libgmp.a /data/local/tmp/
	adb push libgmp.so /data/local/tmp/
	adb push libgmp.la /data/local/tmp/
	adb push libgmp.so.10 /data/local/tmp/
	adb push libgmp.so.10.3.0 /data/local/tmp/
	
Open a terminal and get the adb shell and execute the binary in the adb shell as with the commands documented below.
	adb shell // command to open the adb shell.
	In the shell do the following:
		su root
		cd /data/local/tmp/
		export LD_LIBRARY_PATH=/data/local/tmp/
		./gmptest
	you should get the result as 2470450.