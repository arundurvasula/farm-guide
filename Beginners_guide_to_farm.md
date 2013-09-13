#Beginner's guide to farm

##Contents:

###All about farm:
- What is farm
- Setting up your account
- Farm defaults
- Installing programs on farm
- Special farm things [scratch usage, tentakel]
- Submit jobs to farm 
    
###Unix computer tips:
- Bash 
- R basics [input/output especially, herefiles, pull from distributions, creating your own distribution, things people in the lab need]
- Python basics
- Git 
- Special Unix things [WriteFailed: Broken Pipe, convince people to learn awk, /dev/null]
- Contributing to the wiki
    
###All about farm:

####What is farm:

Farm is a computer cluster that runs a distribution of Linux called CentOS version 5.8. 

Farm has a **head node** , which controls the cluster, and **compute nodes** which is where the action happens. For the most part, you interact with Farm using scripts to launch jobs on the compute nodes; you don't run processes on the head node and you don't log into the compute nodes directly.

####Setting up your account:

In order to create an account on farm you need to send the CSE Help department your SSH public key. This key is a file created on your computer (not farm) that identifies your computer as your computer to farm. It's like a password that you don't need to type in. The process for generating your SSH public key is as follows:

#####Mac:

a. Open the terminal (Applications -> Utilities -> Terminal)
b. Generate your key:

		$ ssh-keygen -t rsa

c. Accept the default location by pressing Enter.

d. Type in a passphrase.

e. Your system will now generate a key pair:

		Your identification has been saved in /Users/myname/.ssh/id_rsa.
		Your public key has been saved in /Users/myname/.ssh/id_rsa.pub.
		The key fingerprint is:
		ae:89:72:0b:85:da:5a:f4:7c:1f:c2:43:fd:c6:44:38 myname@mymac.local
		The key's randomart image is:

		+--[ RSA 2048]----+
		|                 |
		|         .       |
		|        E .      |
		|   .   . o       |
		|  o . . S .      |
		| + + o . +       |
		|. + o = o +      |
		| o...o * o       |
		|.  oo.o .        |
		+-----------------+

**WARNING:** There are two keys generated: private and public. The public key is sent to the farm and the private key stays where it is. NEVER SHARE YOUR PRIVATE KEY. Any computer with your private key can log in to your account. Treat your private key like a password.

f. Your keys are located in the .ssh folder in your home directory:

		/Users/[username]/.ssh

g. Your public key is in a file called `id_rsa.pub`. Attach this file to the email you send to CSE Help.

#####Windows:
a. Follow the steps here:
<http://siteadmin.gforge.inria.fr/ssh_windows.html>


###Farm Defaults:
Farm comes with a lot of software preinstalled. Here's a list of some of the most commonly used ones:

* gcc
* Git
* libsequence
* Matlab
* Perl
* Python
* R

####Installing programs on farm:

#####C and C++:
When using farm, you're probably going to need to install some software. Since farm is a Linux computer, it follows much the same procedure as other Linux computers. The main difference is that you can't install things into the default directory. This is because it's located in `/bin` and you need root access in order to do that. The solution is to change where the binary file is located:

1. Download software:

		$ wget http://www.best-software-ever.com/thesis-in-a-box.tar.gz

2. Untar it:

		$ tar -xvzf thesis-in-a-box.tar.gz

3. Move to your new directory:

		$ cd thesis-in-a-box

4. Configure the program:

		$ ./configure --prefix=/home/user/[path for binary executable]/

5. Compile the program:
		
		$ make

6. Move it to the path you specified:
	
		$ make install

Note: this only works for software that is packaged to be installed this way. Always make sure to read the README or INSTALL files to ensure you're doing it right.

Then, to run the program just type in the path:

	$ /home/user/programs/thesis

Now, that's a lot to type every time you want to run a program. There are multiple better ways.

#####Alias:

Bash has something called aliases, which let you put your own shorter (usually) command to stand for another command. For example, I could create an alias for my thesis program called `graduate` which runs `/home/user/programs/thesis`. I could also create an alias for `ls -a` (show hidden files) called `la`. To do this, you need to create a file called `.bash_profile`

a. Make sure you're in the home directory

		$ cd

b. Create `.bash_profile`

		$ touch .bash_profile

c. Open it up in your favorite text editor
	
		$ nano .bash_profile

d. Create the aliases by typing this in:
	
		alias graduate="/home/user/programs/thesis"
		alias la="ls -a"

