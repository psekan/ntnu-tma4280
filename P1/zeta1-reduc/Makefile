CC=mpicc
CFLAGS=-O2 -std=c99
LIBS=-lm
TARGET=pi
MAINSRC=main.c
NP?=5
RDIR=../../R

SRC=$(wildcard ../zeta1/computation/*.c)
OBJ=$(patsubst %.c, %.o, $(SRC))

%.o: %.c
	$(CC) $(CFLAGS) $(LIBS) -c $< -o $@

$(TARGET): $(OBJ) $(MAINOBJ)
	$(CC) -o $(TARGET) -Wall $(OBJ) $(MAINSRC) $(LIBS)

all: $(TARGET)

test: all
	mpirun -n $(NP) ./$(TARGET) 

results/error.csv:
	mkdir -p results
	echo "n;error" > "results/error.csv"
	for number in 1 2 4 8 16 32 64 128 256 512 1024 2048 4096 8192 16384 32768 ; do \
		echo $$number | mpirun -n $(NP) ./$(TARGET) \
			| grep "Error;" | sed "s/Error/$$number/" >> "results/error.csv" ; \
	done

results/wall-time.csv:
	mkdir -p results
	echo "np;wall time" > "results/wall-time.csv"
	for number in 2 3 5 9 17 33; do \
	  echo 16777216 | mpirun -n $$number ./$(TARGET) \
	    | grep "Wall time;" | sed "s/Wall time/$$number/" >> "results/wall-time.csv" ; \
	done

raw-result: all results/error.csv results/wall-time.csv

plot: raw-result
	Rscript "$(RDIR)/plot.r" results"/error.csv" "results/error.png" "Error plot" "n" "error"
	Rscript "$(RDIR)/plot.r" results"/wall-time.csv" "results/wall-time.png" "Wall time" "processes" "wall time"

clean:
	rm -f $(TARGET) $(OBJ)
