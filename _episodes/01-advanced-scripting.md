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

## Conditionals

You can use conditional statements to test whether something is true or false
and do a programatic behavior as a result. Let's go into the molecules
directory and make a script that will show us molecules with at least a certain number
of lines. Make a new script called is_big.sh

We know that wc -l gives us the number of lines in a file. Let's save that to a
variable.
~~~
num=`wc -l $1`
~~~
{: .bash}

Warning - there is no space around the equals sign. This is important for the
variable to be defined.

We build an if statement like a loop

~~~
if [ "$num" -gt "5" ]
then
    echo $1 "is big enough"
fi
~~~
{: .bash}

Does that work? You'll probably get an error

~~~
is_big.sh: line 2: :       30 octane.pdb: integer expression expected
~~~
{: .bash}

We forgot to check our import. `wc -l` gives us the size and the file name
which isn't a number. If we redirect the file into wc it will work

~~~
num=`wc -l < $1`
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

## System information

You can get the current date using the date command. There are lots of
formatting options, but we're going to go with the recommended year-month-day
option.

~~~
date "+%F"
~~~

Activity: Revise our Nell script to include the date in the output file format.
It might be helpful to save the date as a variable.

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
