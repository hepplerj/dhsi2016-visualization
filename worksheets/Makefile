RMD_FILES := $(patsubst %.Rmd, %.html, $(wildcard *.Rmd))
INCLUDE_FILES := $(wildcard includes/*.html)

all : $(RMD_FILES)

%.html : %.Rmd $(INCLUDE_FILES)
	R --slave -e "set.seed(100);rmarkdown::render('$(<F)')"

.PHONY : deploy
deploy :
	rsync --progress --delete -avz \
		--exclude='.git' \
		* reclaim:~/public_html/lincolnmullen.com/projects/worksheets/

.PHONY : clean
clean :
	rm -f $(RMD_FILES)
	rm -rf $(DATA)
	rm -rf libs/*
	rm -rf *_files/
