## sed & awk, 2nd Edition
> Arnold Robbins

### Preface

This book is about a set of oddly named UNIX utilities, sed and awk. These utilities have many things in common, including the use of regular expressions for pattern matching. 

we will be covering all three programs, although the focus is on sed and awk.


is an overview of the features and capabilities of sed and awk.


This chapter covers the basic elements of writing a sed script using only a few sed commands.

This chapter presents the primary features of this scripting language. A number of scripts are explained, including one that modifies the output of the ls command.

### Obtaining Example Source Code

You can obtain the source code for the programs presented in this book from O'Reilly & Associates through their Internet server. 

To use FTP, you need a machine with direct access to the Internet. 

### Conventions Used in This Handbook

Conventions Used in This Handbook
The following conventions are used in this book:

□indicates a literal space. This symbol is used to make spaces visible in examples, as well as in the text.

•indicates a literal TAB character. This symbol is used to make tabs visible in examples, as well as in the text.


### 1. Power Tools for Editing

 The speed and efficiency provided by power tools would be essential to being productive. [D.D.]


 However, using sed and awk can save many hours of repetitive work in achieving the same result.

Then you can devise a solution that can be reused in other situations.


When the formatting software processes the document, the references are properly resolved and expanded.


The problem we faced was that we had to use the same files to create an online version of the book.

There were three possible ways to solve this problem:


### 1.2. A Stream Editor

Using sed is similar to writing simple shell scripts (or batch files in DOS). You specify a series of actions to be performed in sequence. 

Sed can also be used effectively to edit very large files that would be slow to edit interactively.

Sed also has the ability to be used as an editing filter. In other words, you could process an input file and send the output to another program. For instance, you could use sed to analyze a plain text file 

To automate editing actions to be performed on one or more files.


To simplify the task of performing the same edits on multiple files.

### 1.3. A Pattern-Matching Programming Language

A typical example of an awk program is one that transforms data into a formatted report. 

▪  Generate formatted reports.

▪  Process the result of UNIX commands.

The capabilities of awk extend the idea of text editing into computation, making it possible to perform a variety of data processing tasks, including analysis, extraction, and reporting of data. These are, indeed, the most common uses of awk but there are also many unusual applications

### 1.4. Four Hurdles to Mastering sed and awk

The goal of this book is to take you much further—to help you master sed and awk and to reduce the amount of time and effort that it takes you to reach that goal.

There are four hurdles on the way to mastering sed and awk. 

While not directly related to sed and awk themselves, managing the interaction with the command shell is often a frustrating problem, since the shell shares a number of special characters with both programs. 

### 2. Understanding Basic Operations

you can benefit from looking at how much they have in common.

▪  They are invoked using similar syntax.


▪  They use regular expressions for pattern matching.
▪  They allow the user to specify instructions in a script.

One reason they have so much in common is that their origins can be found in the same line editor, ed. 

Don't worry—this is an exercise intended to help you learn sed and awk, not an attempt to convince you of the wonders of line editors. 

