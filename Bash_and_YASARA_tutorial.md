# Exercise

*Computational analysis of mutations*

This exercise is meant to give an introduction to basic computational methods for mutagenesis and protein engineering. It will cover diverse methods including command line (bash) input and scripting, molecular simulations and molecular visualization. It requires a bash command line (the standard terminal for most linux systems and MacOS, but also available for Windows 10) and YASARA. In this manual, the command line is depicted as follow:

Bash input is indicated with a leading “$” sign in grey background:

```bash
 $ input
```

Bash output is the same, but without the “$” sign:

```bash
 output
```

YASARA command line input is indicated with a leading “>” sign:

```yasara
> input
```

YASARA output is the same, but without the “>” sign:

```yasara
output
```


## Part 1: Preparation of files and scripts


### Create folders and learn basic commands

- Fire up a terminal (Mac: press cmd+space and type “terminal”, linux: press Ctrl+Alt+T). Although, better use the much better [iTerm2](https://iterm2.com/) terminal.
- The terminal usually opens in the home directory. To see your current directory use the *print working directory* command `pwd`:

```bash
 $ pwd
 /Users/max
```

- Make use of the most frequently used linux command *change directory* command `cd` to go to a folder of your choice where you will store the files necessary for this exercise

```bash
$ cd university
```

- Create a folder for this exercise with *make directory* and go into the folder

```bash
$ mkdir exercise
$ cd exercise
```

- Learn these 4 folder shortcuts: `~` (home),`.` (current folder), `..` (parent folder) and `-` (previous folder)

```bash
$ pwd
/Users/max/university/exercise
$ cd ..
$ pwd
/Users/max/university
$ cd ./exercise
/Users/max/university/exercise
$ cd ~
$ pwd
/Users/max/
$ cd -
$ pwd
/Users/max/university/exercise
```

- Did you type `pwd` every time? Note that you can use the arrow keys up and down to access your command history. This saves a lot of time and you can also modify the old command.
- Learn how to use tab completion. When you read <tab>, don’t type this, but press the TAB key on your keyboard

```bash
$ cd uni<tab>
```

The command line should now fill in the rest of the foldername (works also with filenames)

```bash
$ cd university/
```

If you press TAB again it will also fill in the name of the folder inside the folder (at least if there is only one file/folder, otherwise you might have to type the first few letters of the folder and then press TAB.

```bash
$ cd university/<tab>
$ cd university/exercise
```

- Use the *list* command `ls` to show files and folders

```bash
$ ls
$
```

- No files and folders present yet. Create some and show them.

```bash
$ mkdir pdbs simulations scripts
$ ls
pdbs simulations scripts
```

- If you prefer a list style, plus some more info like when the file was modified, use the `-l` option. That is done by typing a command line argument that changes the default parameters of the command. It comes right after the command and follows a “-“.

```bash
$ ls -l
drwxr-xr-x 2 max staff 64 Jun 11 15:10 pdbs
drwxr-xr-x 2 max staff 64 Jun 11 15:10 simulations
drwxr-xr-x 2 max staff 64 Jun 11 15:10 scripts
```

- Or use -1 for a simple list:

```bash
$ ls -1
pdbs
simulations
scripts
```

- Learn how to `echo` text to the command line:

```bash
$ echo hello
test
```

- By default, the echo command will print to the screen. However, you can also redirect the output to a file, using `>` and `>>`

```bash
$ echo hello > test.txt
$ ls
pdbs simulations scripts test.txt
```

- You can show the content of a text file using the `cat` command. (Remember to use TAB completion for file names)

```bash
$ cat test.txt
hello
```

- `>` overrides the file, while `>>` appends:

```bash
$ echo and goodbye >> test.txt
$ cat test.txt
hello
and goodbye
```

- If you want to delete a file, use `rm`. (To delete a folder, you need the -r option)

```bash
$ ls
pdbs simulations scripts test.txt
$ rm test.txt
$ ls
pdbs simulations scripts
```

- Now download the pdb file of our protein. Go to [https://www.rcsb.org/structure/4RG3](https://www.rcsb.org/structure/4RG3) and click Download Files>PDB format. The file should be located in your standard Downloads folder. Copy the file to the exercise folder using the `cp` and `mv` commands.

```bash
$ pwd
/Users/max/university/exercise
$ mv ~/Downloads/4RG3.pdb .
$ ls
pdbs simulations scripts 4RG3.pdb
$ cp 4RG3.pdb pdbs
```

- Use `ls` to confirm that the file 4RG3.pdb now is located in the exercise and in the pdbs folder, but not anymore in the Downloads folder.

Congrats! You’ve mastered the most important bash commands.


### Use bash and regular expressions to edit text files

- Let's go through some examples of mutations. You will later have to apply the procedure to the real mutations that you are doing in the lab.
- First we create an example file with some mutations. Go to the terminal and use echo and `>>` redirect to create a file with a mutation, then a tab, and then a number. To enter a literal tab character in the terminal you need to know two things: firstly, because pressing tab is used in bash for tab completion, you need to first press CTRL+V and then press the tab key. Secondly, in order to print the tab, you need to enclose your string in quotes.

```bash
$ cd ~/university/exercise/
$ echo "R209E	1.00" >> muts_energ.tab
```

- You can also use the simple command line editor nano, to edit a text file interactively.

```bash
$ nano muts_energ.tab
```

- You should see something like this:

![nano screenshot](image1.png)

- You can simply start typing text now. Write the following mutations and numbers separated by a tab:

```bash
R209E	1.00
T78N	2.00
F248A	1.50
```

- At  the bottom of the nano editor, you can see the commands. ^ means to press the CTRL key. If you press CTRL+X, you exit nano, but before, it will ask you if you want to save the file. Enter `y` and click enter to save the file under the name muts_energ.tab. Confirm the content of the file with cat:

```bash
$ cat muts_energ.tab
R209E	1.00
T78N	2.00
F248A	1.50
```

- This example file is representative of a list of mutations as it might come from some other program or as you noted them down in an excel list or anywhere else. The numbers in the second column could mean for example the energy that a program calculated for this mutation. We will use this example file to understand one of the most important features of bash scripting, which is called "piping".
- Piping is the process of taking the output of one command and handing it over to a second command. For example, if we would want to use this list of mutations and sort them, we could combine the cat command, with the sort command, and we connect them with a pipe `|`:

```bash
$ cat muts_energ.tab | sort
T78N	2.00
F248A	1.50
R209E	1.00
```

- Now we didn't simply print the content of the file, but we sent it to sort, which then sorted the list alphabetically, and then printed it. If we would like to sort the list by the second column, which contains the number, we can adjust the sort command with `-k`. For numbers, alphabetic sorting gives undesired results, like 11 coming before 2. Therefore we can sort numerically with the `-n` option.

```bash
$ cat muts_energ.tab | sort -n -k2
R209E	1.00
F248A	1.50
T78N	2.00
```

- You can pipe as many times as you want. If, for example, we were now only interested in the mutation with the highest energy difference, and scrap the rest of the list, we can pipe the output of sort to tail, which can be used to print only the last line:

```bash
$ cat muts_energ.tab | sort -n -k2 | tail -1
T78N	2.00
```

- If we wanted to sort this list by the residue number, though, we would face a problem: the sort command cannot be told directly to ignore the first and last character of this first column entry and only use the number in between. What we could do is to separate the amino acid letter and the residue number by tabs. This kind of text processing is easily done in bash with the help of [Regular Expressions](https://www.regular-expressions.info/) (Regexp).
- A Regexp is a text string that describes a text search pattern. This is an advanced way of doing wildcard (placeholder) search patterns, as for example `*.txt` in many programs (and also in bash), which will match any file that ends in `.txt`. In this case, the asterisk `*` matches any text. In bash, you can also use the `?`, which will match any character, but only one at a time. So, `?.txt` will match e.g. A.txt or Z.txt, but not test.txt. Regexp is the same, only that it has many more options.
- In bash, many commands understand regexp. One of the most famous ones, is sed. Everything within the sed command has to be enclosed in single quotes, because regexp uses so many special characters that otherwise would be interpreted by bash differently. The most common use of sed, is to replace a string that we match, with a new string, and the match and the substitution are separated by a slash `/`. The command for substitution is `s`, and comes at the start. sed usually uses a file as input, but we can also pipe the output of echo to it:

```bash
$ echo A_simple_example | sed 's/_/ /'
A simple_example
```

- Here we told to program to find the literal character `_` and substitute it with a space. As you can see, the sed command only replaced the first occurrence of the match. If we wanted to replace all matches, we can add the g option to the end of the command:

```bash
$ echo A_simple_example | sed 's/_/ /g'
A simple example
```

- The replacement can also be nothing, that way you can delete matches:

```bash
$ echo A_simple_example | sed 's/_//g'
Asimpleexample
```

- You can of course also match more than one letter

```bash
$ echo A_simple_example | sed 's/ple//g'
A_sim_exam
```

- Regexp has many options to match non-literally. With "character classes", rather than matching one literal character, you can match a selection of characters. Use square brackets for this type of match:

```bash
$ echo A_simple_example | sed 's/[aeiou]m//g'
A_sple_exple
```

- Here we matched any vowel followed by an m, which matched im and am. Note that regexps are case sensitive:

```bash
$ echo A_simple_example | sed 's/[A-Z]/The/g'
The_simple_example
```

- Here we used a range of characters, all characters from A to Z that are upper case.
- Another useful feature is called capturing. Since this was introduced later in regexp development, this extended syntax is not included by default in sed. We need to activate it using sed's `-E` option. Then we can use round brackets to store the match. We can access the saved match in the substitution using the backslash `\` followed by the group number (since you can use more than one group).

```bash
$ echo A_simple_example | sed -E 's/([A-Z])/Such_\1/g'
Such_A_simple_example
```

- Back to our example file with mutations. To introduce a tab character after the amino acid, which is at the first character position, we will use a regexp that matches the first character. Since it is such a common thing to be interested in matching the beginning of a line, regexp has a simple shortcut character for this: `^`. `^` is a so-called anchor; it does not match a character itself, but a position. In this case, it matches directly before the first character. To match the first character, we need now a wildcard (placeholder) to match the character that follows `^`. In regexp, we can use the dot `.` to match any character: `^.` matches the R in R209E, and the T in T78N. If we add another dot, we would match the first two characters: `^..` matches F2 of F248A.
- Here comes your first exercise: with all of the above learned, try to come up with a regexp that you use in a sed command, so that you insert a tab after the first character of the mutation. My solution is written below, click on the arrow reveal it.

<details>
<summary>Solution</summary>

```bash
$ echo F248A | sed -E 's/^([A-Z])/\1	/'
```

</details>


```bash
F	248A
```

- If you now use cat to pipe the entire file to sed, you will see that it will change every line. Can you guess the command sequence?

<details>
<summary>Solution</summary>

```bash
$ cat muts_energ.tab | sed -E 's/^([A-Z])/\1	/'
```

</details>

- Having introduced successfully a tab after the original amino acid, we now want to introduce also a tab after the replacement amino acid. We can expand our regexp to match the entire pattern of the three parts letter, number, letter, save each part separately in a group, and then substitute with the three groups separated by tabs. We need to use one additional trick to make this work, which has to do with repetition of characters. The amino acid number of our protein can be between 1-542. In regex, this cannot be matched directly with `[1-542]`, which would actually match a single digit between 1 and 5, and additionally the digits 4 and 2 (which were already included). This inconvenience is due to regexps being primarily made for text and not for number processing. We can work around in two ways: we could use repetition, either with the plus `+`, which means "match at least once": `[0-9]+`, or with exact numbers in curly brackets, where `[0-9]{1,3}` matches a digit repeated between one and three times. Alternatively we can use the question mark `?` to make a match optional, in which case we could use `[0-9][0-9]?[0-9]?` to match at least one digit, possibly a second, and possibly a third digit. Thus we have three possible solutions:

<details>
<summary>Solution</summary>

```bash
$ cat muts_energ.tab | sed -E 's/^([A-Z])([0-9]+)([A-Z])/\1    \2      \3/'
$ cat muts_energ.tab | sed -E 's/^([A-Z])([0-9]{1,3})([A-Z])/\1    \2      \3/'
$ cat muts_energ.tab | sed -E 's/^([A-Z])([0-9][0-9]?[0-9]?)([A-Z])/\1    \2      \3/'
```

</details>

- So… Why did we do all this again? Because now we can sort the file by residue number:

<details>
<summary>Solution</summary>

```bash
$ cat muts_energ.tab | sed -E 's/^([A-Z])([0-9]+)([A-Z])/\1    \2      \3/' | sort -n -k2
```

</details>


```bash
T	78	N	2.00
R	209	E	1.00
F	248	A	1.50
```

- Now lets save this output to a file:

<details>
<summary>Solution</summary>

```bash
$ cat muts_energ.tab | sed -E 's/^([A-Z])([0-9]+)([A-Z])/\1    \2      \3/' | sort -n -k2 > muts_energ2.tab
```

</details>


Congrats, you mastered regular expressions! You have now a powerful tool at hand to change text files.

- We need to make two final adjustments to our mutations file so it becomes useful to YASARA. Inconveniently, YASARA prefers .tab files that are not separated by actual tabs, but by multiple spaces. We could use sed and regexp again, but there is also a simple bash command to replace a character with another, called `tr`:

```bash
$ cat muts_energ2.tab | tr '\t' ' '
```

- This will replace all tabs with a space. Instead of \t we can also write a literal tab, both works. The last adjustment is to get rid of the energies. In YASARA, we are only interested in the mutations. We can use `cut` to divide a string into fragments separated by a delimiter (which is now the space that we just introduced), specified by the `-d` option followed by the delimiter in quotes. We can limit the output now to one or some of the fragments with -f, followed by a number or a range:

```bash
$ cat muts_energ2.tab | tr '\t' ' ' | cut -d ' ' -f1-3 > mutations.tab
```


### Scripts are consecutive commands in a text file

- You know now how to use the bash command line interactively. You type a command, and the result gets printed to the screen, unless you redirect to a file. You can pipe the output of one command to the other. If you wanted to apply the procedure that we just went through to your own mutations, you can now simply create a file similar to the one of the example, and apply the same commands one by one. Alternatively, you can combine them in a script.
- Use nano to create a new file, called script.sh (sh stands for shell, another name for terminal)

```bash
$ nano scripts.sh
```

- In nano, type the following characters: `#!/usr/bin/env bash` and close and save via CTRL+X, y and enter.
- This line is called a shebang and always has to come at the beginning of a script. It tells the terminal that this is a script file, and gives the path to the program used to interpret the script. In our case, we provided the path to bash, but there are also other shells that are in use. Are you on MacOS and upgraded the built-in bash to a newer version (highly recommended?). Then you may want to specify that new bash path in the shebang. Did you installed via [homebrew](https://brew.sh/) (do install this! it should become your default package manager to install software on MacOS) but forgot where it ended up? Use the `which` command to see where a program lives:

```bash
$ which bash
/opt/homebrew/bin/bash
```

- Now open the file in a new terminal window. If you click CMD+N, you open a new terminal window. Navigate to the exercise folder, and use nano to open the file. In the original terminal window, you can now use the arrow keys to go through all the commands we used. Try to find the ones that we eventually used and on the file with the mutations, and copy and paste them into the other terminal window with nano. Does your script file look the same as mine?

<details>
<summary>Solution</summary>

```bash
$ #!/usr/bin/env bash

cat muts_energ.tab | sed -E 's/^([A-Z])([0-9]+)([A-Z])/\1       \2      \3/' | sort -n -k2 > muts_energ2.tab
cat muts_energ2.tab | tr '\t' ' ' | cut -d ' ' -f1-3 > mutations.tab
```

</details>

- Now beware of one thing: copy pasting works quite well in the terminal, with the exception of tabs. If you go with the cursor to the part of the command where we substitute with tabs, you will notice that they were replaced by multiple spaces. Delete the spaces and add the tab again (in nano you don't need CTRL+V, you can simply press <TAB>)
- Save the file. Now we need to do one last thing to make this script file ready: we need to make it executable. For that we use the `chmod` command, which can give the script file the corresponding permission with the `-x` option.

```bash
$ chmod +x script.sh
```

- If you type ls, you might see that the color of the script file has changed. To execute the script, we simply type the path of the file in the terminal. Use the `.` as a placeholder for the current directory:

```bash
$ ./script.sh
```

- You will not see any result on the screen, because the commands in the script redirect their ouput to the files. To see that the script actually works, move the script file and the mutations file to the scripts folder, go into this folder, use ls to see the files, execute the script, and use ls again to see the new files. Use cat to make sure the file contains the expected output.

<details>
<summary>Solution</summary>

```bash
$ mv script.sh scripts
$ mv muts_energ.tab scripts
$ cd scripts
$ ls
muts_energ.tab script.sh
$ ./scripts.sh
$ ls
mutations.tab   muts_energ.tab  muts_energ2.tab script.sh
```

</details>


```bash
$ cat mutations.tab
T 78 N
R 209 E
F 248 A
```


### YASARA means Yet Another Scientific Artificial Reality Application

- We now turn to YASARA. It should be installed on your system. Find out where it is using `find`:

```bash
$ cd ~
$ find . -name 'yasara.app'
./Software/YASARA.app/Contents/MacOS/yasara.app
```

- Create a shortcut to the yasara application in a special file called `.bashrc`. This file is always in `~`, but it might not exist on your system, in which case you will create it. It is a hidden file, defined by the preceeding `.`. Note this is a different dot than the one that defines the current directory, so a hidden file in the current directory could be `./.hidden_file`. The bashrc contains settings for the command line, and you can add lines to the file that will be executed every time you open the terminal.  Caution: obviously, you will need to change the path in the command below, with the correct path to yasara, as the find command above told you. Don't forget to replace the `.` with `~`. On a new system, the bashrc might not be automatically sourced, check the file that definitely auto-sources, `~/.bash_profile`. You may have to add a specific instructions to source bashrc in here:
```
$ cat ~/.bash_profile
#! /bin/bash
source ~/.bashrc
```
If yours is empty, add the above command. In bashrc, you can now add an alias for yasara:

```bash
$ echo "alias yasara='~/Software/YASARA.app/Contents/MacOS/yasara.app'" >> ~/.bashrc
```

- Mind the quotation marks: the echo command uses quotation marks as a sign to take special characters literally and doesn't usually print them. Since we actually want the line in the file to contain the single quotes, we need to enclose the entire string with double quotes:

```bash
$ echo ~
/Users/max
$ echo '~'
~
$ echo "~"
~
$ echo "'~'"
'~'
$ echo '"~"'
"~"
```

- Now that you added the yasara shortcut to .bashrc, you can use the yasara command after you open a new terminal. For the currently open terminal, we still need to reload the .bashrc file.

```bash
$ source ~/.bashrc
```

- Try to run yasara (and be aware that you can use TAB completion also for commands: type yas<tab> and it should auto complete to yasara)

```bash
$ yasara
```

- This will open up YASARA. While it is open, go back to the terminal. And try typing something. You will notice that there is no $ and whatever you type has no effect. That is because the terminal is still busy with the current command, which was yasara. To prevent this effect, you can use the `&` sign, to start a process in the background. Close the YASARA window and go to the terminal. You will see the `$` sign again.

```bash
$ yasara &
```

- Now YASARA will open up again, but the terminal remains functional. If you forget to use &, you can use a trick: go to the frozen terminal and press CTRL+Z. Now the terminal works, however, YASARA is frozen. If you now type `bg` in the terminal, you will send YASARA to a background task, and both the terminal and YASARA are active.
- Fire up YASARA from the terminal.

```bash
$ yasara &
```

- In YASARA, you have access to a command line as well, if you press the space bar. Although it is not a bash command line, some commands work the same:

```bash
$ pwd
/Users/max/university/exercise
$ cd pd<tab>
$ cd pdbs
```

- On the other hand, you now have access to all the commands of YASARA. You can find out about the commands in several ways: you can go to the user manual by clicking in the menu help>show user manual, or with Help>Search User Manual to search for a keyword/topic, or with help> show command docs if you know the command and want to know details. There are also very helpful and interactive movies that you can watch, which are a great way of learning the basics.
- Now click on the search option and type `pdb`. Searching takes a while and then a browser window with results opens. If you click on the entry `PDB files` you will see a list of associated commands. Click on LoadPDB, and you will see the help file for this command. At the top, you see the various ways of accessing this command. At the top (the entry `Format`), you see the syntax for the command in the command line. Below, you see how you can access the command from the menu. The various options of the command, called arguments in case of specifying them on the command line, are explained in the section below, and if you scroll to the bottom of the help file you see various examples.
- Go back to YASARA and use the command from the menu: File>Load>PDB File. Since we went to the pdbs folder earlier, you should now see the 4RG3.pdb file there. Double click to open it. You now see the protein in its most realistic representation: all atoms are shown as balls, which roughly correspond to the size they actually occupy. However, because there are so many atoms, we prefer to not actually see all of them and are used to other representations. Using the F keys, you have a quick access to change the style. Click All the F keys from F1 to F8 to see how the style changes from showing all atoms in various ways to showing the secondary structure in different styles. Now press first F2 and then F6, and try to zoom in on the center of the protein by keeping the right mouse key pressed and moving the mouse up and down. Rotate the scene with the left mouse key, and move the protein with the middle mouse key pressed. In this way, try to zoom in on the flavin cofactor of the protein. Try to find the typical three ringed isoalloxazine ring and zoom in on it at the center of the screen. Leftclick on one of the atoms and see how it is now marked. Now rightclick on the marked atom and click Color>Residue. Chose Yellow and click OK.
- Now open YASARA's command line with space. You will see a lot of text now, that wasn't there before. If you click space again, the command line becomes bigger. You can scroll up to the top, of the command line, where you will first see some messages that YASARA prints on start up, and then the first commands you typed: `pwd` and `cd`. Below that, you also see all the commands that correspond to your mouse clicking on the menu: `LoadPDB`, `Style`, and at lastly `ColorRes`. This is a very useful feature of YASARA: whatever you click on the menu, will appear in the command line afterwards. That is a simple way of learning the commands, and the next time you might just type the command instead of searching it in the menu.


## Part 2: Simulations


### Basic YASARA commands and swapping a residue

If we want to have a look at how the mutations affect the protein structure, we can simulate the mutant structure *in silico* using YASARA. One rule in computational chemistry is, that there is *always* a more precise way of modeling. That means, that there are many methods to simulate reality, and as they differ in complexity they differ in predictive accuracy.
- A very simple way of creating the mutant, is by exchanging the amino acid manually. In YASARA, a single command is sufficient to achieve this, it's called `SwapRes`. As before with the Color command, the second part is `Res` and this is called the final selection unit. Many commands end in this way, and the unit can be All (apply to everything), Obj (apply to an object), Mol (Molecule), Res, or Atom. An Object is usually an entire protein, and this is divided into molecules, which can be different protein subunits, or ligands bound to the protein. The residues are amino acids, waters, or ligands. The exact selection usually follows right after the command. If you check the history of the Color command, you see that the command you executed via the right click menu was ColorRes Atom 4095, which translates to: color the residue which contains atom number 4095.
- All atoms in YASARA are numbered consecutively starting from 1, and you can see the number of a left mouse click marked atom in the left `HUD` (head up display), which is the text in the top left menu under the title ATOM PROPERTIES. Besides atom number, also the atom name, residue number and name and object number and name are given, plus some more information. The top right hud shows the `soup`, i.e. all the atoms, and their organization into objects, molecules, and residues. If you click on 4RG3 in the object list, it will expand the sublist of molecules, and if you click on the first molecule you can see all the residues. The residues also appear in a separate menu, which is at the bottom of the window and only appears if you move the mouse there. Scroll to the right until you find the residue `NAP`, which is the NADP (residue names can only have three letters). If you left click on it, it will be marked. If you click on it while holding CTRL, you will zoom in.
- If you do this and check the command line, you'll see the corresponding command, ZoomAtom 4119, which a random atom of the cofactor. If you want to zoom to see the entire residue, you have to change the final selection to `Res`, and afterwards provide the name of the residue:

```yasara
> ZoomRes NAP
```

- Now let's look at some of the protein residues. If you are in the style mode F6, you don't see amino acids. Locate the first amino acid of our example mutation find, threonine 78, in the bottom residue menu and CTRL+click it. You see an atom is marked, but you can't actually see the atom. Rightclick on the marked spot and select Show>Residue. In the command line you see `ShowRes Atom 560`, but you could also have written `ShowRes Thr 78`.
- Let's hide the secondary structure, to make things a bit more clear. If you don't know the command, you can use the searchdoc command

```yasara
> SearchDoc secondary structure
```

- The third entry is about Hiding the secondary structure, and if you click it, you will learn how to use the command.

```yasara
> HideSecStrAll
```

- Now all that's left showing is the cofactors, a ligand, and the threonine 78. In this representation, it is difficult to understand this threonine: what is the side-chain, and what is the backbone? (The backbone is the amine and carboxyl group of amino acids). To make this easier to see, it helps to show the two neighbouring residues. Since we don't know by heart which amino acids are residues 77 and 79, it is convenient that it is enough to specify only the number, and if we write both numbers behind each other separated by space, the selection is for both:

```yasara
> ShowRes 77 79
```

- Although sometimes, this selection can include more than intended. Water molecules in the structure can have a number that starts from 1 again, in that case the above command would also show them. We could prevent this by telling yasara to only show residues 77 and 79 that are part of the protein:

```yasara
> ShowRes protein Res 77 79
```

- Note that you have to insert the keyword `Res` again, if you omit it, it means `and`, and you would show all protein residues and residues 77 and 79:

```yasara
> ShowRes protein 77 79
```

- If you type this and want to undo your action, you can either type `undo` in the command line, press u on the keyboard (while the command line is closed) or use the undo button in the top menu.
- With three consecutive residues shown, things become slightly clearer. It gets even clearer if you style the backbone atoms different from the side-chain atoms:

```yasara
> ColorAtom backbone, white
```

- Here we use the color command, use Atoms as selection unit, chose the keyword backbone to select all backbone atoms, and provide as a second argument, separated by comma, the color white. In this way we clearly see what is the side-chain of our threonine 78. If we now show the secondary structure again, we see how our color command affected the entire protein

```yasara
> ShowSecStrAll
```

- Now the entire protein is white, which is because the secondary structure inherits the color of the C alpha atom. If you want to know which one is the C alpha atom, it is handy to mark it in a different style – instead of the sticks style used at the moment for amino acids, we can style them as ball-sticks, such as the cofactors currently look, by using the `BallStick` command and temporarily removing the secondary structure:

```yasara
> HideSecStrAll
> BallStickAtom CA
> ShowSecStrAll
```

- You might notice that our threonine seems to be quite at the outside of the protein. If we want to know if it is definitely at the very surface of the protein, we can use the `ShowSurf` command to visualize it. Check out `Help ShowSurf` to see all the info's on this command, and also watch the help movie dealing with surfaces. Importantly, we need to select a surface type, in this case we go for the type `molecular`:

```yasara
> ShowSurfAll molecular
```

- The surface color is now determined by the color of the atom that creates this surface. Do You wonder what the bright red spots are? Those are water molecules that surround our protein crystal. Since they aren't actually part of the protein, we better exclude them from the surface analysis. To achieve this, we have to come up with a better selection then simply select "All":

```yasara
> HideSurfAll
> ShowSurfRes protein, molecular
```

- Now we only showed the surface of amino acids. If you turn the protein around, you will see how also the co-factors lie now on top of the protein surface in the tunnels towards the protein's inside. Now what about the threonine? An easy way of seeing if it creates a surface is by giving it a distinct color, which will be inherited by the surface:

```yasara
> ColorRes 78, magenta
```

- If you look around, you will now see how a pink blob sticks out of the surface. That is our indeed surface exposed threonine 78. Go ahead and try the same with the other two example residues. Where are they located? Are they surface exposed or buried in the protein's interior?
- Now how do we mutate the residue? As mentioned with the `SwapRes` command. Before applying it, we will make a backup of the original structure using the `Duplicate` command:

```yasara
> DuplicateObj 1
```

- Now we have two identical objects. One of them we will edit, while the other is going to be "turned off" until we use it again:

```yasara
> RemoveObj 1
```

- From now on, we always have to specify on which object we want to do our changes. Since we temporarily removed object 1, we will do our edits on object 2. Ready to do `SwapRes`:

```yasara
> SwapRes Obj 2 Res 78, Asn
```

- This command requires the three letter code of the amino acid, N won't work. If you didn't see the change clearly, undo and hide surface and secondary structure. You can also color the residue by element again to see the nitrogens and oxygens with their default blue and red color:

```yasara
> Undo
> HideSurfObj 2
> HideSecStrObj 2
> ColorRes Obj 2 res protein, element
> SwapRes Obj 2 Res 78, Asn
```

- Use `Undo` and `Redo` to see how the side-chain switches.


### Simulation boxes, optimizations and energy minimizations

Simply swappping the residue is not a very good simulation of reality. The SwapRes command only exchanges the atoms, and doesn't consider whether the orientation of the residue is energetically favorable and thus realistic. This functionality is provided by another command, called `OptimizeRes`.
- Before we can apply this command, we need to prepare our structure a little bit. PDB files commonly have lots of problems that make them unsuitable for direct use in simulations. First of all, the resolution of protein crystals is nearly never high enough to see hydrogen atoms, thus they are usually not present in the pdb structure and have to be added manually, according to basic chemistry rules. Another common problem is alternative side-chain conformations: if an amino acid has two similarly favorable orientations, protein crystals will contain a mix of proteins with one or the other conformation and there will be electron density for both. PDB files contain both orientations, and a decision has to be made on which one to keep, before starting a simulation. Many other problems are explained in the `Clean` command help file. Applying clean, you will immediately see the changes:

```yasara
> Clean
```

- We will also apply a second command, OptHydAll. The need for this again lies in the uncertainty caused by electron density maps obtained from protein crystallography: the amide group of asparagine and glutamine side-chains can be rotated by 180 degrees with almost no impact on the electron density. The most reliable assignment is made by analyzing the hydrogen bonds that can be obtained in the two possible orientations. The OptHydAll command performs this kind of analysis for all side chains at once, and also assigns correct charges to charged amino acids according to the currently set pH (7.4 by default).

```yasara
> OptHydAll
```

- As you can see, the side-chain of our mutated asparagine switched. Now we are ready to optimize the residue. You can read details in the help file, but briefly explained, this command chooses a side-chain conformation from a library of rotamers (which are orientations of the amino acid side-chain that are known to exist), and then calculates energies associated with the orientations, by taking into account all the physical forces that act on the atoms, which include van der Waals forces and electrostatics.

```yasara
> OptimizeRes obj 2 res 78, scwall
```

- Now the residue looks like different from its original position when we used SwapRes. You can compare by adding to the scene again the first object that we duplicated and temporarily removed if you style the scene for better comparison:

```yasara
> AddObj 1
> HideSurfAll
> HideSecStrAll
```

- Now you see the original residue in pink, overlaid with the mutated residue. You can see how the mutated residue quite closely matches the original crystal structure orientation. In the top right HUD, you can click on `Yes` in the column `Vis` (for visible) to switch on and off object 2, to better compare.
- You also might have noticed that there is a new Object 3, called SimCELL, and you can see that it is a box surrounding the entire protein if you zoom out a bit. This simulation cell was created by YASARA because it strives to confine the space that it simulates. By creating a box around the protein, you define all simulations to the inside of that box, and we are going to need that for the next step. Now YASARA created the cell during the optimization, but you can also add it manually. First delete the existing one:

```yasara
> DelOBJ SimCELL
```

- Then check `help Cell` to see an explanation for the parameters and use this command:

```yasara
> Cell Auto, Extension=5.0,Shape=Cube
```

- Simulation cells can have either periodic boundaries or fixed walls, in the former case, molecules that hit a wall of the box, simply appear at the opposite wall. That gives the illusion of an unconfined space and we will need this behavior for the simulation:

```yasara
> Boundary periodic
```

- The next step is an energy minimization. In a crystal structure, the atoms are arranged in a conformation that is suitable for the crystal environment. However, a protein in its natural environment, which is water, will have a slightly different conformation. All atoms are of course constantly moving due to Brownian motion, but there are certain energetic minima in the motion landscape that is explored. An energy minimization is a simulation that forces the structure in such a minima, and this can be considered a better representation of the real structure of a protein than a crystal structure. Before doing this, we need to simulate the natural environment, and add water molecules to our scene. We will fill only the simulation box with water, to not increase the size of the system too much. First we remove Object 1 again:

```yasara
> RemoveObj 1
```

- Then we need to decide on a force field. That is basically the algorithm that is used to calculate the physical forces acting on the protein. There are several variants available, all with slight differences and varying accuracy. You can read more about it under `help ForceField`. We will choose the latest Amber force field, which is well established and frequently used in protein simulations.

```yasara
> ForceField Amber14
```

- Then use the menu to access a predefined YASARA script that adds the water, and also adds some salt ions to the scene, that counter the charge of the protein.
- Use the menu to navigate to Options>Choose experiment>Cell neutralization and pKA prediction
- Select Na and Cl as the two ions, and click OK.
- While the experiment is running, you will see matches appear on the screen. In order to see what is going on, open the console after you started the experiment and show the otherwise hidden water molecules:

```yasara
> ShowRes HOH
```

- You can watch while YASARA is simulating a realistic water environment, and you can read details about it under `help Experiment`.
- Then we can perform the actual energy minimization. Again go to the menu and chose Options>Choose experiment>Energy minimization. In order to better see whats happening now, style the scene with F6 and then F8 to see secondary structures and the protein atoms. You can switch on and off the water molecules in the top right HUD.
- Now we save our energy minimized mutant structure in the folder for the simulations:

```yasara
> CD ../simulations/
> SavePDB OBj 2, T78N_4RG3_minimized.pdb
```

- Then we can delete the mutant, the cell and the water, by deleting all objects except object 1:

```yasara
> DeleteObj !1
```

- Next we perform the same steps on the wild type as we performed on the mutant. To make our life easier, we can write a small script. Go to bash and `nano` a text file called minimization.mcr (mcr stands for macro and is the default file extension for YASARA scripts). For YASARA scripts you don't need a shebang. You can, however, add a comment line at the beginning to explain what this script does. As in bash, comments are any line that starts with `#`. Below that, copy and paste all the relevant commands for the energy minimization. If we accessed the command through the menu, go to the command line to see what the resulting command looked like. The experiment is actually a collection of commands. They start with the `Experiment` command, and then some subcommands follow, which are preceded by `ExpData>` and that have an indent of two spaces. Make sure to include all commands, and to also include the two spaces before the subcommands, but not the `ExpData`. After the experiment, you need to add a `Wait ExpEnd` command, to make sure the script doesn't continue before the simulation is done:

```yasara
# This is a script to energy minimize a structure.

# Preparing the structure
Clean
OptHydAll

#Preparing simulations
Cell Auto, Extension=5.0,Shape=Cube
Boundary periodic
ForceField Amber14

#Simulate water environment
Experiment Neutralization
  Speed Fast
  WaterDensity 0.997
  pH 7.4
  Ions Massfraction=0.9
  Experiment On
Wait ExpEnd

#Energy minimization
Experiment Minimization
  Experiment On
Wait ExpEnd
```

- It will take a while again; you will notice that it is finished when there is no message is shown anymore. To speed up the process, you can hide the secondary structure. YASARA will then not use resources to predict the secondary structure at each time step of the simulation.
- Now we can save also this structure:

```yasara
> SavePDB Obj 1, /Users/max/university/exercise/pdbs/WT_4RG3_minimized.pdb
```


Congrats, you created a realistic model of your mutant structure and the wild type using molecular simulations.

## Part 3: Visualization

Now we want to visualize the changes that we made in an appealing way, so we can immediately inspect the result in a productive manner.
- First lets make an overlay of wild type and mutant. First clear everything:

```yasara
> Clear
```

- And then we load both structures on top of each other:

```yasara
> LoadPDB WT_4RG3_minimized
> LoadPDB T78N_4RG3_minimized
```

- Press F6 or type `Style Ribbon`.
- Show the sidechain and C alpha atoms of residue 78 for both objects, zoom in, and hide hydrogens

```yasara
> ShowAtom Res 78 atom sidechain CA
> ZoomRes 78
> HideAtom element H
```

- Now give some colors to your liking, (you can use the rainbow colors 1 till 360 or [html colors](https://htmlcolorcodes.com/)):

```yasara
> ColorAtom obj 1 element C, 260
> ColorAtom obj 2 element C, 310
```

- Now turn the protein around until you are satisfied with the position. To facilitate this, make the CA atom of the mutated residue the center of rotation:

```yasara
> CenterAtom obj 1 res 78 atom CA, coordsys=global
```

- Now everything is very close, move it away with the mouse or use a command:

```yasara
> MoveAll z=20
```

- Place a label with the mutation above the residue:

```yasara
> LabelRes obj 1 res 78, T78N, 0.6, black, y=2
```

- Finally take a raytraced picture. For that you need to make sure that RayTrace is installed. Go to Help>Install>Raytrace. If you don't see this option, it's installed already.

```yasara
> RayTrace T78N_4RG3_overlay, 1920,1080
```

- Now save this entire scene to a file, not only the atom coordinates as in the pdb file:

```yasara
> SaveSce T78N_4RG3_overlay
```

There are many more things you can do to optimize the visualization of your results. You can try to play around in YASARA to get to know all the commands and settings that you can change in order to create a visually attractive but also informative output.

## Part 4: Automation

Now that we have gone through the workflow for one mutation, we want to apply this procedure to all of the mutations in the list. This would be very laborious and inconvenient to do manually for each mutation, so the last part of this exercise deals with automation: how to apply a procedure that has been optimized for one, to many. To achieve this, we use a combination of bash and YASARA scripts.
- There are many possible solutions to this task, and whichever one you come up with, chances are there is a better solution. Better means that it could be written more concise (i.e. less lines of code) and more efficient (i.e. using less computational resources).
- In order to come close to an ideal solution, one has to think: which programs/commands do I need? Which program should do which task? How do I bind the steps together? And: Am I going to write a very specific script or a general one? The last question will determine both the amount of work for your current task (the more general, the more work), as well as for future task (the more general scripts you accumulate, the less work you will have in the future). If you are assuming that you write a script for a problem/task that you are likely to encounter again later, you might as well make the effort of writing a general script, that can be applied to a new task, without changing the entire code.
- In many cases you will start writing scripts and find that you have to achieve a step for which you have no solution at hand. For Bash, you will find all the answers with Google. Websites such as stackoverflow.com are an incredibly rich source of information, and if you phrase your search correctly, you will find the answer to your problem in less than a minute. For YASARA, you need to dig into the user manual. Use the SearchDoc command, or go to the main page of the User Manual, click of the headlines of entire chapters and use CTRL+F to look for keywords.
- We now already have some essential parts, one script in bash and one in YASARA. All we need to do is expand a little on that and glue them together, and luckily bash is a great programming language to write glue code.
- Let's start with creating a new script file. Let's give it a meaningful name:

```bash
$ cd /Users/max/university/exercise
$ nano Mutate_Minimize_Compare.sh
```

- Assuming that we are again starting from a file as in the example, the first step might remain the same as previously:

```bash
#!/usr/bin/env bash
cat muts_energ.tab | sed -E 's/^([A-Z])([0-9]+)([A-Z])/\1 \2 \3/' | sort -n -k2 > muts_energ2.tab
cat muts_energ2.tab | tr '\t' ' ' | cut -d ' ' -f1-3 > mutations.tab
```

- Th is will of course only work, if we have a file called muts_energ.tab, and we're facing the first question about generality. Do we want to let the script only work when there is a file with that name and fail in other cases? We have several ways of at least improving this a little bit. Firstly we could define a variable at the beginning of the script that contains the file name. Variable definitions at the script start are very common, because it is difficult to go through the entire code later to find all the filenames/strings/numbers that the code depends on in order for it to work. If we define those  dependencies at the beginning, they can be easily changed when a new task requires this.
- In bash, variables are defined through the variable name, followed by equals sign `=`, followed by the value, and without spaces. Try to give meaningful, but short names to variables.

```bash
mut_file=muts_energ.tab
```

- You can call the content of the variable with the dollar `$` sign:

```bash
$ echo $mut_file
muts_energ.tab
```

- It is, however, good practice to always quote variables in bash, which will prevent problems that occur if a variable value contains spaces, tabs or other special characters. Spaces into values can only be introduced, if the definition string is also quoted.

```bash
$ test=this	is	a	tab	string
-bash: is: command not found
$ test=" this	is	a	tab	string"
$ echo $test
this is a tab string
$ echo "$test"
this	is	a	tab	string
```

- The second question about the first line in the script is whether the format of the file will also always be the same. Our sed commands introduces the tabs in around the residue number, but only if it comes at the beginning of the line. If we had a file that started with the energy in the first column and has the residue in the second, it already wouldn't work anymore. We can slightly alter the sed command to extract only the mutation, wherever it is. Also I skip the `tr` command and introduce spaces instead of tabs with sed.

```bash
#!/usr/bin/env bash

#Define important variables here
mut_file=muts_energ.tab

#Extract a list of mutations from an input file
cat $mut_file | sed -E 's/.*([A-Z])([0-9]+)([A-Z]).*/\1 \2 \3/' | sort -n -k2 > mutations.tab
```

- The `.*` at the start and end now consumes all the characters that come before and after my match, so when I substitute with only the capturing groups, this will all disappear and what's left is only my matched mutation. Therefore there is also no need for the `cut` command to extract only the columns 1-3, because we already have only the match that we want. Also you noticed that commented the lines, starting with #. It is good practice to comment your scripts so you will later understand what they do. Even though the code readability suffers, try to comment as much as possible.
- Now that we have a list of mutations, we need to apply a procedure to each of them. In order to cycle through a list and apply the same commands, we will use one of the most important concepts in scripting: a loop. Loops exist in probably every programming language, and there might be several ways to create them. In bash, we have the two very common variants `for` and `while`. `For` will cycle through a list of items, and `while` will cycle as long as a condition is true.
- For works like this:

```bash
$ for char in a b c; do echo $char; done
a
b
c
```

- Usually you define a variable, right after for (char in this case) that will then adopt each value of the list that you provide, starting from the first and cycling through until the last. For will execute all commands that are enclosed by `; do` and `; done` through. The semicolon `;` is equivalent to a new line. Thus the above command is the same as typing:

```bash
$ for char in a b c
>    do echo $char
>  done
```

- If you write on the command line it is common to use ; if you write a script it is more common to put every command on a separate line.
- The `>` will appear in bash if you hit enter while your command is not finished. It also appears for example if you don't close parentheses or quotes:

```bash
$ echo "test
> "
test
```

- The second loop construct while has a different syntax, because it depends on a condition. A condition is a logical operation that can evaluate to `TRUE` or `FALSE`. In bash, conditions are enclosed in square brackets, and the logical operators are explained [here](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html). In this link, the conditions are explained in combination with the `if` command, another very basic concept in scripting. Syntax aside, understanding `if` is very easy:

```bash
$ if [ "a" == "a" ]; then echo yes; fi
yes
```

- Here instead of the `do` `done` enclosure, we have to use `then` `fi` (`fi` is `if` backwards). The double equals sign `==` is reserved for text, if we want to compare numbers we have to switch to `-eq` (equal), and `-gt` (greater than) or `-lt` (less than):

```bash
$ if [ 2 -gt 1 ]; then echo yes; fi
yes
```

- In the while loop we don't need if, the condition is indicated by the square brackets. Because it's easier to understand on several lines, I will write the following while loop as a script. In that case it is always good to indent code, i.e. use spaces to indicate stuff that belongs together:

```bash
#!/usr/bin/env bash

i=1
while [ $i -lt 3 ]; do
  echo $i;
  i=$[$i+1]
done
```

- If I `chmod +x` this script and execute it:

```bash
$ ./script
1
2
3
```

- That works only because I have the additional `i=$[$i+1]` command in the loop, which increments i in every cycle by 1. Now you also know that arithmetic in bash can be performed with the `$[]` syntax.
- Alright, how do we now loop through our mutations? We need one more command, which is `read`. `read` will assign the value coming from the standard input to a variable. If you just execute the command by itself, the standard input is the keyboard; i.e. you have to type in the value yourself:

```bash
$ read variable
test
$ echo "$variable"
test
```

- You can also redirect the standard input to come from a file, by using `<`.

```bash
$ echo "some text" > test.txt
$ cat test.txt
some text
$ read var < test.txt
$ echo "$var"
some text
```

- If we now throw all this information together, we finally have the `while` loop we need to access all the lines of our mutations file:

```bash
$ while read line; do echo $line; done < mutations.tab
T 78 N
R 209 E
F 248 A
```

- `read` becomes `FALSE` at the end of the file, therefore the loop breaks by itself. The `<` descriptor has to come at the end of the loop and comes before a possible redirect of the standard output:

```bash
$ while read line; do echo $line; done < mutations.tab > copy_of_mutations.tab
```

- This line can now be incorporated into our script:

```bash
#!/usr/bin/env bash

#Define important variables here
mut_file=muts_energ.tab

#Extract a list of mutations from an input file
cat $mut_file | sed -E 's/.*([A-Z])([0-9]+)([A-Z]).*/\1 \2 \3/' | sort -n -k2 > mutations.tab

#cycle through the list of mutations
while read line; do
  echo $line
done < mutations.tab
```

- Now that we have access to each individual line, we also can access each individual part of each line. For that we can use the `cut` command again. We can also assign each part to a variable, by using  the `$()`. It works like this:

```bash
$ test=$(echo "hello")
$ echo "$test"
hello
```

- And you can also use piping within the brackets:

```bash
$ test=$(echo "a,b,c,d" | cut -d ',' -f2-3)
$ echo "$test"
b,c
```

- We can incorporate this into our loop in the script (hidden for exercise):


<details>
<summary>Solution</summary>

```bash
while read line; do
  original=$(echo "$line" | cut -d ' ' -f1)
  residue=$(echo "$line" | cut -d ' ' -f2)
  mutation=$(echo "$line" | cut -d ' ' -f3)
done < mutations.tab
```

</details>
<br>
- Of course now instead of printing the mutations to the screen, we want to create them in YASARA. Instead of the `echo`, we want to invoke `yasara`, which then performs the mutation for us. So now it's time to work on our YASARA script.
- One essential part is already there. Above, we already created a script for the wild type that performs the structure preparation, and the cell neutralization and energy minimization experiments. We only need to add the mutation part, and the commands for that were already discussed above. The only question is, how we can let YASARA know which exactly is the mutation it has to perform. For that, we have two options: either we can provide this information from the command line when we invoke `yasara`, or we can let YASARA read our tab file. Since we need to provide some command line arguments anyway, we will opt for option one in this case.
- A command line argument for `yasara` is a way to define variables within YASARA. Within YASARA, variables have a different syntax than in bash. While in bash we called the variable with the dollar sign: `$variable`, in YASARA we call them by enclosing them in round brackets: `(variable)`. On the command line, the variables are simply provided after the file name, one after the other with `=`, enclosed in quotes. This only works, however, if we open a script file with YASARA.

```yasara
# This is a test YASARA script
print "Trying to print test_variable:"
print (test_variable)
```

- If we save this script as test.mcr and run yasara with the variable as command line argument

```bash
$ yasara /Users/max/university/exercise/test.mcr "test_variable=1"
```

- The YASARA output will be:

```yasara
Macro>print "Trying to print test_variable:"
Trying to print test_variable:
Macro>print 1
1
```

- Now let's include some variables in our minimization script to perform the mutation. A common (although not required) name for the file that we want to load is `MacroTarget`. The only other two pieces of information we need is the residue number and the mutated amino acid, I will call those variables `(residue)` and `(mutation)`. At the end of the script, I will also perform the overlay with the wild type structure and include the commands that we used in the visualization section, before exiting YASARA. Try to write the script yourself first, before you compare with my solution. Simply collect all the commands that we used above, and replace specific residue numbers with `(residue)`, and specific mutation amino acids with (mutation).

```yasara
# Script to mutate a residue, energy minimize the mutant, compare with WT and save a .png & .sce

# Load the structure
LoadPDB (MacroTarget)

# Preparing the structure
Clean
OptHydAll

#Swap and optimize the Residue
SwapRes Obj 1 Res (residue), (mutation)
OptimizeRes obj 1 res (residue), scwall

#Preparing simulations
Cell Auto, Extension=5.0,Shape=Cube
Boundary periodic
ForceField Amber14

#Simulate water environment
Experiment Neutralization
  Speed Fast
  WaterDensity 0.997
  pH 7.4
  Ions Massfraction=0.9
  Experiment On
Wait ExpEnd

#Energy minimization
Experiment Minimization
  Experiment On
Wait ExpEnd

SavePDB Obj 1, (residue)(mutation)_4RG3_minimized.pdb

#Load WT
Clear
LoadPDB (residue)(mutation)_4RG3_minimized.pdb
LoadPDB WT_4RG3_minimized.pdb

# Style the scene
Style Ribbon
ShowAtom Res (residue) atom sidechain CA
ZoomRes (residue)
HideAtom element H
ColorAtom obj 1 element C, 260
ColorAtom obj 2 element C, 310
CenterAtom obj 1 res (residue) atom CA, coordsys=global
MoveAll z=20
LabelRes obj 1 res (residue), (residue)(mutation), 0.6, black, y=2

#Save an Image and the scene
RayTrace (residue)(mutation)_4RG3_overlay, 1920,1080
SaveSce (residue)(mutation)_4RG3_overlay

#Exit YASARA
Exit
```

- We can test if this script works, by predefining the variables within the script at the very start. Simply include these three lines at the very beginning:

```yasara
MacroTarget='/Users/max/university/exercise/pdbs/4RG3'
residue=78
mutation='Asn'
```

- Save the script as Mutate_Minimize_Save.mcr, and invoke `yasara &` from the same folder where you saved the script. Now inspect the result of the .png or .sce file. If you are unhappy with the style and want to change some small commands in this section, you can temporarily "outcomment" the experiments part which takes a while to perform. Simply put a `#` before the neutralization and energy minimization experiment commands, rerun the script and adapt until you are happy with the result, and then remove the `#` again. Also remove the three variable definitions that we included for testing.
- Now we can include the YASARA script into our bash script. Because the SwapRes commands needs the three letter amino acid code, and our mutations file contains the amino acid in single letter code, we need to perform this substitution first. Even though this could be done in YASARA as well, we will do it within bash, where we can use `sed`, by connecting several substitutions with `;`.

```bash
$ cat mutations.tab | sed 's/G/Gly/;s/A/Ala/;s/V/Val/;s/L/Leu/;s/I/Ile/;s/M/Met/;s/P/Pro/;s/F/Phe/;s/W/Trp/;s/S/Ser/;s/T/Thr/;s/N/Asn/;s/Q/Gln/;s/Y/Tyr/;s/C/Cys/;s/K/Lys/;s/R/Arg/;s/H/His/;s/D/Asp/;s/E/Glu/' > mutations3.tab
```

- We can include that line after our previous `sed` and before the loop, just remember to also change the filename at the end of the loop. The MacroTarget should be defined as a variable at the beginning of the script as well.
- Next, we also include the `yasara` command with the variables in the loop instead of the `echo`. Within a script, our `yasara` alias is not known, and we need to define it again, or provide the full path to the `yasara` application (Beware, this makes the line so long that it is split on four lines in this document. You're supposed to keep this on one line, though)

```bash
#!/usr/bin/env bash

#Define important variables here
mut_file="/Users/max/university/exercise/muts_energ.tab"
macrotarget="/Users/max/university/exercise/pdbs/4RG3"

#Extract a list of mutations from an input file
cat $mut_file | sed -E 's/.*([A-Z])([0-9]+)([A-Z]).*/\1 \2 \3/' | sort -n -k2 > mutations.tab
cat mutations.tab | sed 's/G/Gly/;s/A/Ala/;s/V/Val/;s/L/Leu/;s/I/Ile/;s/M/Met/;s/P/Pro/;s/F/Phe/;s/W/Trp/;s/S/Ser/;s/T/Thr/;s/N/Asn/;s/Q/Gln/;s/Y/Tyr/;s/C/Cys/;s/K/Lys/;s/R/Arg/;s/H/His/;s/D/Asp/;s/E/Glu/' > mutations3.tab

#cycle through the list of mutations
while read line; do
  original=$(echo "$line" | cut -d ' ' -f1)
  residue=$(echo "$line" | cut -d ' ' -f2)
  mutation=$(echo "$line" | cut -d ' ' -f3)
  Users/max/Software/YASARA.app/Contents/MacOS/yasara.app'Mutate_Minimize_Save.mcr "MacroTarget='$macrotarget'" "mutation='$mutation'" "residue='$residue'"
done < mutations3.tab
```

- The very last step is to fine-tune a few details to avoid errors. For example, the loading of the WT is only working if it is in the same folder as yasara was started from.
- Similarly, we are currently saving the output to the same folder and better designate a proper output, by defining an output folder variable.
- Furthermore, since we're already collecting the information of the original residue in the bash script, we might as well pass it on to YASARA, so it can use it in file name.
- It's always good practice to clean up after yourself, so we can delete the temporary files at the end of the script.
- A YASARA script should contain a special command that handles errors, `OnError exit`
- In order to prevent computing power to be wasted in visualizing what YASARA does, we run YASARA in text mode by using the -txt command line option, which makes it much faster. This will prevent the YASARA window to open, and restrains everything to the command line instead. We can create a log file with yasara's output by using the `>` redirect.
- To further speed up things, the special command `Console OFF` in the YASARA script prevents YASARA to print the commands to the console, and just executes them without printing.
- Try to write the scripts yourself and then compare with my solutions below.

Mutate_Minimize_Compare.sh

```bash
#!/usr/bin/env bash

#Define important variables here
mut_file="/Users/max/university/exercise/muts_energ.tab"
macrotarget="/Users/max/university/exercise/pdbs/4RG3"
output="/Users/max/university/exercise/simulations"
wt="/Users/max/university/exercise/pdbs/WT_4RG3_minimized.pdb"

#Extract a list of mutations from an input file
cat $mut_file | sed -E 's/.*([A-Z])([0-9]+)([A-Z]).*/\1 \2 \3/' | sort -n -k2 > mutations.tab
cat mutations.tab | sed 's/G/Gly/;s/A/Ala/;s/V/Val/;s/L/Leu/;s/I/Ile/;s/M/Met/;s/P/Pro/;s/F/Phe/;s/W/Trp/;s/S/Ser/;s/T/Thr/;s/N/Asn/;s/Q/Gln/;s/Y/Tyr/;s/C/Cys/;s/K/Lys/;s/R/Arg/;s/H/His/;s/D/Asp/;s/E/Glu/' > mutations3.tab

#cycle through the list of mutations
while read line; do
  original=$(echo "$line" | cut -d ' ' -f1)
  residue=$(echo "$line" | cut -d ' ' -f2)
  mutation=$(echo "$line" | cut -d ' ' -f3)
  /Users/max/Software/YASARA.app/Contents/MacOS/yasara.app -txt Mutate_Minimize_Save.mcr "MacroTarget='$macrotarget'" "original='$original'" "mutation='$mutation'" "residue='$residue'" "output='$output'" "wt='$wt'" > mutation_log.txt
done < mutations3.tab
rm mutations.tab mutations3.tab
```

Mutate_Minimize_Save.mcr

```yasara
# This is a script to mutate a residue, energy minimize the structure, compare with the WT and save a picture plus the scene.

OnError Exit
Console OFF

# Load the structure
LoadPDB (MacroTarget)

# Preparing the structure
Clean
OptHydAll

#Swap and optimize the Residue
SwapRes Obj 1 Res (residue), (mutation)
OptimizeRes obj 1 res (residue), scwall

#Preparing simulations
Cell Auto, Extension=5.0,Shape=Cube
Boundary periodic
ForceField Amber14

#Simulate water environment
Experiment Neutralization
  Speed Fast
  WaterDensity 0.997
  pH 7.4
  Ions Massfraction=0.9
  Experiment On
Wait ExpEnd

#Energy minimization
Experiment Minimization
  Experiment On
Wait ExpEnd

SavePDB Obj 1, (output)/(original)(residue)(mutation)_4RG3_minimized.pdb

#Load WT
Clear
LoadPDB (output)/(original)(residue)(mutation)_4RG3_minimized.pdb
LoadPDB (wt)

# Style the scene
Style Ribbon
ShowAtom Res (residue) atom sidechain CA
ZoomRes (residue)
HideAtom element H
ColorAtom obj 1 element C, 260
ColorAtom obj 2 element C, 310
CenterAtom obj 1 res (residue) atom CA, coordsys=global
MoveAll z=20
LabelRes obj 1 res (residue), (original)(residue)(mutation), 0.6, black, y=2

#Save an Image and the scene
RayTrace (output)/(original)(residue)(mutation)_4RG3_overlay, 1920,1080
SaveSce (output)/(original)(residue)(mutation)_4RG3_overlay

#Exit YASARA
Exit
```


Congrats! You now know everything to become a computational biochemist. Try to apply this procedure to the mutations you are doing in the lab, and maybe you can get some clues about why the mutations are stabilizing (or not).
