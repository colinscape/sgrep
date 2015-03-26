## Introduction

Sgrep (sorted grep) searches sorted input files for lines that match a search
key and outputs the matching lines. When searching large files sgrep is much
faster than traditional Unix grep, but with significant restrictions.

  * All input files must be sorted regular files. 
  * The sort key must start at the beginning of the line. 
  * The search key matches only at the beginning of the line. 
  * No regular expression support. 

Sgrep uses a binary search algorithm, which is very fast, but requires sorted
input. Each iteration of the search eliminates half of the remaining input. In
other words, doubling the size of the file adds just one iteration.

## Syntax

```
sgrep [ -i | -n ] [ -c ] [ -b ] [ -r ] [ -- ] key [ sorted_file ... ]
```

## Command Arguments

-i (or -f) 
     By default sgrep uses exact byte comparison. The -i flag (or -f) causes sgrep to use case insensitive byte comparison. For example **aa** matches **AA** or **aA**, etc. The input file must be sorted with **sort -f**. 
-n 
     The -n flag causes sgrep to use numeric comparison. The input file must be sorted with **sort -n**. Each line of the file should begin with optional white space and a numeric text string such as **055** or **-27** or **73.57**. Sgrep compares the floating point value of the numeric string with the floating point value of the search key. For example, the search key **10** matches **10.0**, **010**, **010.00**, etc. Non numeric text strings have the value zero. The numeric search key may optionally be followed by a colon and a second number, which specifies a range for the key, such as **10.0:10.999**. 
-b 
     The -b flag causes sgrep to ignore leading white space at the beginning of a line and at the beginning of the search key. The input file must be sorted with **sort -b**. 
-c 
     By default sgrep outputs all lines where the search key matches the beginning of the line. The -c flag causes sgrep to output the number of matching lines. 
-r 
     The -r flag specifies that the input file is sorted in reverse (descending) order using **sort -r**. 
-- 
     The -- flag terminates the option flags and the next argument is used as the search key. You need to use -- if the search key begins with **-**, such as **sgrep -n -- -25 file**

If no input files are specified, then sgrep uses stdin which must be a sorted
regular file. You cannot pipe into sgrep.

## Locale Issues

Sgrep compares bytes using the native hardware byte order, so the file must be
sorted accordingly. You should set the environment variable **LC_ALL** to
**C** before running sort or sgrep. For example,

    
``` 
    export LC_ALL=C
    sort -o sorted unsorted
    sgrep key sorted
```

## Applications

Sgrep offers negligible speed improvement over traditional grep on files
smaller than 100 KBytes, is measurably faster on files larger than a few
MBytes, and is dramatically faster on files larger than 1 GByte.

Sgrep searches large sorted files very efficiently. But the overhead of
sorting a very large file is considerable, so sgrep is most useful in
situations where many searches are performed on the same file.

Some files, such as system logs, are naturally output in chronological order.
If the date is output at the beginning of the line in a format such as
**YYYY/MM/DD:hh:mm:ss**, then the file is already sorted and suitable for
quick search by date using sgrep.

## Author

Stephen C. Losen, University of Virginia

