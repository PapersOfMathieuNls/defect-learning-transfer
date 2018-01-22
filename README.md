# Combined

## Collaborate

Edit the combined.md file using whichever text editor you want (Vim, Sublime, Word). 
You can even edit it online, directly on github: https://github.com/PapersOfMathieuNls/defect-learning-transfert/edit/master/combined.md

The only thing that matters is to save it in text format.

## Build the pdf

The pdf generation is based on [Pandoc](http://pandoc.org/). Pandoc transforms the markdown to latex and then, to pdf. 
Here's the command, assuming pandoc, latex and pandoc-citeproc are installed on your system and that you are on this directory :

```bash
./build.sh
```

## Docker

If you don't wish to install of the dependencies, you can use this docker container that is ready to go: https://hub.docker.com/r/mathieunls/latex/.