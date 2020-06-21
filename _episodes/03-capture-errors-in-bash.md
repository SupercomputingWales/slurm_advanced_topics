---
title: "Capture errors in shell scripts"
teaching: 0
exercises: 0
questions:
- "How the shell helps to keep you safe?"
objectives:
- "How to prevent common errors when working with Bash scripts"
keypoints:
- "Bash has built in options that allow to check before executing if variables are set in a script."
- "Trapping errors early is very important and can save time and effort in the long-run."
- "Writing maintainable shell scripts makes it easier to come back and read your code."
---
This section describes some Bash options useful when working with scripts in Linux. These are specially important in the context of SLURM job scripts since they help toprevent time consuming errors (e.g. a job waiting in the queue for hours and then crashing in the first minutes or seconds of execution due to variable typo), or more dangerously, undesired data deletion.

## The Shell
<img src="{{ page.root }}/fig/shell.png" alt="Shell" width="15%" height="15%" />
The Shell is Linux program for command interpretation, it takes commands from the keyboard and sends them to the operating system for execution. Users interact with the shell when working on a terminal. In the old days of Linux, it was the <a href="https://en.wikipedia.org/wiki/In_the_Beginning..._Was_the_Command_Line" target="_blank">only</a> user interface available. There are several Shells available, the one used in Hawk is Bash (Bourne Again Shell).

Bash shell is a programming language, and as such, it has its own syntax rules and lots of options and features. In this lesson we will only focus on two of them:
- how to trap undefined variables
- how to trap error messages

### Trapping undefined variables
Bash has a built in command *set* which control shell attributes. In particular, we are interested in *set -u*. From the manual:

~~~
-u      Treat  unset  variables  and  parameters other than the special
        parameters "@" and "*" as an error  when  performing  parameter
        expansion.   If  expansion is attempted on an unset variable or
        parameter, the shell prints  an  error  message,  and,  if  not
        interactive, exits with a non-zero status.
~~~

When used within a shell script, *set -u* will trigger and error if an undefied variable is found.

> ## Define your variables.
>
> - Run lesson_4/trap_1.sh – what do you notice?
> - Run lesson_4/trap_2.sh – is this an improvement?
{: .challenge}


### Exit on non-zero exit status
The second useful Bash feature is *set -e*. From the manual:

~~~
-e      Exit immediately if a pipeline (which may consist of  a  single
        simple  command),   a subshell command enclosed in parentheses,
        or one of the commands executed  as  part  of  a  command  list
        enclosed  by braces (see SHELL GRAMMAR above) exits with a non-
        zero status.  The shell does not exit if the command that fails
        is  part  of  the command list immediately following a while or
        until keyword, part of  the  test  following  the  if  or  elif
        reserved words, part of any command executed in a && or || list
        except the command following the final && or ||, any command in
        a  pipeline  but  the last, or if the command's return value is
        being inverted with !.  A trap on  ERR,  if  set,  is  executed
        before the shell exits.  This option applies to the shell envi‐
        ronment and each subshell environment separately  (see  COMMAND
        EXECUTION  ENVIRONMENT  above), and may cause subshells to exit
        before executing all the commands in the subshell.
~~~

In short, Bash will stop the program execution if an unhandled error in found. The common way to handle erros in Bash is through conditionals.

> ## Catch the error
>
> - Run lesson_4/trap_3.sh – what is now happening?
> - Run lesson_4/trap_4.sh – how is this helping?
{: .challenge}

> ## Syntax highlighting
> A side note on syntax highlighting. It it very useful and some text editors provide it as default (Vim, Emacs). Nano also provide it for a few languages:
> <img src="{{ page.root }}/fig/nano-syntax-highlight-languages.png" alt="nano syntax languages" width="50%" height="50%" />
> To activate it for Bash, create a .nanorc file in your home directory with the following line:
> <pre style="color: silver; background: black;">include /usr/share/nano/sh.nanorc</pre>
> Open any of the previous bash examples, see the difference? 
{: .callout}

### Handling errors
As seen before, undefined variables are easy to fix, but how can errors be fixed? Any error in a program that occurs within an *if* condition is not trapped by *set -e* since it is being handled.
~~~
if mkdir $MYPATH
then
  echo “INFO: Directory created.”
else
  echo “ERROR: Failed to create directory.”
  exit 1
fi
~~~
{: .language-bash}

Any program in a Boolean expression is not trapped since it is also being handled.
~~~
mkdir $MYPATH || ( echo “ERROR: Failed to created directory.” && exit 1 ) 
~~~
{: .language-bash}

> ## Handle the error
>
> - Run lesson_4/error_1.sh – what do you notice?
> - Run lesson_4/error_2.sh – is this an improvement?
{: .challenge}

### Functions
As in any programming language splitting up your tasks makes reading it easier. A shell function is like running a mini version of a script inside another script. For example:
~~~
create_directory ()
{
  if mkdir $1
  then 
    echo “INFO: Created directory $1”
  else
    echo “ERROR: Failed to created $1”
  fi
}
~~~
{: .language-bash}

> ## Shell functions
>
> - Run lesson_4/function_1.sh – what is wrong with this script?
> - Run lesson_4/function_2.sh – how is this helping?
>
{: .challenge}

> ## How does this help SLURM
> 1. Load the modules before any use of trapping and undefined variables.
> 2. Define functions for specific issues
>    * Create working directory
>    * Run a particular pre-processing step
>    * Process output files
> 3. Make sure SLURM jobs exits soon after error.
>    * If directory is not creatable
>    * Input file not found
> 4. Reduces incorrect runs and reduces jobs that do not produce useful output.
{: .checklist}

{% include links.md %}

