rwildcard=$(foreach d,$(wildcard $(1:=/*)),$(call rwildcard,$d,$2) $(filter $(subst *,%,$2),$d))

SRCDIR = kernel/C
SRC = $(call rwildcard,$(SRCDIR),*.cpp)
OBJS = $(patsubst $(SRCDIR)/%.cpp, $(OBJDIR)/%.o, $(SRC))
OBJDIR := output

build:
	@rm -rf output
	@mkdir output
	@cd output; mkdir ${shell ls kernel/C}
	@make bootloader
	@make kernel
	@cat output/bootloader.bin output/kernel.bin > output/os.bin
bootloader:
	@nasm bootloader/boot.asm -f bin -o output/bootloader.bin
kernel: loader $(OBJS) link
loader:
	@nasm kernel/KernelLoader.asm -f elf64 -o output/kernel.elf
$(OBJDIR)/%.o: $(SRCDIR)/%.cpp
	@gcc -Ttext 0x8000 -I"kernel/inc" -ffreestanding -mno-red-zone -m64 -c $^ -o $@
link:
	@echo $(OBJS)
	@ld -T"kernel.ld" $(OBJS)
run:
	@qemu-system-x86_64 -drive format=raw,if=floppy,file=output/os.bin
.PHONY: run clean build bootloader kernel
vs:
	@code ./ --user-data-dir='.vscode' --no-sandbox
clean:
	@rm -rf .vscode
	@rm -rf output
