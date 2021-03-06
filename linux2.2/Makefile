#
# Makefile 1.00 Peter Braam <braam@cs.cmu.edu>
#

include ../config.mk

ifndef CFLAGS
# Don't remove "-O2" or bad things will happen!
KFLAGS = -O2 -Wall -Wstrict-prototypes -Winline -pipe
endif

CPPFLAGS = $(PCDEBUG) -D__KERNEL__ -DMODULE -I../include \
	   -I. -I$(LINUX)/include $(SMPFLAG)
COFLAGS = -kv

CFLAGS= $(KFLAGS) $(MFLAG)

SRCS= psdev.c cnode.c inode.c dir.c file.c upcall.c coda_linux.c symlink.c pioctl.c cache.c sysctl.c
OBJS=$(SRCS:%.c=%.o)

ifeq ($(ARCH),ppc)
CFLAGS += -msoft-float
endif

MODULES = coda.o

all:	../.prereq.ok kcheck $(MODULES) 

showvar:
	@echo CFLAGS $(CFLAGS)
	@echo CPPFLAGS $(CPPFLAGS)
	@echo MODDIR $(MODDIR)
	@echo UTS_RELEASE $(UTS_RELEASE)
	@echo SRCS $(SRCS)
	@echo OBJS $(OBJS)

clean:
	rm -f core core.* *.o .*.o *.s *.a *~ .depend .depfiles/*.d

install: $(MODULES) ../.prereq.ok kcheck
	-mkdir -p $(MODDIR)/fs
	install -o root -g root -m 644 $(MODULES) $(MODDIR)/fs/

# Stuff to automatically maintain dependency files

.c.o:
	$(CC) -MD $(CFLAGS) $(CPPFLAGS) -c $<
	@mkdir -p .depfiles ; mv $*.d .depfiles

kcheck:
	@. ../config.out ; \
	if [ "$$CHECK" != "" ] ; then \
	    if [ "`cksum < $$CHECK`" != "$$CKSUM" ] ; then \
		/bin/echo -n "Kernel configuration has changed." ; \
		/bin/echo "  Please re-run 'make config'." ; \
		exit 1 ; \
	    fi ; \
	fi

coda.o: $(OBJS)
	$(LD) -m "`ld --help | awk '/supported emulations/ {print $$4}'`" -r -o $@ $(OBJS)

-include $(SRCS:%.c=.depfiles/%.d)
