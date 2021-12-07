# SPDX-License-Identifier: GPL-2.0-only
#
# Makefile for the linux qnx4-filesystem routines.
# ... as kernel modules

ccflags-y += -I$(src) -Wno-int-conversion -DDEBUG -DCONFIG_QNX6FS_FS=y

obj-m := qnx6.o
qnx6-objs := inode.o dir.o namei.o super_mmi.o

KERNEL_SRC ?= /lib/modules/$(shell uname -r)/build

all:
	$(MAKE) -C $(KERNEL_SRC) V=0 M=$$PWD

install:
	cp qnx6.ko $(DESTDIR)/

clean:
	rm -rf *.o *.ko *.mod.c *.symvers *.order .*.cmd .tmp_versions