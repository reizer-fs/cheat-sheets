See Also: <?add topic='sed'?>

### Matching

Match lines

    awk '$1 ~ /pattern/ { ... }' infile

Matching for Conditions

    awk '{if($1 ~ /pattern) { ... }}' infile

### Advanced Use of awk

#### Negative Indizes

    awk '{print $(NF - 1)}'
AWK one-liner collection
I love perl and I use it for most scripts but nothing beats awk on the commandline. AWK is a pattern matching and string processing language named after the surnames of the original authors: Alfred Aho, Peter Weinberger and Brian Kernighan.
Print selected fields
Split up the lines of the file file.txt with ":" (colon) separated fields and print the second field ($2) of each line:
 awk  -F":" '{print $2}' file.txt

Same as above but print only output if the second field ($2) exists and is not empty:
 awk  -F":" '{if ($2)print $2}' file.txt

Print selected fields from each line separated by a dash:
 awk -F: '{ print $1 "-" $4 "-" $6 }' file.txt

Print the last field in each line:
 awk -F: '{ print $NF }' file.txt

Print every line and delete the second field:
 awk '{ $2 = ""; print }' file.txt

Good to know:
The commandline option -F sets the field separator. The default is space.
$0 the entire line without the newline at the end
$1 to $9, $10 to ..., the fields
NF number of fields
NR currant line number (counting across all files for multiple files)
FNR line number (just for that file)
Print matching lines
Print field number two ($2) only on lines matching "some regexp" (fiel separator is ":"):
 awk  -F":" '/some regexp/{print $2}' file.txt

Print lines matching "regexp a" and lines matching "regexp b" but the later ones are printed without newline (note the printf):
 awk  '/regexp a/{print};/regexp b/{printf $0}' file.txt

Print field number two ($2) only on lines not matching "some regexp" (fiel separator is ":"):
 awk  -F":" '!/some regexp/{print $2}' file.txt
or
 awk  -F":" '/some regexp/{next;}{print $2}' file.txt 

Print field number two ($2) only on lines matching "some regexp" otherwise print field number three ($3) (fiel separator is ":"):
 awk  -F":" '/some regexp/{print $2;next}{print $3}' file.txt
The "next" command causes awk to continue with the next line and execute "{print $3}" only for non matching lines. This is like 
/regexp/{...if..regexp..matches...;next}{...else...} 

Print lines where field number two matches regexp (apply regexp only to field 2, not the whole line):
 awk '$2 ~ /regexp/{print;}' file.txt

Print the next two (i=2) lines after the line matching regexp:
 awk '/regexp/{i=2;next;}{if(i){i--; print;}}' file.txt

Print the line and the next two (i=2) lines after the line matching regexp:
 awk '/regexp/{i=2+1;}{if(i){i--; print;}}' file.txt

Print the lines from a file starting at the line matching "start" until the line matching "stop":
 awk '/start/,/stop/' file.txt

Print fields 1 and 2 from all lines not matching regexp:
 awk '!/regexp/{print $1 " " $2 }' file.txt

Print fields 1 and 2 from lines matching regexp1 and not matching regexp2:
 awk '/regexp1/&&!/regexp2/{print $1 " " $2 }' file.txt


Regexp syntax:
c matches the non-metacharacter c.
\c matches the literal character c.
. matches any character including newline.
^ matches the beginning of a string (example: ^1 , only lines starting with a one)
$ matches the end of a string (example: end$ , only lines ending in "end")
[abc...] character list, matches any of the characters abc....
[0-9a-zA-Z] range of characters 0-9 and a-z,A-Z
[^abc...] negated character list, matches any character except abc....
r1|r2 alternation: matches either r1 or r2.
r1r2 concatenation: matches r1, and then r2.
r+ matches one or more r's.
r* matches zero or more r's.
r? matches zero or one r's.
(r) grouping: matches r.
Insert string after the matching line
This inserts a new line after the matching line:
awk '/regexp/{print $0; print "text inserted after matching line";next}{print}' file.txt
$0 is the line where the search pattern "regexp" matches without the newline at the end. The awk print command prints the string and appends a new line. 

This appends a string to the matching line:
awk '/regexp/{print $0 "text appended at end of the matching line";next}{print}' file.txt
If matching "do A" else "do B" (if .. then .. else in awk)
awk '/regexp/{A-here;next}{B-here}' file.txt
Example:
awk '/regexp/{gsub(/string/,"replacement");print $1;next}{print;}' file.txt
The example would print lines that do not match unchanged (action B is just "print;") while on lines that match /regexp/ it would replace /string/ by replacement and print the first element ($1). 
Replacement for some common unix commands (useful in a non unix environment)
Count lines (wc -l):
 awk 'END{print NR}'

Search for matching lines (egrep regexp):
 awk '/regexp/'

Print non matching lines (egrep -v regexp):
 awk '!/regexp/'

Print matching lines with numbers (egrep -n regexp):
 awk '/regexp/{print FNR,$0}'

Print matching lines and ignore case (egrep -i regexp):
 awk 'BEGIN {IGNORECASE=1};/regexp/'

Number lines (cat -n):
 awk '{print FNR "\t" $0}'

Remove duplicate consecutive lines (uniq):
 awk 'a !~ $0{print}; {a=$0}'

Print first 5 lines of file (head -5):
 awk 'NR < 6'
