---
title: "Common Linux CLI Text Editors"
teaching: 0
exercises: 0
questions:
- "What are some of the most common Linux text editors"
objectives:
- "Identify Linux text editors available on Hawk"
- "Basic commands to edit text files"
keypoints:
- "Nano, Vim and Emacs are the most common CLI Linux text editors"
- "Gedit is also a common text editor with a graphical interface"
- "Command line text editors might have a steep learning curve but are powerful"
- "If you plan to spend a lot of time working on text files on Linux, it is worth mastering a CLI text editor."
---
This section describes three of the most common Command Line Interface text editors for Linux: Nano, Gedit, Vim and Emacs. Text editors can be divided in two main groups, those friendly with new users and with basic features and those feature-rich with a steep learning curve. Nano and Gedit belong to the first group and are recommended for users who only need to perform minor editions in text files, while Vim and Emacs are suggested for users who plan to spend a long time working on the command line and with text files (e.g. programmers).

## Nano
<img src="{{ page.root }}/fig/Nano-logo.png" alt="Nano Logo" width="30%" height="30%" />
A basic and dependable Command Line Interface (CLI) text editor, Nano is arguably the simplest one of the ones covered in this lesson. To start Nano on Hawk:
<pre style="color: silver; background: black;">$ nano </pre>
This should display a "new buffer" in the terminal waiting for text input. The terminal window should look something like this:
<img src="{{ page.root }}/fig/Nano-home-arrows.png" alt="Nano home" width="50%" height="50%" />
With a top line showing Nano's version and current filename, a white rectagle showing the position of the cursor, and a bottom menu with common commands.

The caret symbol “^” represents the control key <kbd>Ctrl</kbd>.  So "^X" means that you should press <kbd>Ctrl</kbd>+<kbd>X</kbd> to quit nano. As you type commands, the menu displayed at the bottom of your screen will update with the currently available options. To cancel a command use <kbd>Ctrl</kbd>+<kbd>C</kbd>.

> ## Sort the rows...
>
> Lets try nano with an example.
>
> Open the file called nano-rows.txt and try sorting the rows.
<img src="{{ page.root }}/fig/Nano-rows-only.png" alt="Nano only rows" width="50%" height="50%" />
Save in a new file nano-sorted-rows.txt
{: .challenge}

## Gedit
<img src="{{ page.root }}/fig/Gedit-logo.png" alt="Gedit Logo" width="10%" height="10%" />
The GNOME desktop text editor. Gedit is another basic Linux text editor, the main difference with Nano is that it is a graphical editor so you will need to use X11 (see lesson 1) to work with it. When openning Gedit it will open a new window that resembles Windows' notepad.
<img src="{{ page.root }}/fig/Gedit-home.png" alt="Gedit home" width="50%" height="50%" />
> ## Sort the rows... again
>
> Lets try Gedit with the same example.
>
> Open the file called unsorted-rows.txt and try sorting the rows.
> <img src="{{ page.root }}/fig/Gedit-rows.png" alt="Gedit rows" width="50%" height="50%" />
> Save in a new file gedit-sorted-rows.txt. How was different from Nano? Did you get any odd warnings in your terminal while working with gedit?
{: .challenge}


## Vim
<img src="{{ page.root }}/fig/Vim-logo.png" alt="Vim logo" width="10%" height="10%" />
VI iMproved is a powerful and nearly omnipresent text editor. To open vim:
<pre style="color: silver; background: black;">$ vim </pre>
<img src="{{ page.root }}/fig/Vim-home.png" alt="Vim home" width="50%" height="50%" />
This will display a black screen with Vim's version, Authors, and few basic commands, including how to quit Vim (<kbd>:</kbd>+<kbd>q</kbd>+<kbd>Return</kbd>) and how to get help (<kbd>:</kbd>+<kbd>help</kbd>+<kbd>Return</kbd>). Vim power becomes apparent when the task at hand involves slightly more complex or repetitive text operations. 
Vim has two main modes of operation "Normal" and "Editing" mode. Normal mode is the default when you open Vim. In this mode Vim can accept commands, like quit. To edit a file, press <kbd>i</kbd> (Vim will let you know you are in edit mode with an "--INSERT--" message at the bottom of the screen). To return to normal mode, press <kbd>Esc</kbd>.

> ## Copy and paste
>
> Open unsorted-rows.txt. Make sure you are on normal mode and and type <kbd>v</kbd> to enter the "Visual" mode and use your keyboard arrows to select text. Then type <kbd>y</kbd> to copy or <kbd>c</kbd> to cut. Move the cursor to the position were you want to paste the text and type <kbd>p</kbd>.
>
> Try doing this again, but this time enter the Visual Block mode with <kbd>Ctrl</kbd>+<kbd>v</kbd>. What did you notice?
{: .challenge}

Short summary of Vim commands.

