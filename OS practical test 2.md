## Jobs & Process

- `<command> &` : Run program in background
- `Ctrl + Z` : Suspend program
- `bg` : Allow background program to run
- `fg <job_id>` : switch to foreground
- `ps` : list all running processes
  - `-e` : Display daemons too
  - `-f` : **Full list** of information
  - `-l` : **Long list** of information
  - `-Z` : Security information
  - `a` : display processes run on terminals
  - `x` : display processes **do not** run on terminals
- `pstree` : `ps` but in tree
- `nice -n <nice value> <program>` : Assign nice level to a program, range from -20 to 19
- `renice <nice value> <process id>` : Assign nice level to process
- `top` : Continuously updated list of process information
  - `-d <seconds>` : delay seconds, default 3
  - `-b` : show in batch, can use with `>` to write the output to a file
  - `-n <refresh count>` : quit when hit refresh count
- `killall` : kill all process of a **command name**
- `kill` : kill indicated **proccess**
- `sleep <number>[s|m|h|d]` : sleep
- `nohup <command> &` : Run command even when logout, best for afk
- `crontab` : cron table related
  - `-e` : edit crontab file, or create one if not exists
  - `-l` : display crontab file
  - `-r` : remove crontab file
  - `-v` : display recent edit timestamp
- cron command
  - `<0-59> <0-23> <1-31> <1-12> <0-6> <user> <command>`
  - min hour dom month dow(sun=0)
  - \* = every
  - , = list of val, eg. 1,2,3,4,5
  - \- = range of val, eg. 1-5 = 1,2,3,4,5
- `at <time>` : issue commands to run on specific time in future
  - xx:xx [am|pm]
  - now + x minutes/hours/days/weeks
  - mm/dd/yyyy
  - Month Day Year
  - `-l` : list all jobs of at
  - `-c <job id>` : show job content?
  - `-d <job id>` : remove job

## Shell Script

### Shebang and Basic Commands

- The shebang line `#!/bin/bash` at **first line of the file** specifies that the script should be run with the Bash interpreter.
- Make the script executable with `chmod +x script.sh`.
- Run the script with `./script.sh`.
- Basic commands:
  - `set`: Lists all variables.
  - `echo`: Prints text or $variables to the standard output.
  - `env`: Lists all **exported** variables.
  - `export <var>`: Exports a variable to subshells.

### Default Variables

Bash provides several default variables:

| Variable | Description                   |
| -------- | ----------------------------- |
| `$user`  | Current user's name           |
| `$home`  | User's home directory path    |
| `$path`  | Environment path for commands |
| `$pwd`   | Current working directory     |
| `$shell` | Current shell                 |

### Variable Assignment

- Assign variables using `variable=value` (no spaces around `=`).
- Variables store strings but can be interpreted differently based on usage.
- Example:
  ```shell
  name="John"
  echo $name  # Outputs: John
  ```
- `let`

  - A built-in command that evaluates arithmetic expressions.
  - Syntax: `let "variable=expression"`
  - Example:

    ```shell
    let "result=1+2"
    echo $result  # Outputs: 3

    a=5
    let "a+=1"  # Can use +=, -=, *=, /=, %=, ++, --
    echo $a  # Outputs: 6

    b=3
    let "c=a*b"  # Multiplies a and b, no need to escape `*`, variable used no need $
    echo $c  # Outputs: 18
    ```

- `$(( expression ))`

  - An arithmetic expansion that directly computes and returns the value of an expression.
  - Syntax: `result=$((expression))`
  - Example:

    ```shell
    result=$((1 + 2))
    echo $result  # Outputs: 3

    a=5
    ((a++))  # Increments a by 1, can use ++, --, +=, -=, *=, /=, %=, etc.
    echo $a  # Outputs: 6

    b=3
    ((c = a * b))  # Multiplies a and b and store in c (can assign inside brackets)
    echo $c  # Outputs: 18
    ```

- `expr`

  - An external command used for arithmetic evaluation. Backticks capture its output.
  - Syntax: `` result=`expr operand operator operand`  ``
  - Example:

  ```shell
  result=`expr 1 + 2`
  echo $result  # Outputs: 3

  a=5
  # Cannot use ++, --, +=, -=, etc.
  # Must add $ before variable name
  a=`expr $a + 1`  # Increments a by 1
  echo $a  # Outputs: 6

  b=3
  result=`expr $a \* $b`  # Multiplies a and b (must escape `*`)
  echo $result  # Outputs: 15
  ```

### Unique Symbols