e. Save the file:
In nano this is control+O (WriteOut)

f. Reload bash by logging out or with this command:

		$ source ~/.bash_profile

#####Add to path:
When you type in a command into bash, bash looks for a binary file matching that command. The places it looks by default on farm are: 

	/share/apps/ge-6.2/bin/lx24-amd64
	/usr/kerberos/bin
	/usr/local/bin
	/bin
	/usr/bin

We can shorten the command we have to type by adding the binary file to the path. To do this, we add it to the `.bash_profile`.

a. Make sure you're in the home directory

		$ cd

b. Create `.bash_profile`

		$ touch .bash_profile

c. Open it up in your favorite text editor

		$ nano .bash_profile

d. Add to your path by typing this in:
		
		PATH=$PATH:/home/user/programs/
		export PATH

e. Save the file:
In nano this is control+O (WriteOut)

f. Reload bash by logging out or with this command:

		$ source ~/.bash_profile

This will add all binary files in `/home/user/programs/`including the one we want, `thesis`. To see what is in your path, type this into bash:

	$ echo $PATH

To add more folders to your path, just append it to the line with a colon in the beginning:

	PATH=$PATH:/home/user/programs/:/home/user/who-needs-organization/bin/
	export PATH

#####Case study: msstats:
Msstats is a program that uses libsequence. It's useful to see how this is installed because it requires libraries to be loaded during installation. 

1. Download msstats source code:

		$ wget http://molpopgen.org/software/msstats/msstats-0.3.1.tar.gz

2. Untar:

		$ tar -xvzf msstats-0.3.1.tar.gz

3. cd to new directory:

		$ cd msstats-0.3.1

4. Read the README:

		$ less README

5. Load the required modules:

		$ module load gcc
		$ module load libsequence

6. Provide configure with information about libsequence and run configure (notice the prefix option; that is all one line):

		$ CPPFLAGS=-I$LIBSEQUENCE/include LDFLAGS=-L$LIBSEQUENCE/lib ./configure --prefix=/home/[user]/programs/

7. Make:

		$ make

8. Make install:
		
		$ make install 

#####Java programs:
Using Java programs is easy because Java is inherently cross-platform. Java works by creating a layer between the computer and the program called the Java Virtual Machine (JVM). The JVM is platform specific, but any java executable file (.jar) can be run. This way, software people only have to send out 1 file which can be used on (almost) any platform. This also means that you don't have to compile the Java program (the software provider already did it for you), you just need to run it. A short example involving `BEAGLE` follows:

a. cd to the parent directory:

		$ cd programs

b. Download `BEAGLE` with `wget`:

		$ wget http://faculty.washington.edu/browning/beagle/beagle.jar

c. Run `BEAGLE` according to the instructions provided here: <http://faculty.washington.edu/browning/beagle/beagle_3.3.2_31Oct11.pdf> For example:

		$ java -Xmx800m -jar beagle.jar data=data.bgl trait=T2D out=example

As you can see, Java programs don't really need to be "installed", they just need to exist. If you do need to compile the program, you will need to follow the instructions provided. If the source code contains a file called `build.xml` it probably uses ant (which is similar to make). If this is the case, you can create a .jar file simply by typing:

	$ ant compile jar

Note: I haven't tested these myself. More information about ant is available here: <http://ant.apache.org/manual/tutorial-HelloWorldWithAnt.html>


#####Programs from Github:
Programs, pipelines, and scripts from Github work much the same way as software from other sources. One key difference is in acquiring it. There are two main ways to get software from Github: cloning and downloading a zip. The advantage of cloning is that if the software updates, you just need to pull the changes and reinstall and you are updated. With the zip file, you need to remove the folder and redownload it when there in an update. However, sometimes software isn't updated frequently so you don't need to worry about updates. 

#####Cloning:

a. Go to the parent directory of where you want the software installed

		$ cd programs

b. Visit the Github repository and on the very right there will be a text box that says `HTTPS clone URL` and it will contain a link that looks like this: `https://github.com/[user]/[repository].git`. Copy that link to the clipboard

c. Back in farm, `clone` the repository:

		$ git clone https://github.com/[user]/[repository].git

d. Change to the directory you just created (the name of the repository) and follow the installation instructions that are (hopefully) provided.

#####Downloading a zip:
a. Go to the parent directory of where you want the software installed

		$ cd programs

b. Visit the Github repository and on the very right there is a button that says `Download zip`. Right click this link and click `copy link`. 

