WGET = /usr/bin/wget --timestamping --no-verbose

OBO = http://purl.obolibrary.org/obo
MONARCH = https://archive.monarchinitiative.org/latest/rdf

OBO_SOURCES = mondo.owl \
	      hp.owl \
	      chebi.owl

MONARCH_SOURCES = reactome.ttl

ALL_SOURCES = $(OBO_SOURCES) $(MONARCH_SOURCES)
CSVS = $(ALL_SOURCES:=.csv)

all: $(ALL_SOURCES) build/robot.jar $(CSVS) all-synonyms.csv

build/:
	mkdir -p build

build/robot.jar: | build/
	curl -L -o build/robot.jar \
		https://build.berkeleybop.org/job/robot/lastSuccessfulBuild/artifact/bin/robot.jar

ROBOT := java -jar build/robot.jar

sources/:
	mkdir --parents $@

$(OBO_SOURCES): | sources/
	cd sources && $(WGET) $(OBO)/$@

$(MONARCH_SOURCES): | sources/
	cd sources && $(WGET) $(MONARCH)/$@

$(CSVS): $(ALL_SOURCES) build/robot.jar
	$(ROBOT) \
		filter --input sources/$(patsubst %.csv,%,$@) --axioms annotation \
	        query -f csv --query ./sparql/synonyms.sparql $@

all-synonyms.csv: $(CSVS)
	cat *.csv | sort -u >all-synonyms.csv

