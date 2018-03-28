安裝並行Lammps 在CentOS 7教學

安裝Lammps 之前必須要安裝的包

$ sudo yum install epel-release
$ sudo yum install compat-libstdc++-33.x86_64 
$ sudo yum install gcc gcc-c++ kernel-devel 
$ sudo yum groupinstall "Development Tools" 
$ sudo yum install lapack-devel
$ sudo yum install zlib-devel
$ sudo yum install voro++-devel
$ sudo yum install python-devel
$ sudo yum install tcsh 

libjpeg9 連結失效請自行上網搜尋，此rpm尚未收錄到官方的軟體庫中，所以必須要自己找到rpm使用rpm 方式安裝，不能透過yum install
$ sudo rpm -Uvh ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home:/Simmphonie/SLE_12/x86_64/libjpeg9-9.1.0-15.4.x86_64.rpm

安裝mpich，更多說明可以看官方的README
去官方網站下載原始碼，然後解壓切換到解壓縮的目錄上

設定安裝路徑
for csh and tcsh:

$ ./configure --prefix=/home/<USERNAME>/mpich-install |& tee c.txt

for bash and sh:

$ ./configure --prefix=/home/<USERNAME>/mpich-install 2>&1 | tee c.txt

開始編譯mpich
for csh and tcsh:

$ make |& tee m.txt

for bash and sh:

$ make 2>&1 | tee m.txt


開始安裝mpich到之前指定的目錄下
for csh and tcsh:

$ sudo make install |& tee mi.txt

for bash and sh:

$ sudo make install 2>&1 | tee mi.txt

安裝openmpich
去官方網站下載原始碼，然後解壓切換到解壓縮的目錄上

設定安裝路徑
$ ./configure --prefix=/home/<USERNAME>/openmpich-install

開始編譯openmpich
$ make 

開始安裝openmpich到之前指定的目錄下
$ sudo make install

安裝fftw
去官方網站下載原始碼，然後解壓切換到解壓縮的目錄上

設定安裝路徑
$ ./configure --prefix=/home/<USERNAME>/fftw-install

開始編譯fftw
$ make 

開始安裝fftw到之前指定的目錄下
$ sudo make install

安裝jpegsrc
去官方網站下載原始碼，然後解壓切換到解壓縮的目錄上

設定安裝路徑
$ ./configure --prefix=/home/<USERNAME>/jpeg-install

開始編譯jpegsrc
$ make 

開始安裝fftw到之前指定的目錄下
$ sudo make install

去官方下載Lammps 原始碼
解壓縮到指定目錄上

設定lammps 要執行的檔案
位於../src/MAKE 裡面有Makefile.mpi 和Makefile.serial ，為了執行並行所以修改Makefile.mpi

**修改Makefile.mpi，**別忘了在換行之後必須加個"\"符號(不含上引號)，告知lammps換行動作。**

設定與之前安裝的軟件的連結，連結可以是相對路徑，或者無路徑(需要修改.bashrc 加上變數，參考最後一步驟說明)
CC 編譯器使用mpixx，改為eg.CC =/home/<USERNAME>/mpich-install/bin/mpicxxe

CCFLAGS告知系統可以在特定目錄下找到編譯器的目錄
eg.CCFLAGS =	-g -O3 -I/home/<USERNAME>/lammps/src\	-I/home/<USERNAME>/mpich-install/include\	-I/home/<USERNAME>/mpich-install/bin\	


LINK直接指定編譯器的目錄
eg.LINK =	/home/<USERNAME>/mpich-install/bin/mpicxx

修改mpich的include目錄位置
eg.MPI_INC =       -DMPICH_SKIP_MPICXX -DOMPI_SKIP_MPICXX=1\
	-I/home/<USERNAME>/mpich-install/include


修改mpich的lib目錄位置
eg.MPI_PATH =      -L/home/<USERNAME>/mpich-install/lib 


告知系統mpich的特徵名稱(-lmpic_xx改為-lmpicxx)
eg.MPI_LIB =       -lmpi -lmpicxx -lmpich -lmpl

修改fftw的include目錄位置
eg.FFT_INC =  -I/home/<USERNAME>/fftw-install/include


修改fftw的lib目錄位置
eg.FFT_PATH = -L/home/<USERNAME>/fftw-install/lib 


告知系統fftw的特徵名稱
eg.FFT_LIB =  -lfftw3	

修改jpegsrc的include目錄位置
eg.JPG_INC =	-I/home/<USERNAME>/jpeg-install/include 

修改jpegsrc的lib目錄位置
eg.JPG_PATH = -L/home/<USERNAME>/jpeg-install/lib

告知系統jpegsrc的特徵名稱(-libjpeg改為-ljpeg)
eg.JPG_LIB =	-ljpeg	

修改Makefile.mpi之後切換到下載解壓的/home/<USERNAME>/lammps/src 目錄開始編譯
$ make mpi

但出現了如下錯誤，因為默認安裝之前尚未編譯下列套件

