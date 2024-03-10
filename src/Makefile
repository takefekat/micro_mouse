CC = rx-elf-gcc
CP = rx-elf-g++
LINKER = ./generate/linker_script.ld

SRCS := $(wildcard ./src/*.cpp) $(wildcard ./generate/*.c) $(wildcard ./generate/*.S)
OBJS := $(SRCS:.cpp=.o)
OBJS := $(OBJS:.c=.o)
OBJS := $(OBJS:.S=.o)

all: gcc_rx_project.elf gcc_rx_project.mot gcc_rx_project.siz

gcc_rx_project.elf: $(OBJS)
	$(CP) -O0 -ffunction-sections -fdata-sections -fdiagnostics-parseable-fixits -Wstack-usage=100 -g2 -mcpu=rx600 -misa=v1 -mlittle-endian-data -o "gcc_rx_project.elf" $(OBJS) $(USER_OBJS) $(LIBS) -T $(LINKER) -Wl,--start-group -lm -lc -lgcc -lstdc++ -Wl,--end-group -nostartfiles -Wl,-e_PowerON_Reset -Wl,-M=gcc_rx_project.map

gcc_rx_project.mot: gcc_rx_project.elf
	rx-elf-objcopy "gcc_rx_project.elf" -O srec -I elf32-rx-be-ns  "gcc_rx_project.mot"

gcc_rx_project.siz: gcc_rx_project.elf
	rx-elf-size --format=berkeley "gcc_rx_project.elf"

%.o: %.cpp
	$(CP) -O0 -ffunction-sections -fdata-sections -fdiagnostics-parseable-fixits -Wstack-usage=100 -g2 -mcpu=rx600 -misa=v1 -mlittle-endian-data -I./generate -DCPPAPP -Wa,-adlnh="gcc_rx_project.lst" -MMD -MP -MF"$*.d" -MT"$*.o"  -c "$<" -o "$@"

%.o: %.c
	$(CC) -O0 -ffunction-sections -fdata-sections -fdiagnostics-parseable-fixits -Wstack-usage=100 -g2 -mcpu=rx600 -misa=v1 -mlittle-endian-data -I./generate -Wa,-adlnh="hwinit.lst" -MMD -MP -MF"$*.d" -MT"$*.o" "$<" -c -o "$@"

%.o: %.S
	$(CC) -O0 -ffunction-sections -fdata-sections -fdiagnostics-parseable-fixits -Wstack-usage=100 -g2 -mcpu=rx600 -misa=v1 -mlittle-endian-data -x assembler-with-cpp -Wa,--gdwarf2 -DCPPAPP -I./src -Wa,-adlhn="start.lst" -MMD -MP -MF"$*.d" -MT"$*.o" -c "$<" -o "$@"

.PHONY: clean
clean:
	-rm generate/*.d src/*.d
	-rm generate/*.o src/*.o
	-rm ./*.lst
	-rm gcc_rx_project.elf
	-rm gcc_rx_project.lst
	-rm gcc_rx_project.map
	-rm gcc_rx_project.mot