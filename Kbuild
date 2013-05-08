#
# Kbuild for top-level directory of the kernel
# This file takes care of the following:
# 1) Generate asm-offsets.h
# 2) Check for missing system calls

#####
# 1) Generate asm-offsets.h
#

offsets-file := include/asm-$(SRCARCH)/asm-offsets.h

always  := $(offsets-file)
targets := $(offsets-file)
targets += arch/$(SRCARCH)/kernel/asm-offsets.s
clean-files := $(addprefix $(objtree)/,$(targets))

# Default sed regexp - multiline due to syntax constraints
define sed-y
	"/^->/{s:^->\([^ ]*\) [\$$#]*\([^ ]*\) \(.*\):#define \1 \2 /* \3 */:; s:->::; p;}"
endef
# Override default regexp for specific architectures
sed-$(CONFIG_MIPS) := "/^@@@/{s/^@@@//; s/ \#.*\$$//; p;}"

quiet_cmd_offsets = GEN     $@
define cmd_offsets
	(set -e; \
	 echo "#ifndef __ASM_OFFSETS_H__"; \
	 echo "#define __ASM_OFFSETS_H__"; \
	 echo "/*"; \
	 echo " * DO NOT MODIFY."; \
	 echo " *"; \
	 echo " * This file was generated by Kbuild"; \
	 echo " *"; \
	 echo " */"; \
	 echo ""; \
	 sed -ne $(sed-y) $<; \
	 echo ""; \
	 echo "#endif" ) > $@
endef

# We use internal kbuild rules to avoid the "is up to date" message from make
arch/$(SRCARCH)/kernel/asm-offsets.s: arch/$(SRCARCH)/kernel/asm-offsets.c FORCE
	$(Q)mkdir -p $(dir $@)
	$(call if_changed_dep,cc_s_c)

$(obj)/$(offsets-file): arch/$(SRCARCH)/kernel/asm-offsets.s Kbuild
	$(Q)mkdir -p $(dir $@)
	$(call cmd,offsets)

#####
# 2) Check for missing system calls
#

quiet_cmd_syscalls = CALL    $<
      cmd_syscalls = $(CONFIG_SHELL) $< $(CC) $(c_flags)

PHONY += missing-syscalls
missing-syscalls: scripts/checksyscalls.sh FORCE
	$(call cmd,syscalls)