<table style="width:100%">
 <tr>
  <th> Command </th>
  <th> Description </th>
  <th> Command </th>
  <th> Description </th>
 </tr>
 <tr>
  <td> <kbd>:</kbd>+<kbd>q</kbd>+<kbd>Return</kbd> </td>
  <td> Quit </td>
  <td> <kbd>u</kbd> </td>
  <td> undo </td>
 </tr>
 <tr>
  <td> <kbd>:</kbd>+<kbd>q</kbd>+<kbd>!</kbd>+<kbd>Return</kbd> </td>
  <td> Quit without saving </td>
  <td> <kbd>Esc</kbd> </td>
  <td> Return to normal mode </td>
 </tr>
 <tr>
  <td> <kbd>:</kbd>+<kbd>help</kbd>+<kbd>Return</kbd> </td>
  <td> Get help </td>
  <td> <kbd>v</kbd> </td>
  <td> Enter visual mode </td>
 </tr>
 <tr>
  <td> <kbd>:</kbd>+<kbd>w</kbd>+<kbd>Return</kbd> </td>
  <td> Save </td>
  <td> <kbd>Ctrl</kbd>+<kbd>v</kbd> </td>
  <td> Enter Visual Block mode  </td>
 </tr>
 <tr>
  <td> <kbd>0</kbd> </td>
  <td> Move to beginning of line </td>
  <td> <kbd>g</kbd>+<kbd>g</kbd> </td>
  <td> Move to beggining of file </td>
 </tr>
 <tr>
  <td> <kbd>$</kbd> </td>
  <td> Move to end of line </td>
  <td> <kbd>Shift</kbd>+<kbd>g</kbd> </td>
  <td> Move to end of file </td>
 </tr>
</table>



> ## Sort the rows... with a twist
>
> Lets try Vim with yet again the same example.
>
> Open the file called unsorted-rows.txt and try sorting the rows but this time, try moving the numbers infront of the word 'job'.
> <img src="{{ page.root }}/fig/Gedit-rows.png" alt="Gedit rows" width="50%" height="50%" />
> Save in a new file vim-sorted-rows.txt. Would this have been easier with Nano? What if the file contained 100s of lines? 
{: .challenge}


> ## A tutor for you ...
>
> If Vim is available in your platform, it is most likely that vimtutor is also available. Vimtutor is at is core a text file with some useful exercises designed to help you master Vim. To start it:
> <pre style="color: silver; background: black;">$ vimtutor </pre>
>
{: .callout}

## Emacs
<img src="{{ page.root }}/fig/Emacs-logo.png" alt="Emacs logo" width="10%" height="10%" />
Is another powerful text editor. While Vim has the fastest startup time of the two advanced text editor discussed in this lesson, and is more widespread, Emacs has, arguibly, a more feature-rich environment extending beyond simply word processing and potentially wrapping around everything you do (with some people going as fars as stating that Emacs is a "way of life").

To try Emacs, open it in the command line with:
<pre style="color: silver; background: black;">$ Emacs </pre>
<img src="{{ page.root }}/fig/Emacs-home.png" alt="Emacs home" width="50%" height="50%" />
This will display a black screen with some useful information including its version, licence and some basic commands. More importantly, Emacs let us know about its command grammar where 'C-' means use the <kbd>Ctrl</kbd> key and 'M-' is the <kbd>Alt</kbd> key. This is specially helpful to know when searching for help online about Emacs commands.

Try openning again "unsorted-rows.txt" and do some changes.

Short summary of Emacs commands.

<table style="width:100%">
 <tr>
  <th> Command </th>
  <th> Description </th>
  <th> Command </th>
  <th> Description </th>
 </tr>
 <tr>
  <td> <kbd>Ctrl</kbd>+<kbd>x</kbd>+<kbd>Ctrl</kbd>+<kbd>c</kbd> </td>
  <td> Exit Emacs </td>
  <td> <kbd>Ctrl</kbd>+<kbd>s</kbd> </td>
  <td> Search string </td>
 </tr>
 <tr>
  <td> <kbd>Ctrl</kbd>+<kbd>x</kbd>+<kbd>u</kbd> </td>
  <td> undo changes </td>
  <td> <kbd>Ctrl</kbd>+<kbd>g</kbd> </td>
  <td> Cancel command </td>
 </tr>
 <tr>
  <td> <kbd>Ctrl</kbd>+<kbd>h</kbd> </td>
  <td> Get help </td>
  <td> <kbd>v</kbd> </td>
  <td> Enter visual mode </td>
 </tr>
 <tr>
  <td> <kbd>Ctrl</kbd>+<kbd>x</kbd>+<kbd>s</kbd> </td>
  <td> Save </td>
  <td> <kbd>Ctrl</kbd>+<kbd>v</kbd> </td>
  <td> Enter Visual Block mode  </td>
 </tr>
 <tr>
  <td> <kbd>Ctrl</kbd>+<kbd>a</kbd> </td>
  <td> Move to beginning of line </td>
  <td> <kbd>Esc</kbd>+<kbd><</kbd> </td>
  <td> Move to beggining of file </td>
 </tr>
 <tr>
  <td> <kbd>Ctrl</kbd>+<kbd>e</kbd> </td>
  <td> Move to end of line </td>
  <td> <kbd>Esc</kbd>+<kbd>></kbd> </td>
  <td> Move to end of file </td>
 </tr>
</table>


{% include links.md %}