Number non empty lines
This prints all lines and adds a line number to non empty lines:
 awk '/^..*$/{ print FNR ":" $0 ;next}{print}' file.txt
Number lines longer than 80 char and show them
This is useful to find all the lines longer than 80 characters (or any other length):
 awk 'length($0)>80{print FNR,$0}'  file.txt
Substitute foo for bar on lines matching regexp
 awk '/regexp/{gsub(/foo/, "bar")};{print}' file.txt
Delete trailing white space (spaces, tabs)
 awk '{sub(/[ \t]*$/, "");print}' file.txt
Delete leading white space
 awk '{sub(/^[ \t]+/, ""); print}' file.txt
Add some characters at the beginning of matching lines
Add ++++ at lines matching regexp.
 awk '/regexp/{sub(/^/, "++++"); print;next;}{print}' file.txt
Color gcc warnings in red
 gcc -Wall main.c |& awk '/: warning:/{print "\x1B[01;31m" $0 "\x1B[m";next;}{print}'
The "\x1B" means the ascii character with hex number 1B (ESC).
Print only lines of less than 80 characters
 awk 'length < 80' file.txt
Renaming files with AWK
You can use awk to generate shell commands such as e.g mv commands to rename files according to a given recipe. I suggest to always print the commands before piping them to sh in order to execute them. A small typo can have very significant side effect so double check what would happen by printing the commands first. 

Rename all .MP3 file to be lower case:
 ls *.MP3 | awk '{ printf("mv \"%s\" \"%s\"\n", $0, tolower($0)) }'
The above will just print what would happen. To actually execute it you run:
 ls *.MP3 | awk '{ printf("mv \"%s\" \"%s\"\n", $0, tolower($0)) }' | sh

Substitute a regexp pattern with a given replacement string. We can e.g replace " " (spaces in the file names) by "-":
 ls | awk '{ printf("mv \"%s\" \"%s\"\n", $0, gensub(/ +/,"-","g")) }'
The above will just print what would happen. To actually execute it you run:
 ls | awk '{ printf("mv \"%s\" \"%s\"\n", $0, gensub(/ +/,"-","g")) }' | sh
The gensub function reads the strings from $0 (=current line) and returns the modified string. The third argument, the "g", means to find and replace everywhere (globally) on the current line.
AWK as a command-line calculator
This prints 5.1:
 awk 'BEGIN{print 3.1+4/2}'

This prints 1.41421:
 awk 'BEGIN{print sqrt(2)}'

This prints 1.41421:
 awk 'BEGIN{print 2^(1/2)}'

This prints 3.141592653589793 (PI with a 15 digits behind the decimal point):
 awk 'BEGIN{printf "%.15f\n",4*atan2(1,1)}'

Print decimal number as hex (this prints 0x20):
 awk 'BEGIN{printf "0x%x\n", 32}'

Convert hex string to decimal (this prints 32):
 awk 'BEGIN{print strtonum(0x20)}'

Math operators in gnu awk: 
+ - * /
^ or ** Exponentiation
% Modulo
exp(), log() Exponential function and natural logarithm
atan2(y, x), sin(), cos() work all in radians (fraction of PI)
sqrt() same as **(1/2) Square root
strtonum() Convert hex (start with 0x) and octal (start with 0) to decimal
If you want to use this frequently then you could put this into your .bashrc file:
# add the awc function to .basrc
# use awc like this: awc "3.4+2+8+99.2" (do not forget the quotes)
awc(){ awk "BEGIN{ print $* }" ;}
On the shell you can then type awc "3.4+2+8+99.2" and it will print 112.6.

AWK minimal web server
You can't write a web server as a reasonable one-liner in AWK, you can do that with netcat but there are some cases where you don't have a real web server and you don't have netcat but you have a very basic shell environment and that does usally include gawk (note: you need gawk, gnu version of awk). Here is a web server that allows you to serve files at port 8080 (or any port, just change the number): 

#!/usr/bin/gawk -f
BEGIN {
if (ARGC < 2) { print "Usage: wwwawk  file.html"; exit 0 }
	Concnt = 1;
        while (1) {
        RS = ORS = "\r\n";
        HttpService = "/inet/tcp/8080/0/0";
        getline Dat < ARGV[1];
        Datlen = length(Dat) + length(ORS);
        while (HttpService |& getline ){
		if (ERRNO) { print "Connection error: " ERRNO; exit 1}
                print "client: " $0;
                if ( length($0) < 1 ) break;
        }
        print "HTTP/1.1 200 OK"             |& HttpService;
        print "Content-Type: text/html"     |& HttpService;
        print "Server: wwwawk/1.0"          |& HttpService;
        print "Connection: close"           |& HttpService;
        print "Content-Length: " Datlen ORS |& HttpService;
        print Dat                           |& HttpService;
        close(HttpService);
        print "OK: served file " ARGV[1] ", count " Concnt;
        Concnt++;
      }
} 
Copy this code and save it into a file called wwwawk and then make it executable with "chmod 755 wwwawk". Now take some file (e.g somefile.html) and you can serve it via that little web server: 
chmod 755 wwwawk
./wwwawk somefile.html

from another shell:
curl http://localhost:8080
 or
lynx http://localhost:8080
 or
firefox -new-tab http://localhost:8080
This is e.g. a great way to serve kickstart files for automated Linux installations. Note that this awk web server requires gawk. Most linux distributions use gawk by default except for raspberry pi which uses mawk and mawk does not support network connections. 
