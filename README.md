# fedora-swift2
This repository is for a set of build scripts for swift that are targeted initialy on fedora 25+ and swift 3.1+ 

The scripts are targeted by default on fedora 25 and swift-3.1-branch

I have build running swift installs for the following versions 

| Fedora   |      Branch    |  Tag |
|----------|:-------------:|------:|
| fc23|  swift-3.1-Branch |   |
| fc24|     swift-3.1-Branch    |    |
| fc25 |  swift-3.1-Branch  |      |
| fc23|  master | swift-3.0.2-RELEASE  |
| fc24|     master   |  swift-3.0.2-RELEASE  |
| fc25 |  master  |   swift-3.0.2-RELEASE   |

There are two scripts in this distribution, buildswift and runcontainer. Together 
they form a containerized build environment for swift on fedora. 

The use of containers means that the buld environment is the same on everybodies 
system, regardless of the particular setup they are running, the arrangement even 
allows running of the scripts on other operating systems to build an fc25 swift distribution. This elminates a whole class of bug resulting from different build enviroments. 

For example it would be quite feasible to use the system to build for fc25 on Mac OSX or Ubuntu.
Basically any environment that can run docker and conforms to basic posix functionality. 

I am hoping to encourage the swift devs to support fc25+ by providing a simple build environment they can run on thier macOSx or ubuntu systems. I would encourage you to request that they do at every opportunity. 

### Check your docker install
You can test your docker install by running the following command.

```
$docker run -it --name alpine_test alpine sh
```

This should present you with an alpine linux command prompt, alpine is 
a tiny linux, and the entire container is less than the size of an average photo.

To quit from the container just type:
```
#exit
```

If you dont have docker runing on your system then this tutorial from fc25 may help:

https://docs.docker.com/engine/installation/linux/fedora/

### Getting started
To get started checkout the repository to a folder in your home directory 
called "swift-work", you can use another folder, but you will need to alter the scripts to match.

I am planning to merge the two together and handle commandline parameters so that
it will be less rigid, but for now its better to follow the conventions here.

The steps are

1. Checkout the repo to "swift-work".

```
$cd ~
$git clone https://github.com/FedoraSwift/fedora-swift2.git ~/swift-work
$cd ~/swift-work
$chmod +x buildswift
$chmod +x runcontainer
$./runcontainer
```
2. Configure the script
    At the top of the script you will find several "variables".

    Set the $FEDORA_VERSION variable to the version you are building for

    Set the $BRANCH to either "master" or "swift-3.1-branch", releases are tags on the 
master branch, versions under development are often in thier own branch as per swift 3.1.

    Set the $TAG variable to the name of the release you want, if you are using a tag, then set the $BRANCH to 'master'
   , if you dont want to use a tag then set it to ''. 

3. When the container initializes and returns a command prompt do the following.

```
#cd ~/swift-source
#./buildswift
```
4. The container will update its packages, and install all the packages need to build the 
distribution, this is going to take a long long time, 10 hours is not unusual, so 
dont get impatient.
5. Eventually the process will complete, and the system will write an 
archive file called "swift-3.1-branch.fc25.tar.gz" to the folder on your
main os, if there are no errors you can use.

```
#exit
```
to quit the container

###Installing swift on fc25
I like to put my distributions into a folder called ~/bin so that they 
are out of the way

1. Create a ~/bin folder if it does not already exist.

2. Expand the tar file by doubleclick-ing it on the tar.gz file produced above in ~/swift-work. This will unpack the archive into a folder called 
after the name of the archive.

3. Move the folcer to ~/bin.

4. Symlink the name "swift" to that folder, this is so you can update to other versions just by changing the symlink, 
and rollback easily if it does not work out.

```
$cd ~/bin
$ln -s swift-3.1-branch.fc25 swift
```
Now you should edit your ~/.bashrc file to add $HOME/swift/usr/bin to your PATH. 

5. Test you got it right by executing the version command. 

```
$swift --version
Swift version 3.1-dev (LLVM a7c680da51, Clang e8941c161d, Swift d751c87f71)
Target: x86_64-unknown-linux-gnu

```

### Caveats

1. I had to disable some of the tests due to a problem with the lldb debugger build
see https://bugs.swift.org/browse/SR-100 for more details, as a result the system has
has to run the build twice, once with tests completely disabled, then it patches up ythe output folders, 
then it runs it again. This is still a work in progress, as im trying to eliminate the double build. 

2. There may be some problems with the posix process management functions in the swiftpm, i have seen a few 
issues but I am still investigating.

3. There is another issue with the Foundation libs, which requires a patch, between the two builds
you can see this in https://github.com/apple/swift-corelibs-foundation/pull/843a

4. The "import" function does not really work at all, it makes a bunch of assumptions about the version of gcc used and the layout of the /usr/include directory which are highly ubuntu specific. ubuntu 16.10 uses gcc 5.x whereas fedora uses gcc 6.x. Im looking a creating a new ld driver for the llbuild system that can handle the difference.  

### Todo

1. merge buildswsift and runcontrainer scripts so they can share configuration variables, and launch 
the container relevant to the particular os version it is building for..

2. Add support for fc23 and fc24, fc26 when it arrives. 

3. Add support for el7 and maybe el6. 





