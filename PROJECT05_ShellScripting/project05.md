# Dev Ops Training Projects - Darey.io 

# SHELL SCRIPTING
![](Images/shell_script.png)

# Introduction to Shell Scripting
Consider you're a freelance web developer managing multiple client projects simultaneously. Each day, you're tasked with routine activities like updating website content, backing up client data, and deploying website changes. These tasks, while essential, can be time-consuming and prone to oversight if handled manually.

However, with the power of scripting, you can transform your workflow and maximize productivity. By creating customized scripts tailored to your specific needs, you can automate repetitive tasks and streamline your daily operations. For instance, you can write scripts to automatically update website content based on predefined schedules, perform regular backups of client data to ensure data integrity, and deploy changes to multiple websites simultaneously with just a few commands.

In this scenario, scripting serves as your digital assistant, allowing you to focus more on your core tasks of coding and client communication while minimizing the risk of errors and improving overall efficiency. By harnessing the capabilities of scripting, you can elevate your freelance web development business to new heights and deliver exceptional results to your clients consistently.


## What is Shell Scripting?

Shell scripting is like having a magic spell that makes your computer do things automatically. You write down a series of instructions in a special language, and then your computer follows those instructions to do whatever you want it to do. It's like having a little helper that does exactly what you tell it to do.