- `''` (single quotes): Cannot recognize variables
- `""` (double quotes): Recognize and expand variables
- `\` (backslash): Escape special characters (e.g., `$`).
- `` `<command>` `` (backticks): Execute a command and capture its output.
- Examples:

  ```shell
  message='Good Morning $USER !'
  echo $message  # Outputs: Good Morning $USER

  message="Good Morning $USER !"
  echo $message  # Outputs: Good Morning <username>

  message="Good Morning \$USER !"
  echo $message  # Outputs: Good Morning $USER

  result=`expr 1 + 2`
  echo $result  # Outputs: 3
  ```

### Reading User Input

- Use `read` to get user input.
- Example:
  ```shell
  read -p "Enter your name: " name
  echo "Hello, $name!"
  ```

### Redirection and Piping

#### Redirection

- `[command] < [file]`: Redirect input.
- `[command] > [file]`: Redirect output (overwrites).
- `[command] >> [file]`: Redirect output (appends).
- `2>`: Redirect error output. (use `2>>` to append, and used with `>` or `>>`)
- `2>&1`: Redirect error to the same place as output.
- Example:

  ```shell
  cat < file.txt  # Reads from file.txt
  ls > output.txt  # Writes output to output.txt (overwrites)
  ls >> output.txt  # Appends output to output.txt
  ls /nonexistent > file.txt 2> error.txt # Redirects output and error to separate files
  ls /nonexistent > output.txt 2>&1 # Redirects both output and error to output.txt
  ```

#### Piping

- `|`: Pipe output to another command.
- `||`: Execute second command if first fails.
- `&&`: Execute second command if first succeeds.
- Examples:
  ```shell
  ls -l | grep ".log" #
  ls /nonexistent || echo "Directory not found."
  echo "Starting process..." && echo "Process initiated successfully."
  ```

### Positional Parameters

- `$0`: Script name.
- `$1`, `$2`, ...: Arguments passed to the script.
- `$*`: All arguments as a single string.
- `$@`: All arguments as separate strings.
- `$#`: Number of arguments.
- Example:

  ```shell
  # bar.sh
  echo "Arg 1: $1"
  echo "Arg 2: $2"
  echo "Arg 3: $3"

  # foo.sh
  echo '$* without quotes:'
  ./bar.sh $*

  echo '$@ without quotes:'
  ./bar.sh $@

  echo '$* with quotes:'
  ./bar.sh "$*"

  echo '$@ with quotes:'
  ./bar.sh "$@"

  # Running: ./foo.sh arg1 "arg21 arg22" arg3
  # Output:
  # $* without quotes:
  # Arg 1: arg1
  # Arg 2: arg21
  # Arg 3: arg22
  #
  # $@ without quotes:
  # Arg 1: arg1
  # Arg 2: arg21
  # Arg 3: arg22
  #
  # $* with quotes:
  # Arg 1: arg1 arg21 arg22 arg3
  # Arg 2:
  # Arg 3:
  #
  # $@ with quotes:
  # Arg 1: arg1
  # Arg 2: arg21 arg22
  # Arg 3: arg3
  ```

### Calculations

- Use `expr` for integer arithmetic.
- Operations: `+`, `-`, `*`, `/`, `%`.
- Note: `*` must be escaped as `\*`.
- Example:
  ```shell
  expr 1 + 2  # Outputs: 3
  expr 4 \* 2  # Outputs: 8
  num1=`expr 4 \* 2 + 1`
  echo $num1  # Outputs: 9
  ```

### Conditional Statements

#### If Statement

- Syntax:
  ```shell
  if [ condition ]; then
    command
  elif [ condition ]; then
    command
  else
    command
  fi
  ```
- Example:
  ```shell
  if [ -f /etc/hosts ]; then
    echo "File exists."
  elif [ -d /etc/hosts ]; then
    echo "Directory exists."
  else
    echo "Neither file nor directory exists."
  fi
  ```

#### Condition Checks

- **Number Comparison:**

  - `-eq`: **_EQ_**ual
  - `-ne`: **_N_**ot **_E_**qual
  - `-gt`: **_G_**reater **_T_**han
  - `-lt`: **_L_**ess **_T_**han
  - `-ge`: **_G_**reater than or **_E_**qual
  - `-le`: **_L_**ess than or **_E_**qual

- **String Comparison:**

  - `=`: Equal
  - `!=`: Not equal
  - `-z`: Empty string (**_z_**ero length)
  - `-n`: Non-empty string (**_n_**on-zero length)

- **File Checks:**

  - `-f`: **_F_**ile exists
  - `-d`: **_D_**irectory exists
  - `-e`: **_E_**xists (file or directory)
  - `-s`: File is not empty (**_s_**ize > 0)
  - `-r`: File is **_r_**eadable
  - `-w`: File is **_w_**ritable
  - `-x`: File is **_ex_**ecutable

- **Logical Operators:**
  - `-a`: **_A_**ND
  - `-o`: **_O_**R
  - `!`: NOT

### Case Statement

- Syntax:
  ```shell
  case variable in
    pattern1)
      command1
      ;;
    pattern2)
      command2
      ;;
    *)
      default command
      ;;
  esac
  ```
- Example:
  ```shell
  case $1 in
    start)
      echo "Starting..."
      ;;
    stop)
      echo "Stopping..."
      ;;
    restart)
      echo "Restarting..."
      ;;
    *)
      echo "Usage: $0 {start|stop|restart}"
      ;;
  esac
  ```

### Loops

#### For Loop

- Syntax:
  ```shell
  for variable in list; do
    command
  done
  ```
- list can be a range, a list of values, or the output of a command.
  - Range: `{1..5}`
  - List: `1 2 3 4 5` or `"one two three"`
  - Command output: `$(command)` or `` `command` ``
- Example:
  ```shell
  for i in 1 2 3 4 5
  do
    echo $i
  done
  ```

#### While Loop

- Syntax:
  ```shell
  while [ condition ]; do
    command
  done
  ```
- Example:
  ```shell
  i=1
  while [ $i -le 5 ]
  do
    echo $i
    i=`expr $i + 1`
  done
  ```

#### Until Loop

- Syntax:
  ```shell
  until [ condition ]; do
    command
  done
  ```
- Example:
  ```shell
  i=1
  until [ $i -gt 5 ]
  do
    echo $i
    i=`expr $i + 1`
  done
  ```

### To remember control flow syntax

- conditional statement like `if` and `case` must end with `fi` and `esac` respectively.
- loop statement like `for`, `while` and `until` must follow with `do` and end with `done`.
- must have a space between `[` and `]` in conditional statement.