c. Back in farm, download the zip with wget:

		$ wget https://github.com/[user]/[repository]/archive/[branch].zip

d. Unzip this folder with unzip:

		$ unzip [repository]

e. Change to the directory you just created (the name of the repository) and follow the installation instructions that are (hopefully) provided.


####Special Farm Things:
Farm has a number of things that make it unique, not dissimilar from a snowflake. These are things you won't see on many Linux distributions and if you try to do it on them, you might get an angry message telling you to do something you're good at (don't listen to it you're beautiful). 

#####Max number of command line arguments:

The maximum number of arguments in any command is 131072. If you are getting errors from bash saying you have too many arguments, try and specify the maximum number of arguments as 131072. For example, in removing a bunch of files:

	$ find -name "[name_of_file].*" | xargs -n131072 rm

This command will remove all files that match the pattern `[name_of_file].*`. `*` is a wildcard character. The `-name` is different from `[name_of_file]` and should not be substituted!
 
#####Modules:

This package allows system administrators to update the system libraries and other core programs without breaking your code or scripts. Modules contain the information that the shell needs to locate programs and libraries without getting confused about different versions and different files. For you, this means that before using something like gcc, you need to load it:

	$ module load gcc
	Module GCC 4.5.0 Loaded.

To see what modules are available, type:

	$ module avail

Any one of these modules can be loaded and should be. The special farm thing here is that if you run python, for example, without loading the module, you'll run version 2.4.3. However, if you load the python module, you will get 2.7.3. This becomes an issue when you run python from a script but forget to load the module because you'll be running an old version of python. So remember, if you're using a preinstalled program, make sure you load the module. 

#####Built-in variables:

When you submit a job on farm, the Sun Grid Engine gives you two variables that change according to some parameters. One is the variable `$SGE_TASK_ID`. When running an array of jobs, this variable corresponds to a particular task. Each job in the array has a different value for `$SGE_TASK_ID`. 

Next, there is `$JOB_ID`. This variable changes every time you submit a job. Therefore, when you submit an array of jobs, each job will have the same `$JOB_ID`, but each one will have a different `$SGE_TASK_ID`. These variables can be used in conjunction to name output files. For example, you can submit a job like this:

	sfs_code 1 1 > out.$JOB_ID.$SGE_TASK_ID

This will save the output in a file named something like this: `out.83917.1`

####Submitting Jobs to farm:
Speaking of submitting jobs, you are probably going to want to do that. The farm cluster uses the Sun Grid Engine (SGE) to distribute jobs across its 39(?) nodes. When you run simulations, you want them to run on these nodes rather than the one you're logged in to (which is called the head). To submit a job simply type:

	$ qsub myjob.sh

The qsub command has a number of options, many of which are necessary: [expand on this from the current wiki] 

`-S`:
This option tells qsub what shell you're using. You will probably want to use bash:
`-S /bin/bash`

`-cwd`:
This tells qsub to run the command from your current working directory. 

`-e`:
This tells qsub where to put the error log files. If you don't want to save them, put them in `/dev/null`:
`-e /dev/null`

`-o`:
This tells qsub where to put the output log files. If you don't want to save them, or if you are creating your own output files, put them in `/dev/null`:
`-o /dev/null`

`-t`:
This tells qsub that you are submitting an array of jobs. It will run the script you send it multiple times. To specify how many times, use a range. The max number of jobs in an array is 50,000:
`-t 1-10`

`-help`:

Prints a listing of all options.

`-j y|n`:

Specifies whether or not the standard error stream of the job is merged into the standard output stream. If both the `-j y` and the `-e` options are present, Grid Engine sets, but ignores the `error-pat`h attribute.

`-m b|e|a|s|n,…`:

Defines or redefines under which circumstances mail is to be sent to the job owner or to the users defined with the `-M` option described below. The option arguments have the following meaning: 'b' Mail is sent at the beginning of the job. 'e' Mail is sent at the end of the job. 'a' Mail is sent when the job is aborted or rescheduled. 's' Mail is sent when the job is suspended. 'n' No mail is sent. Currently no mail is sent when a job is suspended.

`-M user[@host],…`

Defines or redefines the list of users to which the server that executes the job has to send mail, if the server sends mail about the job. Default is the job owner at the originating host. You can use this to add your email address for updates or information about your jobs. For example, `qsub -M johndoe@example.com stupid.sh` will send email about the job status of the stupid.sh script to johndoe@example.com.

