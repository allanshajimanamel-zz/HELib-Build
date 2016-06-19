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

How to build NTL

	NTL build commands: 
		1.) Download and extract NTL-9.6.4 on your computer.
		2.) Download the files from the below links and replace the corresponding files in the "src" directory.
			https://bitbucket.org/allanshajimanamel/helib-build-test/downloads/MakeGetTime
			https://bitbucket.org/allanshajimanamel/helib-build-test/downloads/MakeGetPID
			https://bitbucket.org/allanshajimanamel/helib-build-test/downloads/makefile
			https://bitbucket.org/allanshajimanamel/helib-build-test/downloads/MakeCheckFeature
			https://bitbucket.org/allanshajimanamel/helib-build-test/downloads/AndroidRemoveProg
		3.) Open the makefile in a text editor and change the paths of toolchain, compiler, gmp library (previously compiled), etc according to your system. Close and save it.
		4.) Open terminal and navigate to the src folder.
		5.) Connect the test phone to the computer in debugging mode.
		5.) run command 
				make setup1
		6.) open adb shell and run the following commands.
				su root
				cd /data/local/tmp/
				./MakeDesc
		7.) Quit adb shell
		8.) in the terminal run the following commands
				make setup3
		9.) Open adb shell and navigate to "/data/local/tmp/".
		10.) Check if the compiled gmp libraries are there, if not push them from computer to phone from terminal
				adb push libgmp.a /data/local/tmp/
				adb push libgmp.so /data/local/tmp/
				adb push libgmp.la /data/local/tmp/
				adb push libgmp.so.10 /data/local/tmp/
				adb push libgmp.so.10.3.0 /data/local/tmp/
		11.) In adb shell run command
				export LD_LIBRARY_PATH=/data/local/tmp/
				./gen_gmp_aux > gmp_aux.h
		12.) Copy over the file gmp_aux.h to the include directory of ntl on the computer
		13.) In the include directory of ntl open file ctools.h in an editor and comment out the following lines.
				a) 	#elif (NTL_BITS_PER_LONG == 64 && defined(__GNUC__))
					#define NTL_LL_TYPE __int128_t
					
				b)	#elif (NTL_BITS_PER_LONG == 64 && defined(__GNUC__))
					#define NTL_ULL_TYPE __uint128_t
		14.) On the terminal from the src folder of NTL run the following commands
				make
				make install
		15.) NTL should now be built and can be found in the build folder inside src.
		
	NTL test:
		1.) Download the test file from the link below
				https://bitbucket.org/allanshajimanamel/helib-build-test/downloads/testgmp.cpp
		2.) Compile it using the below command
				
				/home/allan/Desktop/ntltest/arm-linux-androideabi-4.8/bin/arm-linux-androideabi-clang++ --sysroot=/home/allan/Desktop/ntltest/arm-linux-androideabi-4.8/sysroot ntltest.cpp -I/home/allan/Desktop/ntltest/include -I/home/allan/Desktop/ntltest/gmp/include -nostdlib -Bdynamic -Wl,-dynamic-linker,/system/bin/linker -Wl,--gc-sections -Wl,-z,nocopyreloc /home/allan/Desktop/ntltest/arm-linux-androideabi-4.8/sysroot/usr/include -fpic -mthumb-interwork -ffunction-sections -funwind-tables -fstack-protector -fno-short-enums -fomit-frame-pointer -fomit-frame-pointer -fstrict-aliasing -funswitch-loops -finline-limit=300 -D__ARM_ARCH_5__ -D__ARM_ARCH_5T__ -D__ARM_ARCH_5E__ -D__ARM_ARCH_5TE__ -Wno-psabi -Wa,--noexecstack -march=armv7-a -mfloat-abi=softfp -mfpu=vfp -Wl,-rpath-link=/home/allan/Desktop/new_builds/ntl/ntl-9.6.4/arm-linux-androideabi-4.8/sysroot/usr/lib -L/home/allan/Desktop/ntltest/arm-linux-androideabi-4.8/sysroot/usr/lib /home/allan/Desktop/ntltest/arm-linux-androideabi-4.8/sysroot/usr/lib/crtbegin_dynamic.o /home/allan/Desktop/ntltest/arm-linux-androideabi-4.8/bin/../lib/gcc/arm-linux-androideabi/4.8/libgcc.a /home/allan/Desktop/ntltest/arm-linux-androideabi-4.8/sysroot/usr/lib/crtend_android.o -lstdc++ -lc -lm -L/home/allan/Desktop/ntltest/lib -L/home/allan/Desktop/ntltest/gmp/lib -L/home/allan/Desktop/ntltest/boost  -o ntl1 -lgmp -lntl -lstdc++ -latomic
				
		3.) Push ntl1 along with all the ntl and gmp libraries to the phone and run the ntl1 executable.