[<USERNAME>@lammps src]$ make mpi
make[1]: Entering directory `/home/<USERNAME>/lammps/src/Obj_mpi'
Makefile.package.settings:6: ../../lib/qmmm/Makefile.lammps: No such file or directory
Makefile.package.settings:7: ../../lib/colvars/Makefile.lammps: No such file or directory
Makefile.package.settings:8: ../../lib/atc/Makefile.lammps: No such file or directory
Makefile.package.settings:11: ../../lib/reax/Makefile.lammps: No such file or directory
Makefile.package.settings:12: ../../lib/poems/Makefile.lammps: No such file or directory
Makefile.package.settings:13: ../../lib/meam/Makefile.lammps: No such file or directory
make[1]: *** No rule to make target `../../lib/meam/Makefile.lammps'.  Stop.
make[1]: Leaving directory `/home/<USERNAME>/lammps/src/Obj_mpi'
make[1]: Entering directory `/home/<USERNAME>/lammps/src/Obj_mpi'
Makefile.package.settings:6: ../../lib/qmmm/Makefile.lammps: No such file or directory
Makefile.package.settings:7: ../../lib/colvars/Makefile.lammps: No such file or directory
Makefile.package.settings:8: ../../lib/atc/Makefile.lammps: No such file or directory
Makefile.package.settings:11: ../../lib/reax/Makefile.lammps: No such file or directory
Makefile.package.settings:12: ../../lib/poems/Makefile.lammps: No such file or directory
Makefile.package.settings:13: ../../lib/meam/Makefile.lammps: No such file or directory
make[1]: *** No rule to make target `../../lib/meam/Makefile.lammps'.  Stop.
make[1]: Leaving directory `/home/<USERNAME>/lammps/src/Obj_mpi'
make: *** [mpi] Error 2

因此要解決此問題須分別到各目錄下去編譯，使用cd 指令切換到../../lib/ 底下目錄。如果是makefile.g++ 或Makefile.gfortran表示可以用系統預設的g++或gfortran(gcc)編譯器直接編譯，
但要注意裡面文件是否有CCFLAGS= 這個欄位，若有還必須指定編譯器搜索的庫路徑，例如設置如下

eg.
CCFLAGS =       -O -g -fPIC -I../../src -DMPICH_IGNORE_CXX_SEEK\

	-I/home/<USERNAME>/lammps/src\

	-I/home/<USERNAME>/mpich-install/include\

	-I/home/<USERNAME>/mpich-install/bin
修改好執行
$ make -f makefile.g++

若是makefile.mpicc，則要修改CC=	/mpich編譯器的位置/，例如CC=	/home/<USERNAME>/mpich/mpicxx，若有CCFLAGS 也要一併指定路徑，如同上文描述。
修改好執行
$ make -f makefile.mpicc

Note:g++, gfortran, mpicc 都是編譯器的一種，若沒有其中一個編譯器，就要到那一個編譯器官方去下載源碼解壓，然後指定路徑執行。

另一個問題是Lammps 找不到eigen3 文件，所以根據系統說明在lib 下建立一個includelink，然後將eigen 官方下載的包直接解壓命名為eigen3 即可，不須編譯使用

eg./home/<USERNAME>/lammps/lib/includelink/eigen3

上述問題修正之後，切換到下載解壓的/home/<USERNAME>/lammps/src 目錄，開始編譯產生Lammps 執行檔案
(make clean-all 清除之前編譯的東西)
$ make clean-all
$ make mpi

編譯完畢之後，在/home/<USERNAME>/lammps/src 產生了lmp_mpi 這個就是執行程式
(每個人生成的名稱可能不同，具體名稱要看編譯完畢之後系統告知的名稱，或者在/home/<USERNAME>/lammps/src 下找尋檔案大小最大的檔案，即為Lammps 執行檔案)


eg.
size ../lmp_mpi
   text    data     bss     dec     hex filename
23672587         169468 1612601344      1636443399      618a2507        ../lmp_mpi
make[1]: Leaving directory `/home/brucehuang/Software/lammps/lammps-11Aug17/src/Obj_mpi'


請注意，若需指定Lammps特殊功能套件，則必須自行先安裝lib套件，操作步驟類似上述方法，無非就是透過yum install 或者官方源碼直接編譯安裝，再者就是Lammps 官方默認底下已存在的lib
目錄編譯安裝，這些都操作完之後才能使用$ make yes-XXX 指令安裝，同理若要移除lammps套件可使用$ make no-XXX 指令移除，
這些都操作過後再切換到下載解壓的/home/<USERNAME>/lammps/src 目錄，開始編譯產生Lammps 執行檔案

$ make clean-all
$ make mpi

這樣產生的lammps 程式才是你自己客製化的程式。


為了執行lmp_mpi 不用再輸入路徑，所以修改/home/username 底下的.bashrc 加上下列指令

eg.
PATH=/home/<USERNAME>/lammps/src:"$PATH"

開始跑input file 指令如下
eg.
$ lmp_mpi < input file
or
$ /home/<USERNAME>/lammps/lmp_mpi < input file

其他指令備註

看安裝了哪些Lammps 套件
$ cd /home/<USERNAME>/lammps/src
$ make package-status

看預設安裝目錄的指令
$ ./configure --help
