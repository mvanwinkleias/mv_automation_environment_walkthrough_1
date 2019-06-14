# mv_automation_environment_walkthrough_1

We will be cloning these repos:

* https://github.com/theias/ias_package_shell
* https://github.com/theias/ias_bash_script_infra

## System Requrements

### Debian Requirements

This can probably be pared down lots:

```
sudo apt-get install fakeroot gdebi build-essential
```

### RPM Requirements

```
sudo apt-get install fakeroot make rpm-build
```

## Doing Some Work

Let's make a place to do some work:
```
mkdir -p ~/src/git/github
mkdir -p ~/src/git/home_repos
```

### Installing ias_package_shell
Clone and install ias_package_shell (project template generator):

```
cd ~/src/git/github
git clone https://github.com/theias/ias_package_shell.git
cd ias_package_shell
fakeroot make package-deb
sudo gdebi build/ias-package-shell/ias-package-shell*.deb
```

### Making a Simple Project

Let's do a simple project example:

```
cd ~/src/git/home_repos
ias_package_shell.pl
```
Answers:

```
ias_simple_example_one
A simple example
None
None
```

Let's cd into it:

```
cd ias_simple_example_one
ls
```
Sure is a lot of stuff in here.  But, we're interested in src/bin:

```
ls src/bin
```

Let's make that an executable, and run it:

```
chmod +x src/bin/*
src/bin/ias-simple-example-one_hello.sh
```

Let's install it!
```
fakeroot make package-deb
sudo gdebi build/ias-simple-example-one/ias-simple-example-one*.deb
```

Run the installed version:
```
/opt/IAS/bin/ias-simple-example-one/ias-simple-example-one_hello.sh
```

Let's remove it!

```
sudo dpkg -r ias-simple-example-one
```
... And it's gone!

### Changing the Base Directory

You might be thinking, "I don't work at IAS.  I work at AwesomeSchool."

Well, we're going to keep the project name the same (we can just regenerate
it if we like).  BUT, we can change where it gets installed.

```
echo "BASE_DIR=/opt/AwesomeSchool" >> base.gmk
```

Let's install it to its new location.
```
fakeroot make package-deb
sudo gdebi build/ias-simple-example-one/ias-simple-example-one*.deb
```
And now, run it:

```
/opt/AwesomeSchool/bin/ias-simple-example-one/ias-simple-example-one_hello.sh
```

## Introduction to Automation

Let's say you have something you want to cron up, which you'd like to log.
You'd also like to generate an output file.  We'll work through a trivial example.

### Install ias_bash_script_infra

```
cd ~/src/git/github
git clone https://github.com/theias/ias_bash_script_infra.git
cd ias_bash_script_infra
fakeroot make package-deb
sudo gdebi build/ias-bash-script-infra*.deb
```

### Adding an Automation Script

```
cd ~/src/git/home_repos/ias_simple_example_one
cp /opt/IAS/bin/ias-bash-script-infra/simple_example_script.sh src/bin/
```

Edit src/bin/simple_example_script.sh .

* Comment out the lines beginning with:
	* echo "Here is the configuration ..."
	* cat \`get_conf_dir\` ...
* Comment out the line beginning with: write_log_error

Those are left in as sanity checks and examples.

Run it.

You shouldn't see anything to stdout!

Check syslog.

### Create an Output File

Below the line beginning with "output_file_name=" , put:
```
date >> "${output_file_name}"
```

Run it.

There should now be something in the output file.
