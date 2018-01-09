#
# this file was created by a computer. trust it.
#

# compiler tools
XILINX_VIVADO_HLS ?= $(XILINX_SDX)/Vivado_HLS

SDX_CXX ?= $(XILINX_SDX)/bin/xcpp
XOCC ?= $(XILINX_SDX)/bin/xocc
EMCONFIGUTIL = $(XILINX_SDX)/bin/emconfigutil --od .
RM = rm -f
RMDIR = rm -rf

SDX_PLATFORM = xilinx:aws-vu9p-f1:4ddr-xpr-2pr:4.0

# host compiler global settings
CXXFLAGS += -DSDX_PLATFORM=$(SDX_PLATFORM) -D__USE_XOPEN2K8 -I/opt/Xilinx/SDx/2017.1.op/runtime/include/1_2/ -I/opt/Xilinx/SDx/2017.1.op/Vivado_HLS/include/ -O2 -Wall -c -fmessage-length=0 -std=c++14
LDFLAGS += -lxilinxopencl -lpthread -lrt -lstdc++ -L/opt/Xilinx/SDx/2017.1.op/runtime/lib/x86_64

# kernel compiler global settings
XOCC_OPTS = -t hw --platform /home/centos/src/project_data/aws-fpga/SDAccel/aws_platform/xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0/xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xpfm --save-temps  --report system

#
# OpenCL kernel files
#

BINARY_CONTAINERS += binary_container_1.xclbin

BUILD_SUBDIRS += binary_container_1
BINARY_CONTAINER_1_OBJS += binary_container_1/vector_add.xo
ALL_KERNEL_OBJS += binary_container_1/vector_add.xo
BUILD_SUBDIRS += binary_container_1
BINARY_CONTAINER_1_OBJS += binary_container_1/vector_sub.xo
ALL_KERNEL_OBJS += binary_container_1/vector_sub.xo

ALL_MESSAGE_FILES = $(subst .xo,.mdb,$(ALL_KERNEL_OBJS)) $(subst .xclbin,.mdb,$(BINARY_CONTAINERS))

#
# host files
#

HOST_OBJECTS += host.o
HOST_OBJECTS += xcl2.o

HOST_EXE = hello.exe

BUILD_SUBDIRS += 

EMCONFIG_FILE = emconfig.json

#
# primary build targets
#

.PHONY: all clean
all: $(BINARY_CONTAINERS) $(HOST_EXE) $(EMCONFIG_FILE)

clean:
	-$(RM) $(BINARY_CONTAINERS) $(ALL_KERNEL_OBJS) $(ALL_MESSAGE_FILES) $(EMCONFIG_FILE) $(HOST_EXE) $(HOST_OBJECTS)
	-$(RMDIR) $(BUILD_SUBDIRS)
	-$(RMDIR) _xocc*
	-$(RMDIR) .Xil

.PHONY: incremental
incremental: all

include makeemconfig.mk

#
# binary container: binary_container_1.xclbin
#

binary_container_1/vector_add.xo: ../vector_addition.cl
	@mkdir -p $(@D)
	-@$(RM) $@
	$(XOCC) $(XOCC_OPTS) -c -k vector_add --messageDb $(subst .xo,.mdb,$@) -I"$(<D)" --xp misc:solution_name=_xocc_compile_binary_container_1_vector_add -o"$@" "$<"
	-@$(RMDIR) .Xil

binary_container_1/vector_sub.xo: ../vector_subtraction.cl
	@mkdir -p $(@D)
	-@$(RM) $@
	$(XOCC) $(XOCC_OPTS) -c -k vector_sub --messageDb $(subst .xo,.mdb,$@) -I"$(<D)" --xp misc:solution_name=_xocc_compile_binary_container_1_vector_sub -o"$@" "$<"
	-@$(RMDIR) .Xil

binary_container_1.xclbin: $(BINARY_CONTAINER_1_OBJS)
	$(XOCC) $(XOCC_OPTS) -l --nk vector_add:1  --nk vector_sub:1 --messageDb $(subst .xclbin,.mdb,$@) --xp misc:solution_name=_xocc_link_binary_container_1 -o"$@" $(+)
	-@$(RMDIR) .Xil

#
# host rules
#

host.o: ../host.cpp ../xcl2.hpp
	@mkdir -p $(@D)
	$(SDX_CXX) $(CXXFLAGS) -o "$@" "$<"

xcl2.o: ../xcl2.cpp ../xcl2.hpp
	@mkdir -p $(@D)
	$(SDX_CXX) $(CXXFLAGS) -o "$@" "$<"

$(HOST_EXE): $(HOST_OBJECTS)
	$(SDX_CXX) -o "$@" $(+) $(LDFLAGS)

#
# emulation configuration file
#

$(EMCONFIG_FILE): makeemconfig.mk
	$(EMCONFIGUTIL) --platform /home/centos/src/project_data/aws-fpga/SDAccel/aws_platform/xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0/xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xpfm --nd $(NUMBER_OF_DEVICES)
	-@$(RMDIR) TempConfig .Xil

