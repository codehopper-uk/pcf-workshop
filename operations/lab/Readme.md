# Lab Setup

## Install [Vagrant](https://docs.vagrantup.com/v2/installation/)

The Bosh-Lite VM will be deployed to a Virtual Box hypervisor using Vagrant. Vagrant provides a convenient CLI to launch VMs using a configuration file. It needs to be invoked from a folder that contains a `VagrantFile` in order for a VM to be deployed. This file contains information such as the VM image, cpus, memory, network etc.

* Verify [Virtual Box](https://www.virtualbox.org/wiki/Downloads) is installed


	```
	VBoxManage --version
	```

* Download

	```
	curl -OL https://releases.hashicorp.com/vagrant/1.7.4/vagrant_1.7.4_x86_64.deb
	```

* Install

	```
	sudo dpkg -i vagrant_1.7.4_x86_64.deb
	```

* Verify

	```
	vagrant -v
	```

* From you lab folder spin up a Linux VM

	```
	vagrant up --provider virtualbox
	```


## Install CF CLI

You will be using the Cloud Foundry CLI to interact with the runtime once it has been provisioned. Unlike the commercial distribution of Pivotal Cloud Foundry bosh-lite does not have an Applications Dashboard and all inspection and interaction with the Cloud Foundry runtime needs to be done through the CLI.

* SSH into your jumpbox

	```
	vagrant ssh jumpbox
	```

* Download

	```
	curl -L "https://cli.run.pivotal.io/stable?release=linux64-binary&source=github" | tar -zx
	```

	> If curl command fails in you environment download using a browser


* Verify

	```
	cf help
	```

## Install Ruby

The Bosh CLI is based on Ruby. So you need to ensure Ruby is installed in your system to be able to provision Cloud Foundry to the Bosh-Lite VM. The Cloud Foundry CLI does is based on GO so it does not require the Ruby runtime.

### Using a Ruby Version Manager

* SSH into your jumpbox

	```
	vagrant ssh jumpbox
	```

* Install [Ruby Version Manager](http://rvm.io/).

	```
	curl -sSL https://rvm.io/mpapis.asc | gpg --import -
	\curl -sSL https://get.rvm.io | bash -s stable
	```

	> If the above commands fail then follow the instructions on the RVM web site for 	alternate installation methods.

* Install Ruby

	```
	rvm install ruby-2.2
	rvm --default use 2.2
	source ~/.bashrc
	```

* Verify 

	```
	ruby -v
	```

### Install to your system ruby

Alternatively install ruby via apt-get. Not that if you install the system ruby you will have to provision as sudo. 

```
sudo apt-get install ruby
```

> Installing a system Ruby is not recommended as undoing issues that may arise from mismatched gems are difficult and the need to run ruby commands as sudo.


## [Bosh Lite](https://github.com/cloudfoundry/bosh-lite)

### Install Bosh CLI

From your ubuntu jumbox.

$ Install bosh-cli

	```
	gem install bosh_cli --no-document
	```

* Verify Bosh

	```
	bosh target 192.168.50.4 lite
	bosh login admin admin
	bosh deployments
	```

* If you installed a system ruby then ensure bundler is installed

	```
	sudo gem install bundler --no-document
	```

### (Optional) Install Cloud Foundry

* Clone bosh-lite repository

	```
	git clone https://github.com/cloudfoundry/bosh-lite
	```

	> Note: If git is not install install it via ```sudo apt-get install git```.

* Download [spiff](https://github.com/cloudfoundry-incubator/spiff)

	```
	curl -OL https://github.com/cloudfoundry-incubator/spiff/releases/download/v1.0.7/spiff_linux_amd64.zip
	```

Once downloaded unzip it and copy the `spiff` binary to the `/usr/bin` folder.

* Provision Cloud Foundry

	```
	./bin/add-route
	./bin/provision_cf
	```

	> Run the provision script as sudo if you are using the system Ruby.

* Verify Cloud Foundry by loging in and creating your first org and space

	```
	cf login --skip-ssl-validation -a https://api.bosh-lite.com -u admin -p admin
	cf create-org pcf-training
	cf create-space development -o pcf-training
	```

### Troubleshooting

* Before you shutdown your system make sure you suspend the bosh-lite VM.

	```
	vagrant suspend
	```

* Once you have restarted you workstation then resume the bosh-lite VM.

	```
	vagrant up
	```

* Sometimes your bosh agents may become unresponsive and although your VM comes up you will not be able to access your Cloud Foundry deployment. To recover.

	```
	bosh cck
	```

* Instruct bosh to re-create the managed VMs when it gives you the recovery options. Once recovery is complete ensure the route to the warden containers within the bosh-lite VMs is re-established.

	```
	./bin/add-route
	```

	> If bosh appears to be running and you are still unable to connect then run `add-route`.

## Inspecting and Deploying a Bosh Release

### Clone Example Bosh Release

* Review [creating a bosh release](http://bosh.io/docs/create-release.html) documentaton.
 
* Review bosh [jobs](http://bosh.io/docs/jobs.html) and their [lifecycle](http://bosh.io/docs/job-lifecycle.html).

* Clone the following bosh release.

	```
	git clone https://github.com/cloudfoundry-community/jenkins-swarm-boshrelease
	```

	> Note: If git is not install install it via ```sudo apt-get install git```.
	
* Follow the instructions [here](https://github.com/cloudfoundry-community/jenkins-swarm-boshrelease) to build and deploy the release to your bosh-lite environment.



	
