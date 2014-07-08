## CloudFoundry PHP Build Pack Binary Build Scripts

This is a set of scripts that can be used to build the binary files required by the [CF PHP Build Pack].

This repo contains a set of bash scripts that can be used to build HTTPD 2.4.x, Nginx 1.5.x, Nginx 1.6.x, Nginx 1.7.x, PHP 5.4.x, PHP 5.5.x and a full set of extensions for both version of PHP.  

The scripts are configured with variables at the top and can be used to build different versions of each project and modules for the projects.  The scripts were originally designed to be run on Ubuntu 10.04, which is the current stack used by CF.  It has been expanded to support Ubuntu 12.04 and Ubuntu 14.04 as well.  Different OS & Version combinations are supported through different branches of the repository.

### Usage

#### Local

 1. Install the base OS.  Currently Ubuntu 10.04, 12.04 or 14.04.
 2. In a terminal in the OS, run `bash <( curl -s https://raw.githubusercontent.com/dmikusa-pivotal/cf-php-buildpack-binary-build-scripts/master/build/run_local.sh )`.  

This will download and run the local install script, which handles everything else.  See the `run_local.sh` script below for more details.

#### Remote

 1. Install the base OS.  Currently Ubuntu 10.04, 12.04 or 14.04.
 2. Setup an SSH Server on the base OS.  Add your [ssh key] to the `.ssh/authorized_keys` file on the base OS.
 3. Run `bash <( curl -s https://raw.githubusercontent.com/dmikusa-pivotal/cf-php-buildpack-binary-build-scripts/master/build/run_remote.sh ) [user]@<baseos-ip>`.

This will download and run the remote install script, which connects to the remote server and handles everything else.  Please note that step two is optional, but you'll have a much better time if passwordless login is enabled on your remote machine.  

See the `run_remote.sh` script below for details.

### Scripts

Here's a listing of the scripts and what they do.

#### Build Directory

This is the main directory for the scripts, and contains the following scripts.

|   Script Name   |   Explanation                                                 |
| --------------- | --------------------------------------------------------------|
|  run_local.sh    | Runs the full build suite locally.  This will install git, clone or update the repository, install and update all required dependencies and run all of the build scripts. |
|  run_remote.sh   | Runs the full build suite on a local host.  This will install git, clone or update the repository, install and update all required dependencies, run all of the build script and copy the build files from the remote server to your local machine. |
|  build-all.sh    | Builds all of the local packages.  This calls the individual build scripts in each of the component directories.  This script is called by `run_local.sh` and `run_remote.sh`. |
|  upload.sh       | Upload binaries to DropBox. |

#### Component Build Scripts

Each supported OS & Version combination has it's own branch.  These are largely similar, but each combination has it's own differences.  When you run the `run_local.sh` or `run_remote.sh` script, your OS and Version are determined by looking at `/etc/issue` and the correct branch is checked out.

The OS & Version specific branches contain the individual scripts which compile the various components.  These are stored in project subdirectories with a name and major version number and contain the build script.  The build script is responsible for building that individual component.  Most of the time this boils down to running `./configure && make && make install`, but some components are more complicated than that.  Regardless, this script is responsible for building and packaging the component.

Generally you should run the `run_local.sh` or `run_remote.sh` script to build the full suite of components, but you can run the individual `<component-version>/build.sh` script if you want to build just a single component.

#### Upload Script

If hosting files on DropBox, this script can be used to walk the `output` directory (i.e. where built files are downloaded) and automatically upload files to DropBox.

This script relies on the [Dropbox-Uploader] to handle the work of actually uploading files to DropBox.  To use the upload script, you must first run the `./dropbox/dropbox_uploader.sh` script.  The first time it's run, it will walk you through setting up your DropBox account to allow it to connect and upload files.  This creates a config file with your DropBox API credentials and OAuth token info.  It's stored in `~/.dropbox_uploader`.


[CF PHP Build Pack]:https://github.com/dmikusa-pivotal/cf-php-build-pack
[ssh key]:https://www.debian.org/devel/passwordlessssh
[Dropbox-Uploader]:https://github.com/andreafabrizi/Dropbox-Uploader
