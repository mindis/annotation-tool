# annotation-tool

A lightweight web-based annotation tool for labelling entity recognition data.

The tool was designed with rapid annotation in mind. Unlike other annotation tools, this one is keyboard-based, allowing users to tag entities much faster than with the mouse. 

## Dependencies

- Python 2.7
	- [NLTK](http://www.nltk.org/) (optional but highly recommended)
	- [Colorama](https://pypi.python.org/pypi/colorama) (optional)
- Google Chrome or Mozilla Firefox (latest versions recommended. Tested in September 2017).

## How to use

There are three steps to using this annotation tool:

1. Generating the Javascript file for use by the web-based interface.
2. Annotating the data through the web-based interface.
3. Merging the annotated data into one file.

### 1. Generating the Javascript file

The web-based interface uses a Javascript file, `json_data.js`, to load in the sentences and entity categories. Before using the web-based interface, you must first generate this file.

To begin, you'll need to place two files in the root directory of this repository:

1. `raw_data.txt`, which contains all of the sentences in your dataset, separated by newline characters, and
2. `entity_classes.txt`, which contains your entity categories. Each line comprises a category name, followed by a tab character, followed by the abbreviated form of that category.

Note: at this stage, up to 10 entity classes are supported.

Examples of `raw_data.txt` and `entity_classes.txt` can be found in the `examples` folder. The example dataset was taken from the Gutenberg corpus, found in the NLTK library.

Once you've placed the two files into the root directory, you may run the following to generate the `json_data.js` file:

    python generate_js.py
    
This script will tokenise all of your data using NLTK, and will convert all of the data into Javascript arrays.
    
### 2. Annotating the data through the web-based interface

You may now successfully open `tagging.html` in your browser.

Before commencing with the annotation, you may wish to change your download directory to a new folder, as the `annotated_data.txt` files generated by the tool will fill your download directory as you annotate your data.

The sentences are separated into groups of 10. Whenever you complete the annotation of one group, a text file such as `annotated_data_n` (where `n` is the group) will be saved to your download directory. This way, you may close the webpage at any time and resume annotating later.

You can also manually save the current group by pressing the Save button in the top-right.

Note: I've found Chrome to be faster than Firefox for this application as it doesn't ask for confirmation before downloading each file.

#### Controls

- Left/right arrow: Select previous/next token
- Up/down arrow: Select previous/next sentence
- Shift + left/right arrow: Select multiple tokens at once
- 1-9, 0: Tag token with the corresponding class
- j: Jump to group

### 3. Merging the annotated data into one file

Once you've completed annotating, there will be a bunch of `annotated_data.txt` files in your download directory. Create a folder named `annotated_data` in the root directory of this repository, and copy/paste all of the annotated data files into it.

You can then run the `combine_data.py` program to combine them all into one file:

    python combine_data.py
    
This program will only merge the most recent versions of each annotated file, so if you tagged the same group twice (and have something like `annotated_data_1.txt` and `annotated_data_1 (1).txt`), it will place the old versions into a folder named `old_versions`.

The program will also notify you if there are any gaps between annotated data files. For example, if you are missing `annotated_data_4.txt` but have an `annotated_data_5.txt`, the program will let you know.

Once the program has finished, you can find your merged file inside the `annotated_data/merged_data` folder. The format of each line in the file is a token, followed by a space, followed by its entity class (in the BIO style). This mirrors the format used in the CoNLL2003 dataset. For example, tagging the sentence "John bought 300 shares of Acme Corp in 2006" would result in:

    Jim B-PER
    bought O
    300 O
    shares O
    of O
    Acme B-ORG
    Corp I-ORG
    in O
    2006 B-TIM

## Possible FAQs

- **How is this quicker than BRAT, for example?** There's no need to use the mouse here. You don't need to ensure you begin dragging your mouse at the exact right spot, and there are no menus to navigate. There are also no AJAX requests - everything is done client-side, so the transition between records is faster. It's also much quicker to set up.
- **Why can't I tag punctuation?** I tried to make the annotation task as fast as possible. Tokens that comprise solely punctuation characters are almost never entities, so discluding them as taggable entities helps speed up annotation.
- **How do I use already-tokenized data?** At this stage, you can't. Though you could turn your tokenised data into JSON, copying the format of the `json_data.js` file.
- **Is there any ability to label links between entities?** Not yet, no. This tool is purely for generating entity recognition data, not entity linking.
- **Do I need NLTK or Colorama?** NLTK is highly recommended, as without it the `generate_js.py` program will "tokenise" your data using space characters. Colorama is completely optional - it just makes the terminal output a little nicer when running `combine_data.py`.