`-N name`

The name of the job. The name can be any printable set of characters, starting with an alphabetic character. If the `-N` option is not present Grid Engine assigns the name of the job script to the job after any directory pathname has been removed from the script-name.

 `-tc max_running_tasks`
 
allow users to limit concurrent array job task  execution.  Parameter `max_running_tasks` specifies  maximum number of simultaneously running tasks.  For example we have  running SGE  with 10 free slots. We call `qsub -t 1-100 -tc 2  jobscript`. Then  only  2  tasks  will  be scheduled to run even when 8 slots are free.

#####Output to many files vs one:
When using qsub, you are going to want to save the output of each simulation in a different file. This is because when multiple processors write to the same file, bad and funky things happen. James Brown may be pleased, but you will not be. To achieve this, use the built-in variables (`$SGE_TASK_ID`, `$JOB_ID`) to number your output files.

#####Checking up on jobs:
SGE comes with a command called `qstat` which will tell you the progress of your job:

	$ qstat

You can use the following to check on status of everyone's jobs
$ qstat -u \*

You can also check the status of a particular job:

	$ qstat -j [job-id]

The maxvmem information gives you an idea of the maximum amount of RAM your job has used. This is important for memory considerations (see below).

You can delete a job:

	$ qdel [job-id]

Log into a compute node:

	$ qrsh

#####Ganglia

One of the best ways to check on jobs is to use the ganglia website. It can be opened on a browser at

	$ http://localhost:50070/ganglia/

But only works if you've logged into farm with a special ssh command. It is suggested to write an alias to log into farm always using:

	$ ssh -Y -L 50070:farm:80 user@farm.caes.ucdavis.edu

Where "user" is replaced with your username.

Ganglia lets you check down nodes (email help to get them restarted), network I/O, memory, etc. for individual nodes as well as the head node.

#####Things to watch for:

######NFS Usage

The network filesystem (NFS) is what the system uses to communicate between the head node and compute nodes. Overloading the NFS can cause work to slow down dramatically or even cause nodes to crash. Remember that all your files are stored on the head node, so any jobs that do heavy reading/writing require that the CPU on the compute node is writing over the network (using NFS) to the headnode. Avoid heavy read/write as much as possible, for example, by writing to files on the compute node and moving them to the head node when you're done. NFS usage may spike to >150Mb when transferring big files or starting a run, but if it stays constant at >10Mb your job probably needs to be re-thought, as this will likely cause problems for other users.

######Memory

Each compute node has 24G RAM. If you are using more than 1.5G RAM per job you need to use something like `-pe threaded` to request more CPU so you do not end up using other people's resources. Check the overall memory usage of your runs; using too much memory can cause individual nodes to go down.

######Space on nodes

Each compute node has a 200G hard drive. This is available in /scratch . 
It is advisable to create your own directory there, e.g. /scratch/jri . 
You can use tentakel to do this on all nodes at once. 
It is a good idea for jobs writing to the compute node that you create a new directory for each job, using the `$JOB_ID` variable. 
This prevents overwriting files. 
Make sure to delete all these files when the job is done, so the hard disk on the compute node does not fill up.

To avoid causing problems with the NFS, in general:

