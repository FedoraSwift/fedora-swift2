# fedora-swift2
This repository is for a set of build scripts for swift that are targeted initialy on fedora 25+ and swift 3.1+ 

There are two scripts in this distribution, buildswift and runcontainer. Together 
they form a containerized build environment for swift on fedora. 

The use of containers means that the buld enviroment is the same on everybodies 
system, regardless of the particular setup they are running, the arrangement even 
allows running of the scripts on other operating system to build an fc25 swift distibution.
For eaxmple it would be quite feasable to use the system to build for fc25 on macosx or unbuntu.
Basicaly any enviroment that can run docker and confirms to basic posix functionality. 

### Check your docker install
To get started checkout the repository to a folder in your home directory 
called "swift-work", you can use another folder, but you will need to alter the scripts to match, 
I am planning to merge the two together and handle commanfline paramaters so that
it will be less rigid, but for now its better to follow the conventions here

```
$docker run -it --name alpine_test alpine sh
```

This should presenbt you with an alpine linux command prompt, alpine is 
a tiny linux, and the entore container is less than the size of an average photo.

To quit from the container just type 
```
#exit
```

If you dont have docker runing on your system then this tutorial from fc25 may help

https://docs.docker.com/engine/installation/linux/fedora/

### Getting started
To get started checkout the repository to a folder in your home directory 
called "swift-work", you can use another folder, but you will need to alter the scripts to match, 
I am planning to merge the two together and handle commanfline paramaters so that
it will be less rigid, but for now its better to follow the conventions here

The steps are

1. checkout the repo to "swiftwork"

```
$cd ~
$git clone https://github.com/FedoraSwift/fedora-swift2.git ~/swift-work
$cd ~/swift-work
$chmod +x buildswift
$chmod +x runcontainer
$./runcontainer
```

2. When the container initializes and returns a command prompt do the following

```
#cd ~/swift-source
#./buildswift
```
3. The container will update its packages, and install all the packges need to build the 
distribution, this is going to take a long long time, 10 hours is not unusal, so 
dont get impatient.
4. eventualy the process will complete, and the system will write an 
archive file called "swift-3.1-branch.fc25.tar.gz" to the folder on your
main os, if there are no errors you can use.

```
#exit
```
to quit the container

###Installing swift on fc25
I like to put my distributions into a folder called ~/bin so that they 
are out of the way

1. create a ~/bin folder if it does not already exist

2. expand the tar file by doubleclicking it on the tar.gz file produced above in ~/swift-work. This will unpack the archive into a folder called 
after the name of the archive.

3. move the folcer to ~/bin

4. symlink the name "swift" to that folder, this is so you can update to other versions just by changing the symlink, 
and rollback easily if it does not work out.

```
$cd ~/bin
$ln -s swift-3.1-branch.fc25 swift
```
now you should edit your ~/.bashrc file to add $HOME/swift/usr/bin to your PATH. 

5. Test you got it right by executing the version command. 

```
$swift --version
Swift version 3.1-dev (LLVM a7c680da51, Clang e8941c161d, Swift d751c87f71)
Target: x86_64-unknown-linux-gnu

```

### Caveats

1. I had to disable some of the tests due to a problem with the lldb debugger build
see https://bugs.swift.org/browse/SR-100 for more details, as a result the system has
has to run the build twice, once with tests competley disabled, then it patches up ythe output folders, 
then it runs it again. This is still a work in progress, as im trying to eliminate the double build. 

2. There may be some problems with the posix process management functions in the swiftpm, i have seen a few 
issues but I am still investigating.

3. There is another isue with the Foundation libs, which requires a patch, between the two builds
you can see this in https://github.com/apple/swift-corelibs-foundation/pull/843

### Todo

1. merge buildswsift and runcontrainerscripts so they can share configuration variables.

2. add support for fc23 and fc24, fc26 when it arrives. 

3. add support for el7 and maybe el6 





