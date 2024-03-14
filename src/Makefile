CC = rx-elf-gcc
CP = rx-elf-g++
LINKER = ./BSW/linker_script.ld

SRCS := $(wildcard ./src/*.cpp) $(wildcard ./src/*/*.cpp) $(wildcard ./src/*/*/*.cpp) $(wildcard ./BSW/*.c) $(wildcard ./BSW/*.S) $(wildcard ./*.cpp)
OBJS := $(addprefix obj/,$(notdir $(SRCS:.cpp=.o)))
OBJS := $(OBJS:.c=.o)
OBJS := $(OBJS:.S=.o)

VPATH = ./:./src:./src/*:./src/*/*:./BSW

all: obj/micro_mouse.elf obj/micro_mouse.mot obj/micro_mouse.siz

obj/micro_mouse.elf: $(OBJS)
	$(CP) -O0 -ffunction-sections -fdata-sections -fdiagnostics-parseable-fixits -Wstack-usage=100 -g2 -mcpu=rx600 -misa=v1 -mlittle-endian-data -o "obj/micro_mouse.elf" $(OBJS) $(USER_OBJS) $(LIBS) -T $(LINKER) -Wl,--start-group -lm -lc -lgcc -lstdc++ -Wl,--end-group -nostartfiles -Wl,-e_PowerON_Reset -Wl,-M=obj/micro_mouse.map

obj/micro_mouse.mot: obj/micro_mouse.elf
	rx-elf-objcopy "obj/micro_mouse.elf" -O srec -I elf32-rx-be-ns  "obj/micro_mouse.mot"

obj/micro_mouse.siz: obj/micro_mouse.elf
	rx-elf-size --format=berkeley "obj/micro_mouse.elf"

obj/%.o: %.cpp
	$(CP) -O0 -ffunction-sections -fdata-sections -fdiagnostics-parseable-fixits -Wstack-usage=100 -g2 -mcpu=rx600 -misa=v1 -mlittle-endian-data -I./BSW -DCPPAPP -Wa,-adlnh="obj/micro_mouse.lst" -MMD -MP -MF"obj/$*.d" -MT"obj/$*.o"  -c "$<" -o "$@"

obj/%.o: %.c
	$(CC) -O0 -ffunction-sections -fdata-sections -fdiagnostics-parseable-fixits -Wstack-usage=100 -g2 -mcpu=rx600 -misa=v1 -mlittle-endian-data -I./BSW -Wa,-adlnh="obj/hwinit.lst" -MMD -MP -MF"obj/$*.d" -MT"obj/$*.o" "$<" -c -o "$@"

obj/%.o: %.S
	$(CC) -O0 -ffunction-sections -fdata-sections -fdiagnostics-parseable-fixits -Wstack-usage=100 -g2 -mcpu=rx600 -misa=v1 -mlittle-endian-data -x assembler-with-cpp -Wa,--gdwarf2 -DCPPAPP -I./BSW -Wa,-adlhn="obj/start.lst" -MMD -MP -MF"obj/$*.d" -MT"obj/$*.o" -c "$<" -o "$@"

.PHONY: clean
clean:
	-rm obj/*.d
	-rm obj/*.o
	-rm obj/*.lst
	-rm obj/micro_mouse.elf
	-rm obj/micro_mouse.map
	-rm obj/micro_mouse.mot