* Make bigger tasks (so submit 500-1000 instead of 25-75000 * Throttle your qsubs, if in a loop add a sleep so submitting 25-75k takes say 10 minutes instead of 10 seconds.

To check on a big job:

* Run top. Watch for any of the following:

	- Does the load average spike?

	- Does the %wa (3rd line) spike?

	- Does any process balloon in memory usage? (sort by memory use with M)

	- Does any process saturate at 100% cpu usage? Which?

	- Does swap usage increase?



======

###Unix computer tips:
This section contains tips and information applicable to any Unix-based computer you use. (Hereafter referred to as Unix).

####Bash:
In order to effectively use the farm computer, you need to know how to move around in bash. Use this course to learn how: <http://cli.learncodethehardway.org/book/>

[add specific things that people in the lab need/use]

####R:

####Python: 
Learn from here: <http://learnpythonthehardway.org/book/>

[add specific things that people in the lab need/use]

####Git:
Git is useful because it allows you to share your programs/scripts with minimal effort and it backs up your programs in case you do something dumb and `rm -rf ./` your source code (please don't run that code because it deletes things you don't want deleted). 

Git is already installed on farm. To create a new repository, make a new folder:

	$ mkdir i-am-awesome

cd to that directory and type in:

	$ git init

Now you have a new git repository. Now you need to connect it to your Github account:

	$ git remote add origin https://github.com/[username]/[repository-name].git

Now you can add files to the repository with the git add command:

	$ git add [file1] [file2]

You can add multiple files at once separated with a space or if you're lazy you can wildcard:

	$ git add *

Now to send your files to Github, you need to commit them:

	$ git commit -m "[info]"

And finally, push them to the server:

	$ git push

If a repository exists, you can add it to your computer by cloning it (this is explained in depth in the software installation section):

	$ git clone [url]

If you are working on multiple computers or with other people make sure you pull the changes before you start working or else you risk losing your changes or needing to merge, which can get ugly:

	$ git pull

For a more well written guide, go here: <http://rogerdudler.github.io/git-guide/>


####Special Unix things:

#####What language should I use?
The world of computer languages is confusing because of the seemingly arbitrary distinctions and made up words that make every language seem like the new hot stuff. I'm going to try and clear up some of the confusion.

Two of the major camps of languages are scripting languages and compiled languages (scripting languages are also called interpreted languages). Scripting languages have something called an interpreter which runs while your script is running and translates your instructions on the fly to the computer. Examples of scripting languages include perl, Python, R, Ruby, and Javascript. Compiled languages on the other hand, have something called a compiler which translates your program into machine code (or binary: 00101010010). The compiler is no longer needed at this point and your computer can just run your compiled code (the binary file). Examples of compiled languages are: C, C++, Java, Fortran, COBOL.

There are a few notable results of the difference between compiled and scripting languages:

1. Scripts are usually slower than their compiled counterparts: this is because the interpreter must run while the script is running so it takes up more memory (and probably other reasons).

2. Scripts are easier to write than compiled programs: when you compile a program, you need to tell the computer __everything__, including where to start. This is why C, C++, and Java programs have a "main" method or class. Generally speaking, you need to be more careful and know a lot about the language you're programming in when writing in a compiled language. Scripts on the other hand can figure most things out on their own and run top to bottom (unless told otherwise). 

3. Scripts are cross platform: or more accurately, they are more cross platform than compiled programs. This is because when programs are compiled, they are compiled according to the processor instructions, which change from computer to computer. This is why you can't install iOS on your desktop computer (also because Apple would sue you so hard). 

#####So what do I pick?
Scripts for short things like manipulating files and summarizing data, programs for larger projects that do complex transformations or simulations (if you're really in doubt, pick scripts.)

#####Object orientation?
I refer you to this: <http://learnpythonthehardway.org/book/ex42.html>
Any other questions can be answered with Google and mediation.



#####Screen:
Screen is especially useful for farm because it lets you run things that take a while without qsubbing (Be careful not to run things that take hours and lots of memory though). Screen lets you open another session on the farm which you can detach from. This lets whatever program or script you have running keep running even after you log out. To open a new screen, type:

	$ screen

Now you'll have a new bash session. To detach from that screen, press they keys Control-a, d. To see what screens you have detached, use the -ls option:

	$ screen -ls

This will output a list of existing screens along with a number corresponding to each screen. In order to switch to a particular screen, use the -r option along with that screen's number:

	$ screen -r [number]

To find out more, use the manual page:

	$ man screen

#####/dev/null:
This is a directory in Linux that is akin to a wasteland. If you have output files you don't need you can send them here and it's like deleting them. This is especially useful with the SGE because it has default output and error files. If you don't need to look at these, you can add this to the top of your script and it won't write them to disk:
<<<<<<< HEAD

=======
>>>>>>> rilab/master

	#$ -e /dev/null
	#$ -o /dev/null

<<<<<<< HEAD
####Contributing to the wiki

To contribute to this wiki you will need a Github account. First you will want to fork the repository, which you can do by going to the top of the page and clicking "fork". Then, go to the folder you want  to store the repository in on your computer and clone it:

	git clone https://github.com/RILAB/farm-guide.git

#####Updating to the current version of the farm guide

Before you start adding things to the wiki, to avoid merging madness you want to update your fork to the current version of the RILAB fork.

First you want to add the repository for tracking:

	git remote add --track master rilab https://github.com/RILAB/farm-guide.git
	
Then fetch the current version

	git fetch rilab
	
Then merge it with yours:

	git merge rilab/master
	
Now you should have the most recent version of the RILAB wiki.

#####Submitting changes to the RILAB wiki

Pull requests.
=======
>>>>>>> rilab/master
