A collection of Bash scripts built to practice real scripting fundamentals — user input, file handling, permissions, and automation.

Bash scripting is important in Dev Ops as it is used to automate repetitive tasks like deployments, back ups and server setup so you're not doing the same thing manually every time.

1) Basic Arithmetic Calculator
A script that takes two numbers as input and performs basic arithmetic operations (addition, subtraction, multiplication, division).

#!/bin/bash
# Challenge 1: Basic Arithmetic Calculator

echo "================================"
echo "    Basic Arithmetic Calculator"
echo "================================"

read -p "Enter first number: " num1
read -p "Enter second number: " num2

echo ""
echo "Results:"
echo "$num1 + $num2 = $((num1 + num2))"
echo "$num1 - $num2 = $((num1 - num2))"
echo "$num1 × $num2 = $((num1 * num2))"

if [ "$num2" -eq 0 ]; then
  echo "$num1 ÷ $num2 = Error: Cannot divide by zero"
else
  echo "$num1 ÷ $num2 = $((num1 / num2))"
fi

3 Key Learnings for Script 1

- How to capture user input with read and store it in a variable.
- Using $(( )) for arithmetic operations in Bash.
- Using an if statement to catch division by zero before it breaks the script.



Script 2) File Operations Script
A script that automates directory and file creation.

!/bin/bash
# Challenge 2: File Operations Script

DIR="bash_demo"
FILE="demo.txt"

mkdir -p "$DIR"
echo "Directory '$DIR' created."

cd "$DIR"
touch "$FILE"
echo "File '$FILE' created."

echo "This file was created by a Bash script on $(date '+%Y-%m-%d')" > "$FILE"

echo ""
echo "File contents:"
cat "$FILE"

3 Key Learnings for Script 2

- How to create directories and files using mkdir and touch.
- Using $(date) to grab the current date and write it into a file.
- How to write text to a file using echo with > and read it back with cat.


Script 3) File Checker with Permissions
A script that checks if a file exists and displays its permissions.

#!/bin/bash
# Challenge 3: File Checker with Permissions

read -p "Enter filename to check: " filename

echo ""

if [ ! -e "$filename" ]; then
  echo "✗ File '$filename' does not exist."
  exit 1
fi

echo "File '$filename' exists."

[ -r "$filename" ] && echo "✓ File is readable"   || echo "✗ File is not readable"
[ -w "$filename" ] && echo "✓ File is writable"   || echo "✗ File is not writable"
[ -x "$filename" ] && echo "✓ File is executable" || echo "✗ File is not executable"

3 Key Learnings for Script 3

- How to check if a file exists using the -e flag.
- Using -r, -w, and -x flags to test file permissions.
- How to exit a script early with exit 1 when a condition fails.


4) Backup Script for Text Files
Create a script that backs up all .txt files from one directory to another.

#!/bin/bash
# Challenge 4: Backup Script for Text Files

read -p "Enter source directory: " src

if [ ! -d "$src" ]; then
  echo "Error: '$src' is not a valid directory."
  exit 1
fi

timestamp=$(date '+%Y-%m-%d_%H-%M')
backup_dir="backup_$timestamp"

mkdir -p "$backup_dir"
echo "Backup directory created: $backup_dir"

echo "Copying .txt files..."
count=0
for file in "$src"/*.txt; do
  [ -f "$file" ] || continue
  cp "$file" "$backup_dir/"
  ((count++))
done

echo ""
echo "Backup complete! Files backed up: $count"

3 Key Learnings for Script 4

- How to use a for loop to iterate over files in a directory.
- Using $(date) to create a unique timestamped folder name each time the script runs.
- How to use a counter variable to track and display how many files were copied.



