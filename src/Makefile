#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material.
#
#*****************************************************************************

#------------------------------------------------------------------------------
# A makefile to create a build system that supports two platform,HOST and MSP432
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      %.i --->  preprocessed output of all c-program implementation files
#      %.asm ---> assembly output of c-program implementation files and the final output executable
#      %..o ----> object file for all c-source files
#      compile all ---> Compile all object files
#      build ----> Compile all object files and link into a final executable
#      clean ----> remove all compiled objects, preprocessed outputs, assembly outputs, executable files and build output files
# Platform Overrides for MSP432:
#      CPU - ARM Cortex Architecture (cortex-m4)
#      ARCH - ARM Architecture (arm)
#      SPECS - Specs file to give the linker (nano.specs)
#Platform Overrides for HOST:
#      CPU - linux
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides

TARGET = c1m2

# Architectures Specific Flags
ifeq ($(PLATFORM),HOST)
	CPU = intel core i5
	ARCH:=$(shell arch)
else
	LINKER_FILE = -T msp432p401r.lds
	CPU = cortex-m4
	ARCH = armv7e-m
	SPECS = nosys.specs
	DEFINEFLAG = -DHOST

endif


# Compiler Flags and Defines

ifeq ($(PLATFORM),HOST)
	CC = gcc
	LDFLAGS = -Wl,-Map=$(TARGET).map
         CFLAGS = -Wall -Werror -O0 -g -std=c99
else
	CC = arm-none-eabi-gcc
	LD = arm-none-eabi-ld
	LDFLAGS =  -Wl,-Map=$(TARGET).map $(LINKER_FILE)
	CFLAGS =  -mcpu=$(CPU) -march=$(ARCH) --specs=$(SPECS) -mfloat-abi=hard -mfpu=fpv4-sp-d16 -mthumb -Wall -Werror -O0 -g -std=c99
	CPPFLAGs = -M -MF test.d
	DEFINEFLAG = -DMSP432
endif

#Additional variable declaration

OBJS:= $(SOURCES:.c=.o)
ASMS:= $(SOURCES:.c=.s)
PPS:= $(SOURCES:.c=.i)
MAPS:= $(SOURCES:.c=.map)
OUTS:= $(SOURCES:.c=.out)

#generating preprocessed output using -E to stop at preprocessed stage
%.i : %.c
	$(CC) $(CPPFLAGs) $(DEFINEFLAG) $<
	$(CC) $(DEFINEFLAG) -E -o $@ $<

#generating assembly output using -S to stop at assembly stage
%.asm : %.c
	$(CC) $(CPPFLAGs) $(DEFINEFLAG) $<
	objdump $(OBJS) -D

#generating object file output
%.o : %.c
	$(CC) $(CPPFLAGs) $(DEFINEFLAG) $<

#Compiling all object files
.PHONY: compile-all
compile-all: $(SOURCES)
#$(OBJS): $(SOURCES)
	$(CC) $(DEFINEFLAG) $(CFLAGS) -c $(SOURCES)

#Compile all object files and link into a final executable
.PHONY: build
build: $(OUTS)
$(OUTS): $(SOURCES)
	$(CC) $(DEFINEFLAG) $(CFLAGS) $(LDFLAGS) -o c1m2.out $^
	size c1m2.out

#remove all compiled objects, preprocessed outputs, assembly outputs, executable files and build output files
.PHONY: clean
clean:
	rm -f *.i *.d *.map *.s *.out *.o
