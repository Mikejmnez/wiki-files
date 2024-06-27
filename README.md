# wiki-files

Migrating documents from MediaWiki to Markdown.

## Steps I followed:

### Exporting files from wiki docs 

1. MediaWiki (or wiki docs) -> Special Pages -> 'All Pages'
2. With help from the filter tool at the top of 'All Pages', copy the page names to convert into a text file (one file name per line).
3. On a separate tab, MediaWiki (wiki docs) -> Special Pages -> 'Export'
4. Paste the list of pages into the Export field. NOTE: had to do this for each individual page.
5. Check: 'Include only the current revision, not the full history'.
6. Uncheck: Include Templates
7. Check: Save as file
8. Click on the 'Export' button.
9. An `XML` file will be saved locally on `Downloads`


### Transforming xml to markdown

Here, I make use of [mediawiki-to-gfm](https://github.com/outofcontrol/mediawiki-to-gfm?tab=readme-ov-file). In particular, on the local directory where all xml files are located I run:

```
docker run -v $PWD:/app oooc/mediawiki-to-gfm --filename=file_name.xml
```

This makes a new markdown file named `file_name.md`. It contains a copy of the contents of `file_name.xml`. The markdown document will be located in a folder `output` in the same directory as where the code above is being run. In the example above, then, you would find the following file structure:
```
file_name.xml
output/file_name.md
```


### Troubleshooting

Not all files were successfully copied into markdown, and in some cases, an xml file was turned into a folder with a markdown inside it. 

1. Case: folder creation.
Example: An xml file called `DAP4:_Aggregg.xml`. Then executing `docker run -v ...` on the command line produced:
```
output/DAP4/Aggregg.md
```

2. Case: error and no markdown file creation.

This happened with a few files. For each case I created an issue explaining the error message and the file that lead to the error. 


