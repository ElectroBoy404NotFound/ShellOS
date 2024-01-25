rwildcard=$(foreach d,$(wildcard $(1:=/*)),$(call rwildcard,$d,$2) $(filter $(subst *,%,$2),$d))

SRCDIR = kernel/ASM
SRC = $(call rwildcard,$(SRCDIR),*.asm)
OBJS = $(patsubst $(SRCDIR)/%.asm, $(OBJDIR)/%.o, $(SRC))
OBJDIR := output

build:
	rm -rf output
	mkdir output
	cd output; mkdir ${shell ls kernel/ASM}
	make bootloader
	make kernel
	cat output/bootloader.bin output/kernel.bin > output/os.bin
bootloader:
	nasm bootloader/boot.asm -f bin -o output/bootloader.bin
kernel: loader $(OBJS) link
loader:
	nasm kernel/KernelLoader.asm -f elf64 -o output/kernel.elf
$(OBJDIR)/%.o: $(SRCDIR)/%.asm
	nasm -felf64 $^ -o $@
link:
	echo $(OBJS)
	ld -T"kernel.ld" $(OBJS)
run:
	qemu-system-x86_64 -drive format=raw,if=floppy,file=output/os.bin
.PHONY: run clean build bootloader kernel
vs:
	code ./ --user-data-dir='.vscode' --no-sandbox
