## Faculty Client Installation

The git-keeper client is a command line application that runs in a Unix-like
environment. It works natively in macOS and Linux, and in Windows requires the
Windows Subsystem for Linux (WSL). The client also requires Git and Python 3.8
or greater.

You can install the client system-wide using `sudo` using the command below, or
you can make a virtual environment and install the client there.

```
sudo python3 -m pip install git-keeper-client
```

If you are using Ubuntu within WSL, you may not have pip for Python 3. You can
install it like this:

```
sudo apt install python3-pip
```

## Client Configuration

All client commands are run using `gkeep` along with a subcommand. To
configure the client, run `gkeep config` and follow the prompts. See the
example output below for the hostname, and use your username on the git-keeper
server for the username:

```
gkeep config
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

All communication with the server is done over SSH. If you have never generated
SSH keys in your environment, you will need to do so now. Run the following:

```
ssh-keygen
```

You can use the default filename, and you do **NOT** want to set a password.

Now you can copy your key to the git-keeper server with `ssh-copy-id`. Use the
password that you received in an email from git-keeper:

```
ssh-copy-id <username>@ec2-18-205-161-251.compute-1.amazonaws.com
```

If everything is set up correctly, `gkeep` should now be able to communication
with the server. Use `gkeep check` to check this:

```
gkeep check
/home/username/.config/git-keeper/client.cfg parsed without errors

Successfully communicated with ec2-18-205-161-251.compute-1.amazonaws.com

Server information:
  Version: 1.0.1
  gkeepd uptime: 20h44m33s
  Firejail installed: True
  Docker installed: True

Server default assignment settings that can be overridden:
  env: firejail
  use_html: True
  timeout: 10
  memory_limit 1024
```

## Creating a Class

To create a class that contains students you will need to create a CSV file
that contains the class roster. The CSV file needs 3 columns: last name, first
name, and email address.

Using a text editor, create a file named `ccsc.csv` and add one or more of your
workshop neighbors as students in your course. Your CSV file should look
something like this:

```
Hamilton,Margaret,mhamilton@example.edu
Hopper,Grace,ghopper@example.edu
Lovelace,Ada,alovelace@example.edu
```

In your terminal, navigate to the location of your CSV file and create a class
named `ccsc` like this:

```
gkeep add ccsc ccsc.csv
```

## Creating an Assignment

Try creating a simple hello world Python assignment. To create a new assignment
directory structure, use `gkeep new`:

```
gkeep new hello_world
```

This creates the following structure:

```
hello_world
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

The `base_code` directory must not be empty, since the contents of this
directory will be in the initial git repositories that the students
clone. Create the empty file `hello_world.py` in `base_code`.

The `action.sh` file in the `tests` directory is a bash script that is run by
git-keeper when a student pushes a submission. The tests directory should also
contain any other files needed for testing. Create a file in `tests` named
`expected_output.txt` so that the tests can compare the output of the
submission with the expected output, and enter the following:

```
Hello, world!
```

 Below is an `action.sh` that runs the student's `hello_world.py` and checks
the output against `expected_output.txt`. You can copy this into your
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

To upload an assignment you use `gkeep upload`, giving it the name of your
class and the path to the assignment directory:

```
gkeep upload ccsc hello_world
```

Once the assignment is uploaded you will get an email similar to the email that
the students will receive, but the students will not receive any emails until
the assignment is published. This gives you a chance to ensure that the email
looks okay, and an opportunity to try pushing a solution yourself.

## Testing an Assignment

To make sure that the tests work okay on the server, create a solution for the
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

This should send emails to the neighbors that you added to your class. You can
try cloning and pushing to each other's assignments.