(If you're already familiar with ed, feel free to skip to the next section.)

You can enter the print command, p, to display the current line.

By default, a command affects only the current line. To make an edit, you move to the line that you want to edit and then apply the command. To move to a line, you specify its address. 

You can also specify a regular expression as an address. To delete a line containing the word "regular," you could issue this command:


To delete all the lines that contain the regular expression, you'd prefix the command with the letter g for global.


No address is specified, so it affects only the first occurrence on the current line.

To look for multiple occurrences on the same line, you must specify g as a flag: 




To make it apply to all lines, use the global command, putting g before the address.


The address and the pattern need not be the same.

One more interesting feature of ed is the ability to script your edits, placing them in a separate file and directing them as input to the line editor. 

The braces ({}) surround a series of one or more statements that are applied to the same address.


The advantage of using a programming language in scripts is that it offers many more ways to control what the programmable editor can do. Awk offers expressions, conditional statements, loops, and other programming constructs.


Although awk was designed as a programmable editor, users found that awk scripts could do a wide range of other tasks as well. 

### 2.2. Command-Line Syntax

You invoke sed and awk in much the same way. The command-line syntax is:


command [options] script filename

One option common to both sed and awk is the -f option that allows you to specify the name of a script file. 

As a script grows in size, it is convenient to place it in a file. Thus, you might invoke sed as follows:


In the upcoming sections, the examples use a sample file, named list. It contains a list of names and addresses, as shown below.


Because many of the examples in this chapter are short and interactive, you can enter them at your keyboard and verify the results.

### 2.3. Using sed


 There are two ways to invoke sed: either you specify your editing instructions on the command line or you put them in a file and supply the name of the file.


You can specify simple editing commands on the command line.

The -e option is necessary only when you supply more than one instruction on the command line. It tells sed to interpret the next argument as an instruction.

Let's look at some examples.

Three lines are affected by the instruction but all lines are displayed.

Enclosing the instruction in single quotes is not required in all cases but you should get in the habit of always doing it. The enclosing single quotes prevent the shell from interpreting special characters or spaces found in the editing instruction.

There are three ways to specify multiple instructions on the command line:

1.  Separate instructions with a semicolon. 
 



2.  Precede each instruction by -e.



.  Use the multiline entry capability of the Bourne shell.[1] Press RETURN after entering a single quote and a secondary prompt (>) will be displayed for multiline input.

In the example above, changes were made to five lines and, of course, all lines were displayed. Remember that nothing has changed in the input file.

The syntax of a sed command can be detailed, and it's easy to make a mistake or omit a required element. Notice what happens when incomplete syntax is entered:

This form, using the -f option, requires that you specify the name of the script file on the command line. 



All the editing commands that we want executed are placed in a file. We follow a convention of creating temporary script files named sedscr.


$ cat sedscr
s/ MA/, Massachusetts/
s/ PA/, Pennsylvania/
s/ CA/, California/
s/ VA/, Virginia/
s/ OK/, Oklahoma/

The following command reads all of the substitution commands in sedscr and applies them to each line in the input file list:


$ sed -f sedscr list

Do not redirect the output to the file you are editing or you will clobber it.

Here, only the lines that were affected by the command were printed.

### 2.4. Using awk

Like sed, awk executes a set of instructions for each line of input. You can specify instructions on the command line or create a script file.


Awk programs are usually placed in a file where they can be tested and modified. The syntax for invoking awk with a script file is:


awk -f script files
The -f option works the same way as it does with sed.


$0 represents the entire input line. $1, $2, ... refer to the individual fields on the input line. Awk splits the input record before the script is applied. Let's look at a few examples, using the sample input file list.


"$1" refers to the value of the first field on each input line. Because there is no pattern specified, the print statement is applied to all lines. 

The default action is to print each line that matches the pattern.

As mentioned in the first chapter, an awk program can be used more like a query language, extracting useful information from a file.

It helps to understand the above instruction if we try to read it aloud: Print the first word of each line containing the string "MA". We can say "word" because by default awk separates the input into fields using either spaces or tabs as the field separator.

In the next example, we use the -F option to change the field separator to a comma. This allows us to retrieve any of three fields: the full name, the street address, or the city and state.

Do not confuse the -F option to change the field separator with the -f option to specify the name of a script file.

Multiple commands are separated by semicolons.


In the preceding awk example, note how the leading blank is now considered part of the second and third fields.

Each implementation of awk gives you different error messages when it encounters problems in your program. 

Messages can be caused by any of the following:
▪  Not enclosing a procedure within braces ({})
▪  Not surrounding the instructions within single quotes (`')
▪  Not enclosing regular expressions within slashes (//)

The -v option for specifying parameters on the command line is discussed in Chapter 7.


### 2.5. Using sed and awk Together

In UNIX, pipes can be used to pass the output from one program as input to the next program. Let's look at a few examples that combine sed and awk to produce a report. 

While the result of this program is not very useful, it could be passed to sort | uniq -c, which would sort the states into an alphabetical list with a count of the number of occurrences of each state.


Now we are going to do something more interesting. 

 The program invokes awk to produce input for the sort program and then invokes awk again to test the sorted input and determine if the name of the state in the current record is the same as in the previous record.

The names are sorted by state. This is a typical example of using awk to generate a report from structured data.

To examine how the byState program works, let's look at each part separately.

In order to sort records by state, and not names, we insert the state as a sort key at the beginning of the record. Now the sort program can do its work for us. (Notice that using the sort utility saves us from having to write sort routines inside awk.)


significant

testing the first field of each input line to see if it contains a variable string

### 3. Understanding Regular Expression Syntax

(A square box, □, is used to make spaces visible in our examples.)


 You are probably familiar with the kinds of expressions that a calculator interprets. 

the dot (.)—that can be used as a "wildcard" to match any single character.

### 3.2. A Line-Up of Characters

2.  Writing a pattern to describe what you want to match.

3.  Testing the pattern to see what it matches.

The same syntax is used by the UNIX shell. Thus, you can use character classes to specify filenames in UNIX commands. For example, to extract headings from a group of chapter files, you might enter:

[cC]hapter [1-9]
It matches the string "chapter" or "Chapter" followed by a space and then followed by any single-digit number from 1 to 9. 

Remember that each character class matches a single character. If you specify multiple classes, you are describing multiple consecutive characters such as:

When you combine the asterisk with the wildcard metacharacter (.), you can match zero or more occurrences of any character. In the previous example, we might write a fuller regular expression as:


^\...□
This expression matches "a dot at the beginning of a line followed by any two-character string, and then followed by a space."

You might use this pattern to count the number of blank lines in a file using the count option, -c, to grep:

Similarly, you can match the entire line:


^.*$

Later, when we look at sed, you'll learn how to match patterns over multiple lines and you'll see a shell script incorporating sed that makes this capability available in a general way.

So, you can specify the minimum and maximum number of occurrences of a literal character or regular expression.

 
 
 \{ and \} are available in grep and sed.[8] POSIX egrep and POSIX awk use { and }. In any case, the braces enclose one or two arguments.


f you specify \{n,\}, then at least n occurrences will be matched. 

will match lines containing either the string "UNIX" or the string "LINUX". More than one alternative can be specified:


UNIX|LINUX|NETBSD

□book.*□
This expression is fairly simple, matching a space followed by the string "book" followed by any number of characters followed by a space

First, let's run the previous regular expression on the sample file and check the results.


All of these are problems caused by the string appearing at the beginning or end of a line. Because there is no space at the beginning or end of a line, the pattern is not matched.

For instance, to match either the beginning of a line or a space, you could write the expression:


(^| )

Lastly, we will look at several metacharacters used to describe the replacement string.

This can cause problems if what you want is to match the shortest extent possible.

between two quotation marks:


"[^"]*"
It matches "a quote followed by any number of characters that do not match a quote followed by a quote":

### 4. Writing sed Scripts

To use sed, you write a script that contains a series of editing actions and then you run the script on an input file. 

1.  Think through what you want to do before you do it.

3.  Test the procedure repeatedly before committing to any final changes.

  All editing commands in a script are applied in order to each line of input.
▪  Commands are applied to all lines (globally) unless line addressing restricts the lines affected by editing commands.


  The original input file is unchanged; the editing commands modify a copy of original input line and the copy is sent to standard output.


These scripts provide the basic models for the scripts that you will write. Although there are a number of commands available for use in sed, the scripts in this chapter purposely use only a few commands.

Sed maintains a pattern space, a workspace or temporary buffer where a single line of input is held while the editing commands are applied.

Note that the pattern for the second substitute command does not match the original input line; it matches the current line as it has changed in the pattern space.

When all the instructions have been applied, the current line is output and the next line of input is read into the pattern space. Then all the commands in the script are applied to that line.

For example, the N command reads another line into the pattern space without removing the current line, so you can test for patterns across multiple lines. 

Other commands tell sed to exit before reaching the bottom of the script or to go to a labeled command. Sed also maintains a second temporary buffer called the hold space. You can copy the contents of the pattern space to the hold space and retrieve them later.

One advantage of the one-line-at-a-time design is that sed can read very large files without any problems. Screen editors that have to read the entire file into memory, or some large portion of it, can run out of memory or be extremely slow to use in dealing with large files.

### 4.2. A Global Perspective on Addressing

A sed command can specify zero, one, or two addresses. An address can be a regular expression describing a pattern, a line number, or a line addressing symbol.

  If no address is specified, then the command is applied to each line.
▪  If there is only one address, the command is applied to any line matching the address.

▪  If two comma-separated addresses are specified, the command is performed on the first line matching the first address and all succeeding lines up to and including a line matching the second address.

When a line number is supplied as an address, the command affects only that line. For instance, the following example deletes only the first line:


1d

/^$/d
deletes only blank lines. All other lines are passed through untouched.

The following command deletes from line 50 to the last line in the file:


50,$d

Braces ({}) are used in sed to nest one address inside another or to apply multiple commands at the same address. 

The opening curly brace must end a line and the closing curly brace must be on a line by itself. Be sure there are no spaces after the braces.

### 4.3. Testing and Saving Output

1.  Makes a copy of the input line.
2.  Modifies that copy in the pattern space.
3.  Outputs the copy to standard output.

One important reason to redirect the output to a file is to verify your results. You can examine the contents of newfile and compare it to testfile. 

Be sure that the editing script is working properly before abandoning the original version.

### 4.4. Four Types of sed Scripts

2.  Remove all leading spaces from each line.

4.  Remove multiple blank spaces that were added between words.

Once this script is tested, it can be executed using runsed to process as many files as there are at once.

When we test this script, the following results are produced:



This script now produces the results that were shown earlier.
Y

### 4.5. Getting to the PromiSed Land

3.  Think Before Doing. Work carefully, testing each command that you add to a script. Compare the output against the input file to see what has changed. Prove to yourself that your script is complete. Your script may work perfectly, based on your assumptions of what is in the input file, but your assumptions may be wrong.


 If you encounter difficult situations, check and see how frequently they occur. Sometimes it's better to do a few remaining edits manually.

As you gain experience, add your own "scripting tips" to this list. You will also find that these tips apply equally well when working with awk.

### 5. Basic sed Commands

multiple sed commands can be placed on the same line if each one is separated by a semicolon.[1] The following example is syntactically correct:


n;d

However, putting a space after the n command causes a syntax error. Putting a space before the d command is okay.

### 5.3. Substitution

w fileWrite the contents of the pattern space to file. 
 


### 5.4. Delete

(In this behavior, it is the same as the next command, which you'll encounter later in this chapter.)


### 5.5. Append, Insert, and Change

The insert command places the supplied text before the current line in the pattern space. The append command places it after the current line. The change command replaces the contents of the pattern space with the supplied text.


Each of these commands requires a backslash following it to escape the first end-of-line. The text must begin on the next line. To input multiple lines of text, each successive line must end with a backslash, with the exception of the very last line.

The append and insert commands can be applied only to a single line address, not a range of lines. The change command, however, can address a range of lines. In this case, it replaces all addressed lines with a single copy of the text. 

The $ is a line-addressing symbol that matches the last line of a file. 

Note that even though only one line is output, the supplied text must start on a line by itself and cannot be on the same line as the append command.

### 5.6. List

The list command (l) displays the contents of the pattern space, showing non-printing characters as two-digit ASCII codes. 

### 5.7. Transform

This command is patterned after the UNIX tr command, which translates characters. This is a useful command in its own right; see your local documentation for details. Undoubtedly sed's y command would have been named t, if t had not already been taken (by the test command,

### 5.8. Print

Here's a sample run of the above script:

### 5.10. Next

The next command (n) outputs the contents of the pattern space and then reads the next line of input without returning to the top of the script. Its syntax is:


[address]n

In effect, the next command causes the next line of input to replace the current line in the pattern space. Subsequent commands in the script are applied to the replacement line, not the current line. If the default output has not been suppressed, the current line is printed before the replacement takes place.


In a longer script, you must remember that commands occurring before the next command will not be applied to the new input line, nor will commands occuring after it be applied to the old input line.

### 5.11. Reading and Writing Files

Now let's look at examples of the write command. One use is to extract information from one file and place it in its own file. 

 To account for these, you add lines to your script, making it longer, more complex, and more complete. While the amount of time you spend refining your script may cancel out the time saved by not doing the editing manually, at least during that time your mind has been engaged by your own seeming sleight-of-hand: "See! The computer did it."

### 5.12. Quit

 
 The quit command (q) causes sed to stop reading new input lines (and stop sending them to the output). Its syntax is:


[line-address]q

$ sed '100q' test
...
It prints each line until it gets to line 100 and quits. In this regard, this command functions similarly to the UNIX head command.

### 6. Advanced sed Commands

In this chapter, we cover the remaining sed commands. These commands require more determination to master and are more difficult to learn from the standard documentation than any of the basic commands. 

The advanced commands fall into three groupings: 
 

1.  Working with a multiline pattern space (N,D,P).
2.  Using the hold space to preserve the contents of the pattern space and make it available for subsequent commands (H,h,G,g,x).

3.  Writing scripts that use branching and conditional instructions to change the flow of control (:,b,t).

 fact, the scripts may be easier to write than they are to read. When you are writing a difficult script, you have the benefit of testing it to see how and why commands work.

We'd recommend that you test the scripts presented in this chapter and experiment by adding or removing commands to understand how the script is working. Seeing the results for yourself will help you understand the script much better than simply reading about it.

In this section, we will look at commands that create a multiline pattern space and manipulate its contents. 

The Delete (D) command, for instance, is a multiline version of the delete command (d). The difference is that while d deletes the contents of the pattern space, D deletes only the first line of a multiline pattern space.

The multiline Next (N) command creates a multiline pattern space by reading a new line of input and appending it to the contents of the pattern space. The original contents of pattern space and the new input line are separated by a newline. 

The embedded newline character can be matched in patterns by the escape sequence "\n". In a multiline pattern space, the metacharacter "^" 
 
 
 
 
 matches the very first character of the pattern space, and not the character(s) following any embedded newline(s). Similarly, "$" matches only the final newline in the pattern space, and not any embedded newline(s). 

The following script looks for "Operator" at the end of a line, reads the next line of input and then makes the replacement.


/Operator$/{
N
s/Owner and Operator\nGuide/Installation Guide/
}

Unfortunately, you cannot use "\n" to insert a newline in the replacement string. You must use a backslash to escape the newline, as follows:


s/Owner and Operator\nGuide /Installation Guide\
/

Do you see the two problems? The most obvious problem is that the last line did not print. The last line matches "Owner" and when N is executed, there is not another input line to read, so sed quits (immediately, without even outputting the line). To fix this, the Next command should be used as follows to be safe:

$!N

It excludes the last line ($) from the Next command. 

# reduce multiple blank lines to one; version using d command
/^$/{
	N
	/^\n$/d
}

The reason the multiline Delete command gets the job done is that when we encounter two blank lines, the Delete command removes only the first of the two.

### 6.2. A Case for Study

We can fix this problem by modifying the regular expression ".*" to zero or more occurrences of any character except ")".



Now we have a command that handles one or more occurrences on a single line.



### 6.3. Hold That Line

. The contents of the pattern space can be copied to the hold space and the contents of the hold space can be copied to the pattern space. A group of commands allows you to move data between the hold space and the pattern space.

The most frequent use of the hold space is to have it retain a duplicate of the current input line while you change the original in the pattern space.

 
 Each of these commands can take an address that specifies a single line or a range of lines. The hold (h,H) commands move data into the hold space and the get (g,G) commands move data from the hold space back into the pattern space. 

The difference between the lowercase and uppercase versions of the same command is that the lowercase command overwrites the contents of the target buffer, while the uppercase command appends to the buffer's existing contents.

The hold command replaces the contents of the hold space with the contents of the pattern space. The get command replaces the contents of the pattern space with the contents of the hold space.

The Hold command puts a newline followed by the contents of the pattern space after the contents of the hold space. (The newline is appended to the hold space even if the hold space is empty.) The Get command puts a newline followed by the contents of the hold space after the contents of the pattern space.


The exchange command swaps the contents of the two buffers. It has no side effects on either buffer.

Let's use a trivial example to illustrate putting lines into the hold space and retrieving them later. 

Any line matching a "1" is copied to the hold space and deleted from the pattern space. Control passes to the top of the script and the line is not printed. When the next line is read, it matches the pattern "2" and the line that had been copied to the hold space is now appended to the pattern space. Then both lines are printed. In other words, we save the first line of the pair and don't output it until we match the second line.

Here's the result of running the script on the sample file:



 The hold command followed by the delete command is a fairly common pairing. Without the delete command, control would reach the bottom of the script and the contents of the pattern space would be output.

The transform command could do the lowercase-to-uppercase conversion but it applies the conversion to the entire line.

After the h command, both the pattern space and the hold space are identical.

As you can see from this script, skillful use of the hold space can aid in isolating and manipulating portions of the input line.

### 6.4. Advanced Flow Control Commands

The branch (b) and test (t) commands transfer control in a script to a line containing a specified label. If no label is specified, control passes to the end of the script. 

The branch command transfers control unconditionally while the test command is a conditional transfer, occurring only if a substitute command has changed the current line.

There are no spaces permitted between the colon and the label. 

The branch command allows you to transfer control to another line in the script.

The label is optional, and if not supplied, control is transferred to the end of the script. If a label is supplied, execution resumes at the line following the label.


 If we wanted to avoid making these changes on certain lines, then we could use the branch command to skip that portion of the script. 

Once an input line is read, command1 and command2 will be applied to the line; afterwards, if the contents of the pattern space match the pattern, then control will be passed to the line following the label "top," which means command1 then command2 will be executed again.


:top
command1
command2
/pattern/b top
command3

The script executes command3 only if the pattern doesn't match. All three commands will be executed, although the first two may be executed multiple times.

In the next example, command1 is executed. If the pattern is matched, control passes to the line following the label "end." This means command2 is skipped.


command1
/pattern/b end
command2
:end
command3

In all cases, command1 and command3 are executed.

The branch command following command2 sends control to the end of the script, bypassing command3. 

If no label is supplied, control falls through to the end of the script. If the label is supplied, then execution resumes at the line following the label.


The test command allows us to drop to the end of the script once a substitution has been made. 

### 7. Writing Scripts for awk

In 1989, for System V Release 4, awk was updated in some minor ways.

As you read the rest of this book, bear in mind that the term awk refers to POSIX awk

To write an awk script, you must become familiar with the rules of the game.

not an easy task.

### 7.2. Hello, World

It has become a convention to introduce a programming language by demonstrating the"Hello, world" program. 

 That action is to execute the print statement foreach line of input.

To verify this for yourself, try entering the command line in the first example but omit thefilename.

Awk prints the message, and then exits. If a program has only a BEGIN pattern, and no otherstatements, awk will not process any input files.

### 7.4. Pattern Matching

Adding comments as you write the script is a good practice. A comment begins with the "#"character and ends at a newline. Unlike sed, awk allows comments anywhere in the script.

### 7.5. Records and Fields

In the simplest case, it takes each input line as a record and each word,separated by spaces or tabs, as a field. (The characters separating the fields are oftenreferred to as delimiters.) 

Two or more consecutive spaces and/or tabs count as a single delimiter.

"$1"refers to the first field, "$2" to the second field, and so on. "$0" refers to the entire inputrecord. 

You can use any expression that evaluates to an integer to refer to a field, not just numbersand variables.

You can change the field separator with the -F option on the command line. 

The following report is produced:

It is usually a better practice, and more convenient, to specify the field separator in the scriptitself. The system variable FS can be defined to change the field separator. 

Notice that we use blank lines in the script itself to improve readability. 

 To do this, set FS equal to a single space. In this case,leading and trailing whitespace (spaces and/or tabs) are stripped from the record, and fieldsare separated by runs of spaces and/or tabs. Since the default value of FS is a single space,this is the way awk normally splits each record into fields.

### 7.6. Expressions

The dollar sign ($) operator is used to reference fields. The following expression assigns the value of the first field of the current input record to the variable w:


w = $1

Look at the following example, which counts each blank line in a file.


# Count blank lines.
/^$/ { 
	print x += 1 
     }

Although we didn't initialize the value of x, we can safely assume that its value is 0 up until the first blank line is encountered.

$/ { 
	++x
}
END {
	print x
}
Let's try it on the sample file that has three blank lines in it.

### 7.7. System Variables

These are automatically updated by awk; for example, the current record number and input file name.

Awk defines the variable NF to be the number of fields for the current input record. Changing the value of NF actually has side effects. 

Awk also defines RS, the record separator, as a newline. RS is a bit unusual; it's the only variable where awk only pays attention to the first character of the value.

POSIX awk, assigning a new value to FS has no effect on the current input line; it only affects the next input line.


By default, the comma causes a space (the default value of OFS) to be output.

You can also use NF to reference the last field of each record. Using the "$" field operator and NF produces that reference. If there are six fields, then "$NF" is the same as "$6." Given a list of names, such as the following:

To process this data, we can specify a multiline record by defining the field separator to be a newline, represented as "\n", and set the record separator to the empty string, which stands for a blank line.


BEGIN { FS = "\n"; RS = "" }

If you want the fields to be output on separate lines, change OFS to a newline. While you're at it, you probably want to preserve the blank line between records, so you must specify the output record separator ORS to be two newlines. 
 



OFS = "\n"; ORS = "\n\n"

The point is that with awk, you are able to isolate and implement the basic functionality quite easily.


### 7.8. Relational and Boolean Operators

For instance, if we wanted to limit the records selected for processing to those that have five fields, we could use the following expression:


NF == 5

Make sure you notice that the relational operator "==" ("is equal to") is not the same as the assignment operator "=" ("equals"). It is a common error to use "=" instead of "==" to test for equality.

We can use a relational expression to validate the phonelist database before attempting to print out the record.


NF == 6 { print $1, $6 }
Then only lines with six fields will be printed.

The opposite of "==" is "!=" ("is not equal to"). Similarly, you can compare one expression to another to see if it is greater than (>) or less than (<) or greater than or equal to (>=) or less than or equal to (<=). The expression


NR > 1

tests whether the number of the current record is greater than 1.

The following expression:


NF == 6 && NR > 1
states that the number of fields must be equal to 6 and that the number of the record must be greater than 1.


In our first example, we're going to pipe the output of this command to an awk script that prints selected fields from the file listing. 

Now we can put this script in an executable file named filesum and execute it as a single-word command.

There is also a problem with this script in how it handles subdirectories. Look at the following line from an ls -l:

#2 test for 9 fields; files begin with "-"

### 7.9. Formatted Printing

And since one of awk's most common functions is to produce reports, it is crucial that we be able to format our reports in an orderly fashion. 


 Awk offers an alternative to the print statement, printf, which is borrowed from the C programming language. The printf statement can output a simple string just like the print statement.

The main difference that you will notice at the outset is that, unlike print, printf does not automatically supply a newline. You must specify it explicitly as "\n".


This printf statement can be used to specify the width and alignment of output fields. A format expression can take three optional modifiers following "%!"(MISSING) and preceding the format specifier:

The next example left-justifies the text:


printf("|%!s(MISSING)|\n", "hello")
It produces:


|hello     |

### 7.10. Passing Parameters Into a Script

awk -f scriptfile "high=$1" "low=$2" datafile
If this shell script were named awket, it could be invoked as:


In addition, environment variables or the output of a command can be passed as the value of a variable. Here are two examples:


awk '{ ... }' directory=$cwd file1 ...
awk '{ ... }' directory=`pwd` file1 ...


 POSIX awk provides a solution to the problem of defining parameters before any input is read. The -v option[15] specifies variable assignments that you want to take place before executing the BEGIN procedure

### 8. Conditionals, Loops, and Arrays

A conditional statement allows you to make a test before performing an action.

A conditional statement is introduced by if and evaluates an expression placed in parentheses. The syntax is:



Perhaps the simplest conditional expression that you could write is one that tests whether a variable contains a non-zero value.


if ( x ) print x
If x is zero, the print statement will not be executed. If x has a non-zero value, that value will be printed. You can also test whether x equals another value:


We can also test whether x matches a pattern using the pattern-matching operator "~":


if ( x ~ /[yY](es)?/ ) print x

If any action consists of more than one statement, the action is enclosed within a pair of braces.

if ( avg >= 65 ) 
	grade = "Pass"
else 
	grade = "Fail"


Conditional Operator

 
 Awk provides a conditional operator that is found in the C programming language. Its form is:


expr ? action1 : action2

grade = (avg >= 65) ? "Pass" : "Fail"
This form has the advantage of brevity and is appropriate for simple conditionals such as the one shown here

### 8.2. Looping


 A loop is a construct that allows us to perform one or more actions again and again. In awk, a loop can be specified using a while, do, or for statement.


To keep in mind the difference between the do loop and the while loop, remember that the do loop always executes the body of the loop at least once. At the bottom of the procedure, you decide if you need to execute it again.


### 8.3. Other Statements That Affect Flow Control

There are two statements that affect the flow control of a loop, break and continue. The break statement, as you'd expect, breaks out of the loop, such that no more iterations of the loop are performed. The continue statement stops the current iteration before reaching the bottom of the loop and starts a new iteration at the top.

Consider what happens in the following program fragment:

There are two statements that affect the main input loop, next and exit. The next statement causes the next line of input to be read and then resumes execution at the top of the script.[1] This allows you to avoid applying other procedures on the current input line. 

We used the exit statement earlier in the factorial program to exit after reading one line of input.

An exit statement can take an expression as an argument. The value of this expression will be returned as the exit status of awk. 

### 8.4. Arrays


 An array is a variable that can be used to store a set of values. Usually the values are related in some way. Individual elements are accessed by their index in the array. Each index is enclosed in square brackets. 


 Loops can be used to load and extract elements from arrays. For instance, if the array flavor has five elements, you can write a loop to print each element:



student_avg[NR] = avg

 
 The system variable NR is used as the subscript for the array because it is incremented for each record.

 What makes an associative array unique is that its index can be a string or a number.


Associative arrays are a distinctive feature of awk, and a very powerful one that allows you to use a string as an index to another value. For instance, you could use a word as the index to its definition. If you know the word, you can retrieve the definition.


 There is a special looping syntax for accessing all the elements of an associative array. It is a version of the for loop.


for ( variable in array )
     do something with array[variable]

This syntax can be applied to arrays with numeric subscripts. However, the order in which the items are retrieved is somewhat random.[2] The order is very likely to vary among awk implementations; be careful to write your programs so that they don't depend on any one version of awk.


It is important to remember that all array indices in awk are strings. Even when you use a number as an index, awk automatically converts it to a string first.


Testing for Membership in an Array

 
 
 
 The keyword in is also an operator that can be used in a conditional expression to test that a subscript is a member of an array. 

returns 1 if array[item] exists and 0 if it does not. 

Using split( ) to Create Arrays

 
 
 
 The built-in function split( ) can parse any string into elements of an array. This function can be useful to extract "subfields" from a field. The syntax of the split( ) function is:


n = split(string, array, separator)

you could use the split( ) function to extract the person's first and last names. 

The following statement breaks up the first field into elements of the array fullname:


z = split($1, fullname, " ")

because z contains the number of elements in the array. 

z = split($1, array, " ")
for (i = 1; i <= z; ++i)
	print i, array[i]

This section looks at two examples that demonstrate similar methods of converting output from one format to another.

# print specified element
	print numerals[$1]

This script defines a list of 10 roman numerals, then uses split( ) to load them into an array named numerals. This is done in the BEGIN action because it only needs to be done once.

The exit statement terminates the program. The last rule is executed only if there is no valid entry.


### 8.6. System Variables That Are Arrays

 It allows you to access variables in the environment. The following script loops through the elements of the ENVIRON array and prints them.


# environ.awk - print environment variable
BEGIN {
	for (env in ENVIRON)
		print env "=" ENVIRON[env]
}

output produced by the env command (printenv on some systems).



If programming is new to you, be sure you take the time to run and modify the programs in this chapter, and write small programs of your own. It is essential, like learning how to conjugate verbs, that these constructs become familiar and predictable to you.

### 9. Functions

The int( ) function simply truncates; it does not round up or down. (Use the printf format "%!f(MISSING)" to perform rounding.)[1]


### 9.2. String Functions


 The built-in string functions are much more significant and interesting than the numeric functions. Because awk is essentially designed as a string-processing language, a lot of its power derives from these functions.

The beginning of the string is position 1 (which is different from the C language, where the first character in a string is at position 0). Look at the following example:

pos = index("Mississippi", "is")
The value of pos is 2. If the substring is not found, the index( ) function returns 0.

For instance, to evaluate the length of the current input record, we specify length($0). (As it happens, if length( ) is called without an argument, it returns the length of $0.)

The length( ) function is often used to find the length of the current input record, in order to determine if we need to break the line.


Awk provides two substitution functions: sub( ) and gsub( ). The difference between them is that gsub( ) performs its substitution globally on the input string whereas sub( ) makes only the first possible substitution. 

Both functions take at least two arguments. The first is a regular expression (surrounded by slashes) that matches a pattern and the second argument is a string that replaces what the pattern matches. 

If there is no third argument, the substitution is made for the current input record ($0).


The substitution functions actually return the number of substitutions made. sub( ) will always return 1 if successful; both return 0 if not successful. Thus, you can test the result to see if a substitution was made.


The conditional statement tests the return value of gsub( ) such that the current input line is printed only if a change is made.


As with sed, if an "&" appears in the substitution string, it will be replaced by the string matched by the regular expression. Use "\&" to output an ampersand.

gsub(/UNIX/, "\\fB&\\fR")
If the input is "the UNIX operating system", the output is "the \fBUNIX\fR operating system".

awk '{ printf("<%!s(MISSING)>, <%!s(MISSING)>\n", tolower($0), toupper($0)) }' test

The match( ) function allows you to determine if a regular expression matches a specified string. It takes two arguments, the string and the regular expression. (This function is confusing because the regular expression is in the second position, whereas it is in the first position for the substitution functions.)


### 9.3. Writing Your Own Functions

A function definition can be placed anywhere in a script that a pattern-action rule can appear. Typically, we put the function definitions at the top of the script before the pattern-action rules. A function is defined using the following syntax: 


### 10. The Bottom Drawer

In this chapter we cover a number of topics, including the following:
▪  The getline function
▪  The system( ) function
▪  Directing output to files and pipes
▪  Debugging awk scripts


The getline function is used to read another line of input. Not only can getline read from the regular input data stream, it can also handle input from files and pipes.

The getline function is similar to awk's next statement.

### 10.3. The system( ) Function

The system( ) function executes a command supplied as an expression.[3] It does not, however, make the output of the command available within the program for processing. It returns the exit status of the command that was executed. 

The system( ) function is called from an if statement that tests for a non-zero exit status. Running the program twice produces one success and one failure:


getline < "-" # get response

Note the two uses of the getline function: the first gets the user's response and the second executes the pwd command.


### 10.5. Directing Output to Files and Pipes

The output of any print or printf statement can be directed to a file, using the output redirection operators ">" or ">>". For example, the following statement writes the current record to the file data.out:


You can also direct output to a pipe. The command


print | command
opens a pipe the first time it is executed and sends the current record as input to that command. In other words, the command is only invoked once, but each execution of the print command supplies another line of input.

In most cases, we prefer to use a shell script to pipe the output of the awk command to another command rather than do it inside the awk script. 

You should perhaps test the filename, either to determine its length or to look for characters that cannot be used in a filename.


print $0 > filename

# output name to screen 
		print filename 
		# print any lines collected

# close up and clean up for next one
	if ($0 ~ /^$/) {
		close(filename)
		filename = ""
		file = 0
		i = 0
	}

### 10.7. Debugging

If you are unsure about the result of a substitution command or any function, print the string before and after the function is called:



Finding Out Where the Problem Is


 Another technique is simply commenting out a series of lines that may be causing problems to see whether they really are. 

### 10.9. Invoking awk Using the #! Syntax

Note that no quotes are necessary around the script. All lines in the file after the first one will be executed as though they were specified in a separate script file.

Because no script has been supplied, awk produces a syntax error message. In other words, if the name of the shell script is "myscript," then the first script executes as:

### 11. A Flock of awks

Over the years, UNIX vendors have enhanced their versions of original awk; you may need to write small test programs to see exactly what features your old awk has or doesn't have.


### 11.2. Freely Available awks

All three free awks extend the delete statement, making it possible to delete all the elements of an array at one time. The syntax is:


delete array

Normally, to delete every element from an array, you have to use a loop, like this.


for (i in data)
	delete data[i]

This is particularly useful for arrays with lots of subscripts; this version is considerably faster than the one using a loop.


Note that a special filename, like any filename, must be quoted when specified as a string constant.

▪  printf was improved: dynamic width and precision were added, and the behavior for "%!c(MISSING)" was rationalized.


The Free Software Foundation GNU project's version of awk, gawk, implements all the features of the POSIX awk, and many more. It is perhaps the most popular of the freely available implementations; gawk is used on Linux systems, as well as various other freely available UNIX-like systems, such as NetBSD and FreeBSD.


 Gawk provides several additional regular expression operators. These are common to most GNU programs that work with regular expressions. The extended operators are listed in Table 11.5.


### 11.3. Commercial awks


 There are also several commercial versions of awk. In this section, we review the ones that we know about.

### 12.2. Generating a Formatted Index


 The process of generating an index usually involves three steps:
▪  Code the index entries in the document.
▪  Format the document, producing index entries with page numbers.

### 13. A Miscellany of Scripts

adjAdjust lines for text files.

### A.2. Syntax of sed Commands

Sed copies each line of input into a pattern space. 


 The ! operator following an address causes sed to apply the command to all lines that do not match the address.


Braces ({}) are used in sed to nest one address inside another or to apply multiple commands at the same address.


### B.2. Language Summary for awk

An array is a variable that can be used to store a set of values. The following statement assigns a value to an element of an array:

You can use the operator in to test that an element exists by testing to see if its index exists.


if (index in array)