SLP: Docker Usage
=================

[Go up to the main SLP documents page](index.html) ([md](index.md))

Docker install

- You will have to enable virtualization, which is a BIOS option.  If you have already run VirtualBox on this machine, then you've done that.  Otherwise, there will be an option for "virtualization technology", "VT-X", "AMD-v", or similar.
- Windows can download and install from [https://docs.docker.com/docker-for-windows/install/](https://docs.docker.com/docker-for-windows/install/)
    - That version requires Windows 10; previous versions of Windows can use the install at [https://www.docker.com/products/docker-toolbox](https://www.docker.com/products/docker-toolbox)
    - Once installed, you run the Docker Quickstart Terminal
        - If you run into a problem such as the one described [here](https://github.com/docker/toolbox/issues/473), even if you are on a different version of Widnows, then it could be a driver issue: there is a bug with the NDIS6 driver, and you should select the NDIS5 driver during isntall (see [here](https://stackoverflow.com/questions/33725779/failed-to-open-create-the-internal-network-vagrant-on-windows10/33733486#33733486) for details).  Thus, you'll have to uninstall and reinstall docker, selecting the appropriate driver.
    - To connect to the mapped port, run `docker-machine ip default` in another docker quickstart terminal (while the other container is running).  You will get an IP address such as 192.168.99.100.  You can then view the mapped port at http://192.168.99.100:3000.
- Mac OS X can download and install from [https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)

Docker startup

- Download the docker image
	- Run `docker pull aaronbloomfield/slp` (it's a public image/repo)
        - You can run that last command in the future to upgrade to the latest version
	- The image name is now "aaronbloomfield/slp", which is long to type each time; you can rename it via `docker tag aaronbloomfield/slp slp`
        - That being said, the above commands only need to be run once (or if you are upgrading), and not each time you start up a container
    - Instead of the two docker commands above, you could build the image yourself from the included [Dockerfile](../utils/docker/Dockerfile), but that will take much longer
- Choose a directory to save your work do
    - If you save it in the container, if the container is removed (as often happens), then your work is lost, hence why we want to save it locally
	- We'll call that directory /path/to/work
    - Note that the docker-run command, below, will likely save files into that directory as root, so you may have to use `sudo` to modify them
- Start up docker: `docker run -it -p 3000:3000 -v /path/to/work:/root/work slp`
    - This will provide you with a command prompt *inside* the container

To create and run a rails app:

- You likely will want to be in the `/root/work` directory, since that is the one that the docker-run command above mapped to your host file system (`cd /root/work`)
- Run `rails new myapp` (we aren't specifying a DB, so it defaults to sqlite3); change "myapp" to the name of your project
- Change the last line in your Gemfile from `gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]` to just `gem 'tzinfo-data'`
- Remove the Gemfile.lock file in the project directory
- Run `bundle install` in the project directory
- You can now run `rails server` from the project directory
- You can view your app from [http://localhost:3000](http://localhost:3000)
- **NOTE:** if and when you re-launch the container, you will likely have to run `bundle install` from your project directory again

To create and run a django app:

- You likely will want to be in the `/root/work` directory, since that is the one that the docker-run command above mapped to your host file system (`cd /root/work`)
- Run `django-admin startproject mysite`
    - Change "mysite" to the name of your project
- In the mysite/ directory, run `python3 manage.py runserver 0.0.0.0:3000`
    - Note that you have to specify the IP address and port!
- You can view your app from [http://localhost:3000](http://localhost:3000)