**What Is a Shebang or Hashbang (#!/bin/bash)** 
At the beginning of a script, is a shebang or hashbang. It is a special notation used in Unix-like operating systems, to specify the interpreter that should be used to execute the script. In this case, #!/bin/bash specifically indicates that the Bash shell should be used to interpret and execute the script.

The #! (pronounced as "shebang" or "hashbang"): This character sequence tells the operating system that what follows is the path to the interpreter that should be used to execute the script.

/bin/bash: This is the absolute path to the Bash shell executable. It tells the system to use the Bash interpreter located at /bin/bash to run the script.

When a script with a shebang line like #!/bin/bash, the operating system looks for the specified interpreter (in this case, Bash) and uses it to execute the script.

Without a shebang line, the system **may not know how** to interpret and execute the script, and you may need to explicitly specify the interpreter when running the script.

## Why is Shell Scripting Important?

Shell scripting is super important because it helps you save time and avoid mistakes. Imagine if you had to copy all your important files by hand every day – it would take forever and you'd probably forget something! But with a shell script, you can tell your computer to do it for you, and it will make sure everything gets copied correctly every time.


## Prerequisites

Before you start learning shell scripting, there are a few things you should know:

1. You should be familiar with using the command line interface – that's the thing where you type commands into a black window on your computer.
  
2. You should know some basic Linux commands, especially how to change file permissions. This helps you control who can access your files.
  
3. You should be able to use text editors like Nano, Vim, or Emacs. These are tools that let you write and edit your shell scripts.

# Project Highlights

- Shell Scripting
- Introduction to Shell Scripting
- Importance of Shell Scripting
- Getting Started with Shell Scripting
- Conclusion

## Getting Started with Shell Scripting

### Syntax for a Shell Script

Bash Scripts are essentially a series of commands and instructions that are executed sequentially in a shell. 
1. Every line of a bash file starts with a shebang - `#!/bin/bash`

### 1. Variables
Variables: Bash allows you to define and work with variables. In shell scripting, you can use variables to store data. For example, you can create a variable called `Name` and give it the value "John" like this:

**Example: Assigning value to a variable:** - `Name="John"`
From the example above, "John" was asigned to the variable "Name". 

**Example: Retrieving value from a variable:** - `echo $Name`
`echo` is a command used to print a text, variables or values. In this example, echo is used to print a variable which stores a value.
![](<Images/01. Variables.png>)

### 2. Control Flow

Shell scripting lets you control the flow of your script using things like if-else statements and loops. 

**Example 1 - For example, you can use an if-else statement to check if a number is positive, negative, or zero:**

#!/bin/bash
#Example script to check if a number is positive, negative, or zero
read -p "Enter a number: " num
if [ $num -gt 0 ]; then
    echo "The number is positive."
elif [ $num -lt 0 ]; then
    echo "The number is negative."
else
    echo "The number is zero."
fi

This script asks the user to enter a number, then checks if it's positive, negative, or zero, and prints out a message accordingly.

![](<Images/02. control_flow.png>)

**Example 2: The piece of code iterates through a list using a for loop:**

#!/bin/bash
# Example script to print numbers from 1 to 5 using a for loop
for (( i=1; i<=5; i++ ))
do
    echo $i
done
![](<Images/03. ex_2.png>)

### 3. Command Substitution

Command substitution allows us to capture the output of a command and use it as a value within the script. The backtick or $() syntax can be used.

**Example 1: Using Backtick**
current_date=`date +%Y-%m-%d`

**Example 2: Using $() syntax**
current_date=$(date +%Y-%m-%d)

### 4. Input and Output

In Bash scripting, you've got handy tools to handle input and output. The "read" command lets you gather user input smoothly, while the "echo" command effortlessly displays messages on the screen.

**Example 1: Accept User Input** -  echo "Enter your name:
                                    read name
                                    echo "Hi $name, nice to meet you"

**Steps:**
1. Create the script file
2. Write the code into the script file - ![](<Images/06. script_01.png>)
3. Make it executable - ![](<Images/05. script_steps.png>)
4. Run the script and output text to terminal - ![](<Images/07. run_scripts.png>)

**Example 2: Output results into a file:**

**Steps:**
1. Modify the code in the script file to add -  `echo "Hi $name, nice to meet you" > index.txt`
![](<Images/08. output_file.png>)
2. Run the script - ![](<Images/09. newrun_02.png>)
3. Check Result in file - ![](<Images/10. output_exp_file.png>)


### 5. Functions

In Bash scripting, we can define and use functions to group related commands together. Functions help organize our code, making it easier to understand and reuse.

**Steps**
1. Create a folder named shell-scripts using the command - `mkdir shell-scripts`
2. Create the script file - `touch user-input.sh`
![](<Images/11. shell_scrr.png>)
3. Write code into file - ![](<Images/12. code_script.png>)
4. Make the file executable - `chmod u+x user-input.sh` 
5. Run the script - `./user-input.sh` ![](<Images/13. new_scrippts.png>)


## Directory Manipulation and Navigation

**Steps**
1. Create a file named linux_filesystem.sh using the command - `touch linux_filesystem.sh` and add the code
![](<Images/14. code_in_file.png>)
2. Make the file executable - `chmod u+x linux_filesystem.sh`
3. Run the script. - `./linux_filesystem.sh` ![](<Images/15. new_commd.png>)

## File Operations and Sorting

**Steps**
1. Create a file named sorting.sh using the command - `touch sorting.sh` and add the code
![](<Images/16. sorting_sh.png>)
2. Make the file executable - `chmod u+x sorting.sh` 
3. Run the script. - `./sorting.sh` ![](<Images/17. sorting_ngh.png>)


## Working with Numbers and Calculations

**Steps**
1. Create a file named calculations.sh using the command - `touch calculations.sh` and add the code
![](<Images/18. calcu_sh.png>)
2. Make the file executable - `chmod u+x calculations.sh` 
3. Run the script. - `./calculations.sh` - ![](<Images/19. calc_do.png>)


## File Backup and Timestamping

**Steps**
1. Create a file named backup.sh using the command - `touch backup.sh` and add the code
![](<Images/20. touch_bk.png>) ![](<Images/21. bk_sh.png>)
2. Make the file executable - `chmod u+x backup.sh` 
3. Run the script. - `./backup.sh` - ![](<Images/22. backup.png>)


## Conclusion

Shell scripting is like having a magic wand that lets you control your computer and make it do whatever you want. By learning shell scripting, you can automate tasks, save time, and avoid mistakes, making your life a whole lot easier! So why wait? Start learning shell scripting today and become a wizard of the command line! 🚀