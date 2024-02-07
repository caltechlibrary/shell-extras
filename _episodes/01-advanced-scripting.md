---
title: "Advanced Shell Scripting"
teaching: 60
exercises: 0
questions:
- "How do I use if statements?"
- "Can I add date to filenames in bash script?"
- "Can I use file attributes like image size or properties to filter files?"
objectives:
- "Learn how to use conditionals"
- "Learn about getting system information into scripts"
- "Learn about getting file information into scripts"
keypoints:
- "Shell scripts can be used to more complicated programming tasks"
---

*Instructor note: there are intentional typos in these examples to show the importnace of spaces*

## Recapping scripting

We're going to use a demonstration script from the shell-novice lesson.

## Nelle's Pipeline: Processing Files

Nelle is now ready to process her data files using `goostats` --- a shell script written by her supervisor.
This calculates some statistics from a protein sample file, and takes two arguments:

1. an input file (containing the raw data)
2. an output file (to store the calculated statistics)

Since she's still learning how to use the shell,
she decides to build up the required commands in stages.
Her first step is to make sure that she can select the right input files --- remember,
these are ones whose names end in 'A' or 'B', rather than 'Z'. Starting from her home directory, Nelle types:

~~~
$ cd north-pacific-gyre
~~~
{: .output}

And write a script in nano that will generate an input and output file name

~~~
$ for datafile in NENE*[AB].txt
> do
>     echo $datafile stats-$datafile
> done
~~~
{: .language-bash}

~~~
NENE01729A.txt stats-NENE01729A.txt
NENE01729B.txt stats-NENE01729B.txt
NENE01736A.txt stats-NENE01736A.txt
...
NENE02043A.txt stats-NENE02043A.txt
NENE02043B.txt stats-NENE02043B.txt
~~~
{: .output}

She hasn't actually run `goostats` yet,
but now she's sure she can select the right files and generate the right output filenames.

~~~
$ for datafile in NENE*[AB].txt
 do
     bash goostats.sh $datafile stats-$datafile
 done
~~~
{: .language-bash}

When she presses Enter,
the shell runs the modified command.
However, nothing appears to happen --- there is no output.
After a moment, Nelle realizes that since her script doesn't print anything to the screen any longer,
she has no idea whether it is running, much less how quickly.
She kills the running command by typing `Ctrl-C`,
uses up-arrow to repeat the command,
and edits it to read:

~~~
$ for datafile in NENE*[AB].txt
 do
     echo $datafile
     bash goostats $datafile stats-$datafile
 done
~~~
{: .language-bash}



## System information and variables

You can get the current date using the date command. There are lots of
formatting options, but we're going to go with the recommended year-month-day
option.

~~~
date "+%F"
~~~

Let's make a script that prints out the date. We can save the date in a variable like

~~~
date = $(date "+%F")
~~~

You probably got an error like

~~~
date: illegal time format
~~~

This is because we had extra spaces around the equals sign. This is a bit confusing, because the error is coming from the variable name we used 'date'. Since there is a space, bash thinks that 'date' variable name is a command we want to run. If you use

~~~
date=$(date "+%F")
echo $date
~~~

You should get the date printed as expected

## Conditionals

You can use conditional statements to test whether something is true or false
and do a programmatic behavior as a result. Let's go into the molecules
directory and make a script that will show us molecules with at least a certain number
of lines. Make a new script called is_big.sh

We know that wc -l gives us the number of lines in a file. Let's save that to a
variable.
~~~
num=$(wc -l $1)
~~~
{: .bash}

We build an if statement like a loop

~~~
if ["$num" -gt "5"]
then
    echo $1 "is big enough"
fi
~~~
{: .bash}

Does that work? You'll probably get an error

~~~
[      30: command not found
~~~

This is again a spacing issue, but the opposite of the earlier one we saw. You need a space after the `[`, otherwise bash thinks it is a command. Once we fix the spacing

~~~
if [ "$num" -gt "5" ]
then
    echo $1 "is big enough"
fi
~~~
{: .bash}

We get a different error

~~~
is_big.sh: line 2: :       30 octane.pdb: integer expression expected
~~~
{: .bash}

We forgot to check our import. `wc -l` gives us the size and the file name
which isn't a number. If we redirect the file into wc it will work

~~~
num=$(wc -l < $1)
~~~
{: .bash}

We can add else to have the script always print something

~~~
if [ "$num" -gt "5" ]
then
    echo $1 "is big enough"
else
    echo $1 "is not big enough"
fi
~~~
{: .bash}

Activity: Make the size cutoff generalizabla

## System Variables

You can set variables outside of your script that you can use in the script.
This is useful for saving passwords or things that you don't want to put in the
script and don't want to have to type at the command line every time. Let's go
back to molecules and have the size cutoff be an environment variable. First
we'll set the variable.

~~~
$ export CUTOFF=5
~~~
{: .bash}

Then add the variable to your script

~~~
if [ "$num" -gt $CUTOFF ]
~~~
{: .bash}

If you want variables to be set wevery time you log in, you can add them to the
.bash_profile file (or .zshrc file if you're using the most recent OSX version
- don't know type echo "$SHELL" and is if says /bin/zsh you're using the most
  recent version)  in your home directory

{% include links.md %}
