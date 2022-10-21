## Client Installation

The git-keeper client is a command line application that runs in a Unix-like
environment. It works natively in macOS and Linux, and in Windows requires the
Windows Subsystem for Linux (WSL). The client also requires Git and Python 3.8
or greater.

If you are using Ubuntu within WSL, you will have Python 3, but it may not come
with `pip3`. If you cannot run `pip3` in WSL, install it like so:

```
$ sudo apt-get update
$ sudo apt install python3-pip
```

Regardless of which of the three environments you are using, you should now be
able to install the git-keeper client. You can install it system-wide with the
command below, or if you are familiar with Python virtual environments you can
install it in a virtual environment.

```
$ sudo python3 -m pip install git-keeper-client
```

## Client Configuration

To configure the client, run `gkeep config` and follow the prompts. See the
example output below for the hostname, and use your username on the git-keeper
server for the username:

```
$ gkeep config
Configuring gkeep
Server hostname: ec2-18-205-161-251.compute-1.amazonaws.com
Username: <your username>
Server SSH port (press enter for port 22): 
Submissions fetch path (optional, press enter to skip): 
Assignment templates path (optional, press enter to skip): 
The following will be written to /home/username/.config/git-keeper/client.cfg: 
[server]
host = ec2-18-205-161-251.compute-1.amazonaws.com
username = <your username>

# optional section
[local]

Would you like to proceed? (y/n) y
```

## SSH Keys

If you have never generated SSH keys in your environment, you will need to do
so now. Run the following:

```
$ ssh-keygen
```

You can use the default filename, and you do **NOT** want to set a password.

Now you can copy your key to the git-keeper server with `ssh-copy-id`:

```
$ ssh-copy-id <your username>@ec2-18-205-161-251.compute-1.amazonaws.com
```

If everything is set up correctly you should be able to communication with the
server. Use `gkeep check` to check this:

```
$ gkeep check
/home/username/.config/git-keeper/client.cfg parsed without errors

Successfully communicated with ec2-18-205-161-251.compute-1.amazonaws.com

Server information:
  Version: 1.0.2
  gkeepd uptime: 20h44m33s
  Firejail installed: True
  Docker installed: True

Server default assignment settings that can be overridden:
  env: firejail
  use_html: True
  timeout: 300
  memory_limit 1024
```

## Creating a Class

To create a class that contains students you will need to create a CSV file
that contains the class roster. The CSV file needs 3 columns: last name, first
name, and email address.

Using a text editor, create a file named `ccsc.csv` and add one or more of your
neighbors as students in your course. Your CSV file should look something like
this:

```
Hamilton,Margaret,mhamilton@example.edu
Hopper,Grace,ghopper@example.edu
Lovelace,Ada,alovelace@example.edu
```

In your terminal, navigate to the location of your CSV file and create a class
named `ccsc` like so:

```
gkeep add ccsc ccsc.csv
```

## Creating an Assignment

Try creating a simple hello world assignment. To create a new assignment
directory structure, use `gkeep new`:

```
gkeep new hello_world
```

This creates the following structure:

```
hello_world/
├── assignment.cfg
├── base_code
├── email.txt
└── tests
    └── action.sh
```

The files `assignment.cfg`, `email.txt`, and `action.sh` are created as empty
files. The `assignment.cfg` file would let you set various configuration
options for the assignment, but you can leave it empty for the default
options. The `email.txt` file is text that will be added to the email that
students receive for the assignment. You may leave that empty as well, or try
adding some text.

The `base_code` directory may not be empty, since the contents of this
directory will be in the initial git repositories that the students
clone. Create the empty file `hello_world.py` in `base_code`.

The `action.sh` is a bash script that is run by git-keeper when a student
pushes a submission. Below is an `action.sh` that runs the student's
`hello_world.py` and checks the output. You can copy this into your
`action.sh`:

```bash
# The path to the student's submission directory is in $1
SUBMISSION_DIR=$1

# Run the student's code and put the output (stdout and stderr) in output.txt
python $SUBMISSION_DIR/hello_world.py &> output.txt

# Compare the output with the expected output. Throw away the diff output
# because we only care about diff's exit code
diff output.txt expected_output.txt &> /dev/null

# diff returns a non-zero exit code if the files were different
if [ $? -ne 0 ]
then
    echo "Your program did not produce the expected output. Your output:"
    echo
    cat output.txt
    echo
    echo "Expected output:"
    echo
    cat expected_output.txt
else
    echo "Tests passed, good job!"
fi

# Always exit 0. If action.sh exits with a non-zero exit code the server sees
# this as an error in your tests.
exit 0
```

Your assignment is now ready to be uploaded!

## Uploading an Assignment

To upload an assignment you use `gkeep upload`, giving it the name of the your
class and the path to the assignment directory:

```
gkeep upload ccsc hello_world
```

Once the assignment is uploaded you will get an email similar to the email that
the students will receive, but the students will not receive any emails until
the assignment is published. This gives you a chance to ensure that the email
looks okay, and an opportunity to try pushing a solution yourself.

## Testing an Assignment

To make sure that the test work okay on the server, create a solution for the
assignment by creating `hello_world/solution` and in that directory create a
`hello_world.py` solution file containing a correct solution:

```python
print('Hello, world!')
```

Now you can use `gkeep test` to automatically push your solution:

```
gkeep test ccsc hello_world hello_world/solution
```

You should receive an email with your test results.

## Publishing an Assignment

If everything looks good, you can now publish your assignment with `gkeep
publish`, which will send emails out to your students:

```
gkeep publish ccsc hello_world
```

This should send emails to the neighbors that you added to your class.
