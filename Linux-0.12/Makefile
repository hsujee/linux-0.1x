
# indicate the Hardware Image file
HDA_IMG = hdc-0.11.img

#
# if you want the ram-disk device, define this to be the
# size in blocks.
#
RAMDISK =  #-DRAMDISK=1024

# This is a basic Makefile for setting the general configuration
include Makefile.header

LDFLAGS	+= -Ttext 0 -e startup_32
CFLAGS	+= $(RAMDISK)
CPP	+= -Iinclude

#
# ROOT_DEV specifies the default root-device when making the image.
# This can be either FLOPPY, /dev/xxxx or empty, in which case the
# default of /dev/hd6 is used by 'build'.
#
ROOT_DEV=0301
SWAP_DEV=0304

ARCHIVES=kernel/kernel.o mm/mm.o fs/fs.o
DRIVERS =kernel/blk_drv/blk_drv.a kernel/chr_drv/chr_drv.a
MATH	=kernel/math/math.a
LIBS	=lib/lib.a

.c.s:
	$(CC) $(CFLAGS) \
	-nostdinc -Iinclude -S -o $*.s $<
.s.o:
	$(AS) -o $*.o $<
.c.o:
	$(CC) $(CFLAGS) \
	-nostdinc -Iinclude -c -o $*.o $<


all: clean Image

Image: boot/bootsect boot/setup tools/system
	@cp -f tools/system system.tmp
	@strip system.tmp
	@objcopy -O binary -R .note -R .comment system.tmp tools/kernel
	@tools/build.sh boot/bootsect boot/setup tools/kernel Image $(ROOT_DEV) $(SWAP_DEV)
	@rm system.tmp
	@rm -f tools/kernel
	@sync

boot/bootsect: boot/bootsect.S
	@make bootsect -C boot

boot/setup: boot/setup.S
	@make setup -C boot

boot/head.o: boot/head.s
	@make head.o -C boot

tools/build: tools/build.c
	$(CC) $(CFLAGS) \
	-o tools/build tools/build.c

tools/system:	boot/head.o init/main.o \
		$(ARCHIVES) $(DRIVERS) $(MATH) $(LIBS)
	$(LD) $(LDFLAGS) boot/head.o init/main.o \
	$(ARCHIVES) \
	$(DRIVERS) \
	$(MATH) \
	$(LIBS) \
	-o tools/system
	@nm tools/system | grep -v '\(compiled\)\|\(\.o$$\)\|\( [aU] \)\|\(\.\.ng$$\)\|\(LASH[RL]DI\)'| sort > System.map
	@objdump -S tools/system > system.S

kernel/math/math.a:
	@make -C kernel/math

fs/fs.o:
	@make -C fs

kernel/kernel.o:
	@make -C kernel

mm/mm.o:
	@make -C mm

lib/lib.a:
	@make -C lib

kernel/blk_drv/blk_drv.a:
	@make -C kernel/blk_drv

kernel/chr_drv/chr_drv.a:
	@make -C kernel/chr_drv

clean:
	@rm -f Image System.map System_s.map system.S tmp_make core boot/bootsect boot/setup
	@rm -f init/*.o tools/system boot/*.o typescript* info bochsout.txt
	@for i in mm fs kernel lib boot; do make clean -C $$i; done

dep:
	@sed '/\#\#\# Dependencies/q' < Makefile > tmp_make
	@(for i in init/*.c;do echo -n "init/";$(CPP) -M $$i;done) >> tmp_make
	@cp tmp_make Makefile
	@for i in fs kernel mm lib; do make dep -C $$i; done

tag: tags
tags:
	@ctags -R

cscope:
	@cscope -Rbkq

start:
	@qemu-system-i386 -m 32M -boot a -fda Image -hda $(HDA_IMG)

debug:
	@qemu-system-i386 -m 32M -boot a -fda Image -hda $(HDA_IMG) \
	-s -S

bochs-debug:
	@$(BOCHS) -q -f tools/bochs/bochsrc/bochsrc-hd-dbg.bxrc	

bochs:
ifeq ($(BOCHS),)
	@(cd tools/bochs/bochs-2.3.7; \
	./configure --enable-plugins --enable-disasm --enable-gdb-stub;\
	make)
endif

bochs-clean:
	@make clean -C tools/bochs/bochs-2.3.7

calltree:
ifeq ($(CALLTREE),)
	@make -C tools/calltree-2.3
endif

calltree-clean:
	@(find tools/calltree-2.3 -name "*.o" \
	-o -name "calltree" -type f | xargs -i rm -f {})

cg: callgraph
callgraph:
	@calltree -b -np -m init/main.c | tools/tree2dotx > linux-0.11.dot
	@dot -Tjpg linux-0.11.dot -o linux-0.11.jpg

help:
	@echo "<<<<This is the basic help info of linux-0.11>>>"
	@echo ""
	@echo "Usage:"
	@echo "     make --generate a kernel floppy Image with a fs on hda1"
	@echo "     make start -- start the kernel in qemu"
	@echo "     make debug -- debug the kernel in qemu & gdb at port 1234"
	@echo "     make disk  -- generate a kernel Image & copy it to floppy"
	@echo "     make cscope -- genereate the cscope index databases"
	@echo "     make tags -- generate the tag file"
	@echo "     make cg -- generate callgraph of the system architecture"
	@echo "     make clean -- clean the object files"
	@echo "     make distclean -- only keep the source code files"
	@echo ""
	@echo "Note!:"
	@echo "     * You need to install the following basic tools:"
	@echo "          ubuntu|debian, qemu|bochs, ctags, cscope, calltree, graphviz "
	@echo "          vim-full, build-essential, hex, dd, gcc 4.3.2..."
	@echo "     * Becarefull to change the compiling options, which will heavily"
	@echo "     influence the compiling procedure and running result."
	@echo ""
	@echo "Author:"
	@echo "     * 1991, linus write and release the original linux 0.95(linux 0.11)."
	@echo "     * 2005, jiong.zhao<gohigh@sh163.net> release a new version "
	@echo "     which can be used in RedHat 9 along with the book 'Explaining "
	@echo "     Linux-0.11 Completly', and he build a site http://www.oldlinux.org"
	@echo "     * 2008, falcon<wuzhangjin@gmail.com> release a new version which can be"
	@echo "     used in ubuntu|debian 32bit|64bit with gcc 4.3.2, and give some new "
	@echo "     features for experimenting. such as this help info, boot/bootsect.s and"
	@echo "     boot/setup.s with AT&T rewritting, porting to gcc 4.3.2 :-)"
	@echo ""
	@echo "<<<Be Happy To Play With It :-)>>>"

### Dependencies:
init/main.o: init/main.c include/unistd.h include/sys/stat.h \
 include/sys/types.h include/sys/time.h include/time.h \
 include/sys/times.h include/sys/utsname.h include/sys/param.h \
 include/sys/resource.h include/utime.h include/linux/tty.h \
 include/termios.h include/linux/sched.h include/linux/head.h \
 include/linux/fs.h include/linux/mm.h include/linux/kernel.h \
 include/signal.h include/asm/system.h include/asm/io.h include/stddef.h \
 include/stdarg.h include/fcntl.h include/string.h
