

# file: perlbrew.md

* [ metacpan install and run overview ]( https://metacpan.org/pod/App::perlbrew )
* [ using-perlbrew-on-ubuntu ]( http://smonff.github.io/blog/2014/03/23/using-perlbrew-on-ubuntu/ )
* [ perlbrew.pl ](https://perlbrew.pl/)
* [ good perlbrew overview ]( https://metacpan.org/pod/App::perlbrew )
* [ Comprehensive Perl Archive Network ]( http://www.cpan.org/ )
* [ www.perl.org ]( https://www.perl.org/ )
* [ How to install, remove or purge  and uninstall perlbrew On Ubuntu 16.04 ]( https://www.devmanuals.net/install/ubuntu/ubuntu-16-04-LTS-Xenial-Xerus/how-to-install-perlbrew.html )
* [ How to install perl modules through CPAN on Ubuntu ]( http://www.sudobash.net/bash-how-to-install-perl-modules-through-cpan-on-ubuntu-2/ )

## vagrant testing 
```shell
vagrant up
vagrant ssh
```

## install perlbrew

###  install, initialize and list available perl versions

```shell
# Install requirements. We will use curl in order to install perlbrew.
sudo apt-get install -y curl

# Installing perlbrew to our home directory
curl -L https://install.perlbrew.pl | bash

# source
#
# setup your perlbrew environment (paths, commands and so on)
source ~/perl5/perlbrew/etc/bashrc

# Initialize
perlbrew init

# test
perlbrew

# See what is available
perlbrew available
```

Output example

```shell
  perl-5.27.0
  perl-5.26.0
  perl-5.24.1
  perl-5.22.3
  perl-5.20.3
  perl-5.18.4
  perl-5.16.3
  perl-5.14.4
  perl-5.12.5
  perl-5.10.1
  perl-5.8.9
  perl-5.6.2
  perl5.005_04
  perl5.004_05
  cperl-5.27.0
```

## Day to day usage

```shell
# Set up perlbrews environment
source ~/perl5/perlbrew/etc/bashrc

# run your commands
```

### install perl(s)

### example of failed installation attempt

This is included because perl installations fail often. See the section titled **Installing a perl using the `--notest` option**" below for a way to install this version of perl when the included tests fail..

```shell
perlbrew install 5.14.2
```

By opening  a second terminal session and running the following command you can watch the installation progress by using the `tail` command:

```shell
tail -f ~/perl5/perlbrew/build.perl-5.14.2.log
```

The installation failed on my system (this happens frequently) and using the `cat` command I see the details:

```shell
cat /home/vagrant/perl5/perlbrew/build.perl-5.14.2.log
```

The end of our log:

```shell
Test Summary Report
-------------------
op/numconvert.t                                                 (Wstat: 0 Tests: 1444 Failed: 12)
  Failed tests:  104, 108, 112, 136, 140, 144, 152, 156
                160, 168, 172, 176
op/range.t                                                      (Wstat: 0 Tests: 141 Failed: 25)
  Failed tests:  84-95, 99-111
../lib/h2ph.t                                                   (Wstat: 0 Tests: 6 Failed: 2)
  Failed tests:  5-6
Files=2085, Tests=454853, 446 wallclock secs (16.92 usr  5.64 sys + 161.26 cusr 13.72 csys = 197.54 CPU)
Result: FAIL
makefile:947: recipe for target 'test_harness' failed
make: *** [test_harness] Error 39
##### Brew Failed #####
```

### Installing a perl using the --notest option

Here we attempt an installation of the same version using the `--notest` option:

```shell
perlbrew --notest install 5.14.2
```

In this case my perl works as the included tests where not run. I `cat` my log file again:

```shell
cat /home/vagrant/perl5/perlbrew/build.perl-5.14.2.log
```

and at the end I see that brew has finished the installation:

Output example:

```shell
...
  /home/vagrant/perl5/perlbrew/perls/perl-5.14.2/man/man1/find2perl.1
  /home/vagrant/perl5/perlbrew/perls/perl-5.14.2/man/man1/s2p.1
  /home/vagrant/perl5/perlbrew/perls/perl-5.14.2/man/man1/psed.1
make[1]: Leaving directory '/home/vagrant/perl5/perlbrew/build/perl-5.14.2'
##### Brew Finished #####
```

### Installing with the --force flag

In cases where the `--notest` flag does not work you could also try to force the installation running the following command:

```shell
perlbrew --force install 5.14.2
```

### Setting a new default perl version for your user

The default version of perl for all users on your system is the version of perl that gets installed when you run `sudo apt-get install perl`. This version of perl is the one that gets executed when you, or a program, runs a perl command. This version of perl gets run because it is the first executable file called `perl` on your **path**.

First lets take a look at where the systems default version of perl is located using the `which` command:

```shell
which perl
```

output:

```shell
/usr/bin/perl
```

Next lets look at your users path variable using the echo command followed by  `$ ` and the PATH variable name.

```shell
echo $PATH
```

Output example:

```shell
/home/csteel/bin:/home/csteel/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

Next lets take a look at the location of the version of perl that is installed on the system when someone runs `sudo apt-get install -y perl`.

```shell
which perl
```

output:

```shell
/usr/bin/perl
```

keeping in mind that each path defined in your users PATH variable is separated using colon `:` you can see that directory containing the systems version of perl, in this case `/usr/bin`,  is located nearer to the end of my path just before `/sbin`.

#### setting a new default version of perl using perlbrew

First I will list the versions of perl available to me in my home using perlbrew.

```shell
perlbrew list
```

example output:

```shell
  perl-5.14.2
```

I only have one, **5.14.2**. I am going to make it my default version of perl by running the following command:

```shell
perlbrew switch perl-5.14.2
```

#### testing 

Next I will run some tests to ensure that my default version of perl has changed. First I am going to run the `which` command again:

```shell
which perl
```

Notice that the first perl on my path, the one that gets displayed when I type `which perl` is now in my home directory here:

```shell
/home/csteel/perl5/perlbrew/perls/perl-5.14.2/bin/perl
```

by taking a look at the value of my PATH variable using 

```shell
echo $PATH
```

I see that the directory `/home/csteel/perl5/perlbrew/perls/perl-5.14.2/bin` , the directory that contains perl version 5.14.2 the second path in my PATH which means that the version of perl on this second path is the version of perl that gets executed rather than the system version located at `/usr/bin/perl. is now at the beginning of my path.

```shell
home/csteel/perl5/perlbrew/bin:/home/csteel/perl5/perlbrew/perls/perl-5.14.2/bin:/home/csteel/bin:/home/csteel/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

