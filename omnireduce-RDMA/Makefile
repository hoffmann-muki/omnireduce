ifeq ($(OMNIREDUCE_PATH),)
OMNIREDUCE_PATH = $(shell pwd)
export OMNIREDUCE_PATH
endif

SOURCEDIR  := ${OMNIREDUCE_PATH}/omnireduce
DESTDIR  := ${OMNIREDUCE_PATH}/build

CXXFLAGS  := -O3 -std=c++11

# Base include for repo
INCLUDE  := -I ${OMNIREDUCE_PATH}

# If a conda prefix is available, add its include and lib paths
ifdef CONDA_PREFIX
	INCLUDE += -I ${CONDA_PREFIX}/include
	LDFLAGS  := -shared -lstdc++ -L${CONDA_PREFIX}/lib -Xlinker -rpath -Xlinker ${CONDA_PREFIX}/lib
	LDLIBS  := -lverbs -lboost_system -lboost_thread -lboost_chrono -lboost_program_options
else
	LDFLAGS  := -shared -lstdc++
	LDLIBS  := -lverbs -lboost_system -lboost_thread -lboost_chrono -lboost_program_options
endif
ifeq ($(USE_CUDA),ON)
$(info "USE_CUDA ON")
CXXFLAGS += -DUSE_CUDA --compiler-options -fPIC 
CC  := nvcc
LD  := nvcc
else
CXXFLAGS += -fPIC
CC  := g++
LD  := g++
endif

SOURCE:=${wildcard ${SOURCEDIR}/*.cpp}
OBJS:=${patsubst ${SOURCEDIR}/%.cpp,${SOURCEDIR}/%.o,${SOURCE}}

ifeq ($(USE_CUDA),ON)
SOURCE:=${wildcard ${SOURCEDIR}/*.cu}
OBJS+=${patsubst ${SOURCEDIR}/%.cu,${SOURCEDIR}/%.o,${SOURCE}}
endif

TARGET_LIB  := libomnireduce.so

all:${OBJS}
	${LD} ${LDFLAGS} -o ${SOURCEDIR}/${TARGET_LIB} ${OBJS} ${LDLIBS}
	mkdir -p ${DESTDIR}/include/omnireduce
	cp ${SOURCEDIR}/${TARGET_LIB} ${DESTDIR}
	cp ${SOURCEDIR}/*.hpp ${DESTDIR}/include/omnireduce

${SOURCEDIR}/%.o:${SOURCEDIR}/%.cpp
	${CC} -c ${CXXFLAGS} $< -o ${SOURCEDIR}/$*.o ${INCLUDE}

${SOURCEDIR}/%.o:${SOURCEDIR}/%.cu
	${CC} -c ${CXXFLAGS} $< -o ${SOURCEDIR}/$*.o ${INCLUDE}

.PHONY: clean

clean:
	rm ${SOURCEDIR}/*.so ${SOURCEDIR}/*.o -rf
	rm -rf ${DESTDIR}
