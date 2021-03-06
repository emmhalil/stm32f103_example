#====================================================================#
#Output files
EXECUTABLE=stm32_executable.elf
BIN_IMAGE=stm32_bin_image.bin

#======================================================================#
#Cross Compiler
CC=arm-none-eabi-gcc
OBJCOPY=arm-none-eabi-objcopy

#======================================================================#
#Flags
CFLAGS=-g -mlittle-endian -mthumb -nostdlib -ffreestanding
CFLAGS+=-mcpu=cortex-m3
CFLAGS+=-D USE_STDPERIPH_DRIVER
CFLAGS+=-I./
CFLAGS+=-I../USART/printf/
CFLAGS+=-D STM32F10X_HD


#stm32-flash
CFLAGS+=-Wl,-T,stm32_flash.ld

#======================================================================#
#Libraries

#Stm32 libraries
ST_LIB=../lib/STM32F10x_StdPeriph_Driver

#CMSIS libraries
CFLAGS+=-I../lib/CMSIS/CM3/CoreSupport

#StdPeriph includes
CFLAGS+=-I$(ST_LIB)/inc
CFLAGS+=-I../lib/CMSIS/CM3/DeviceSupport/ST/STM32F10x/
#======================================================================#
#setup system clock
SRC=./system_stm32f10x.c
#StdPeriph
SRC+=$(ST_LIB)/src/misc.c \
	$(ST_LIB)/src/stm32f10x_rcc.c \
	$(ST_LIB)/src/stm32f10x_gpio.c \
	$(ST_LIB)/src/stm32f10x_adc.c \
	$(ST_LIB)/src/stm32f10x_dma.c \
	$(ST_LIB)/src/stm32f10x_usart.c

#Major programs
SRC+=./main.c \
	./adc.c \
	../USART/printf/string.c \
	../USART/printf/usart.c

#======================================================================#
#STM32 startup file
STARTUP=../lib/CMSIS/CM3/DeviceSupport/ST/STM32F10x/startup/gcc_ride7/startup_stm32f10x_md.s


#======================================================================#
#Make rules

#Make all
all:$(BIN_IMAGE)

$(BIN_IMAGE):$(EXECUTABLE)
	$(OBJCOPY) -O binary $^ $@

STARTUP_OBJ = startup_stm32f10x_md.o

$(STARTUP_OBJ): $(STARTUP) 
	$(CC) $(CFLAGS) $^ -c $(STARTUP)

$(EXECUTABLE):$(SRC) $(STARTUP_OBJ)
	$(CC) $(CFLAGS) $^ -o $@

#Make clean
clean:
	rm -rf $(EXECUTABLE)
	rm -rf $(BIN_IMAGE)
#Make flash
flash:
	st-flash write $(BIN_IMAGE) 0x8000000 
gdb_server:
	st-flash --reset write $(EXECUTABLE) 0x8000000 && \
	st-util  
gdb:	
	arm-none-eabi-gdb -x gdb_init.gdb 

#======================================================================
.PHONY:all clean flash gdb_server gdb
