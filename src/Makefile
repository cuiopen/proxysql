



DEPS_PATH=../deps

MARIADB_PATH=$(DEPS_PATH)/mariadb-client-library/mariadb_client
MARIADB_IDIR=$(MARIADB_PATH)/include
MARIADB_LDIR=$(MARIADB_PATH)/libmariadb


DAEMONPATH=$(DEPS_PATH)/libdaemon/libdaemon
DAEMONPATH_IDIR=$(DAEMONPATH)
DAEMONPATH_LDIR=$(DAEMONPATH)/libdaemon/.libs

JEMALLOC_PATH=$(DEPS_PATH)/jemalloc/jemalloc
JEMALLOC_IDIR=$(JEMALLOC_PATH)/include/jemalloc
JEMALLOC_LDIR=$(JEMALLOC_PATH)/lib

LIBCONFIG_PATH=$(DEPS_PATH)/libconfig/libconfig-1.4.9
LIBCONFIG_IDIR=-I$(LIBCONFIG_PATH)/lib
LIBCONFIG_LDIR=-L$(LIBCONFIG_PATH)/lib/.libs

RE2_PATH=$(DEPS_PATH)/re2/re2
RE2_IDIR=$(RE2_PATH)

PCRE_PATH=$(DEPS_PATH)/pcre/pcre
PCRE_LDIR=$(PCRE_PATH)/.libs

SQLITE3_DIR=$(DEPS_PATH)/sqlite3/sqlite3

IDIR=../include
LDIR=../lib
IDIRS=-I$(IDIR) -I$(JEMALLOC_IDIR) -I$(MARIADB_IDIR) $(LIBCONFIG_IDIR) -I$(DAEMONPATH_IDIR) -I$(SQLITE3_DIR)
LDIRS=-L$(LDIR) -L$(JEMALLOC_LDIR) $(LIBCONFIG_LDIR) -L$(RE2_PATH)/obj -L$(MARIADB_LDIR) -L$(DAEMONPATH_LDIR) -L$(PCRE_LDIR)

UNAME_S := $(shell uname -s)

ifeq ($(UNAME_S),Darwin)
	IDIRS+= -I/usr/local/include
	LDIRS+= -L/usr/local/lib
endif

MYCXXFLAGS=-std=c++11 $(IDIRS) $(OPTZ) $(DEBUG)
LDFLAGS+=
NOJEMALLOC := $(shell echo $(NOJEMALLOC))
ifeq ($(NOJEMALLOC),1)
MYLIBS=-Wl,--export-dynamic -Wl,-Bstatic -lconfig -lproxysql -ldaemon -lconfig++ -lre2 -lpcrecpp -lpcre -lmariadbclient -Wl,-Bdynamic -lpthread -lm -lz -lrt -lcrypto -lssl $(EXTRALINK)
else
MYLIBS=-Wl,--export-dynamic -Wl,-Bstatic -lconfig -lproxysql -ldaemon -ljemalloc -lconfig++ -lre2 -lpcrecpp -lpcre -lmariadbclient -Wl,-Bdynamic -lpthread -lm -lz -lrt -lcrypto -lssl $(EXTRALINK)
endif

ifeq ($(UNAME_S),Darwin)
	MYLIBS=-lssl -lre2 -lmariadbclient -lpthread -lm -lz -liconv -lcrypto
endif
ifeq ($(UNAME_S),Linux)
	MYLIBS+= -ldl
endif
ifeq ($(UNAME_S),FreeBSD)
	MYLIBS+= -lexecinfo
endif

LIBPROXYSQLAR=$(LDIR)/libproxysql.a
ifeq ($(UNAME_S),Darwin)
	LIBPROXYSQLAR=$(LDIR)/libproxysql.a ../deps/pcre/pcre/.libs/libpcre.a ../deps/pcre/pcre/.libs/libpcrecpp.a  ../deps/libdaemon/libdaemon/libdaemon/.libs/libdaemon.a  ../deps/libconfig/libconfig/lib/.libs/libconfig++.a ../deps/libconfig/libconfig/lib/.libs/libconfig.a ../deps/sqlite3/sqlite3/sqlite3.o
endif

ODIR= obj

EXECUTABLE=proxysql

_OBJ = main.o proxysql_global.o
OBJ = $(patsubst %,$(ODIR)/%,$(_OBJ))

$(ODIR)/%.o: %.cpp
	$(CXX) -c -o $@ $< $(MYCXXFLAGS) $(CXXFLAGS) -Wall

proxysql: $(ODIR) $(OBJ) $(LIBPROXYSQLAR)
	$(CXX) -o $@ $(OBJ) $(LIBPROXYSQLAR) $(MYCXXFLAGS) $(CXXFLAGS) $(LDIRS) $(LIBS) $(LDFLAGS) $(MYLIBS)

$(ODIR):
	mkdir $(ODIR)

$(LIBPROXYSQLAR):
	cd $(LDIR) && ${MAKE}

default: $(EXECUTABLE)

clean:
	rm -f *.pid $(ODIR)/*.o *~ core $(EXECUTABLE)

