# Define the basics
MCU = atmega328p
PROGRAMMER = arduino
PORT = /dev/ttyACM0
F_CPU = 16000000UL

# Compiler and Flags + Utils
CC = avr-gcc
CFLAGS = -mmcu=$(MCU) -DF_CPU=$(F_CPU) -Wall -Wextra -Werror -Os -Iinclude

OBJCOPY = avr-objcopy
OBJCPYFLAGS = -O ihex

UTILS = avrdude

# Library and Sources
LIBRARY = 328p-d.a

GPIO_SRC = src/gpio_driver.c
GPIO_OBJ = $(GPIO_SRC:.c=.o)

USART_SRC = src/usart_driver.c
USART_OBJ = $(USART_SRC:.c=.o)

TWI_SRC = src/twi_driver.c
TWI_OBJ = $(TWI_SRC:.c=.o)

PWM_SRC = src/pwm_driver.c
PWM_OBJ = $(PWM_SRC:.c=.o)

ADC_SRC = src/adc_driver.c
ADC_OBJ = $(ADC_SRC:.c=.o)

# File to keep track of library objects
LIB_OBJS_FILE = lib_objs.txt

# Main app
MAIN_SRC = main.c
MAIN_OBJ = $(MAIN_SRC:.c=.o)
HEX_FILE = main.hex

# Rule to build the object
#%.o: %.c
#	$(CC) $(CFLAGS) -c $< -o $@

# Rule to add LED to LIB_OBJS
gpio: $(GPIO_OBJ)
	echo $(GPIO_OBJ) >> $(LIB_OBJS_FILE)
	@echo "GPIO driver added to the library!"

usart: $(USART_OBJ)
	echo $(USART_OBJ) >> $(LIB_OBJS_FILE)
	@echo "USART driver added to the library!"

twi: $(TWI_OBJ)
	echo $(TWI_OBJ) >> $(LIB_OBJS_FILE)
	@echo "TWI driver added to the library!"

pwm: $(PWM_OBJ)
	echo $(PWM_OBJ) >> $(LIB_OBJS_FILE)
	@echo "PWM driver added to the library!"

adc: $(ADC_OBJ)
	echo $(ADC_OBJ) >> $(LIB_OBJS_FILE)
	@echo "ADC driver added to the library!"

# Rule to create the static library from the objects
lib: $(LIB_OBJS_FILE)
	@echo "Creating library with objects: $(shell cat $(LIB_OBJS_FILE))"
	@ar rcs $(LIBRARY) $(shell cat $(LIB_OBJS_FILE))
	@echo "Library created: $(LIBRARY)"

# All rule for ease of use
all: gpio usart twi pwm adc
	@echo "All drivers added to the library!"

# Rule to build the main app and link it with the library
run: all $(MAIN_OBJ) $(LIBRARY)
	$(CC) $(CFLAGS) -o temp.elf $(MAIN_SRC) $(LIBRARY)
	$(OBJCOPY) $(OBJCPYFLAGS) temp.elf $(HEX_FILE)
	@echo "Hex file created: $(HEX_FILE)"

# Flash the program to the atmega328p
flash: $(HEX_FILE)
	$(UTILS) -c $(PROGRAMMER) -p $(MCU) -P $(PORT) -U flash:w:$(HEX_FILE)

# Build the objects
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

# Clean rule
clean:
	rm -f $(LIBRARY) $(MAIN_OBJ) $(HEX_FILE) temp.elf $(GPIO_OBJ) $(USART_OBJ) $(TWI_OBJ) $(PWM_OBJ) $(ADC_OBJ) $(LIB_OBJS_FILE)

# Phony targets
.PHONY: all run flash lib clean gpio usart twi pwm adc
