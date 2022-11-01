### install notes created on 11/10/2021 creating a new machine in TRI IE-dev account 

### Local Laptop Setup 
	### Install AWS CLI v2 or latest 
	[windows]
	update aws CLI https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-windows.html
		msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
	[linux]
	https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html
		curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
		unzip awscliv2.zip
		sudo ./aws/install


0] login to SSO managemnt console.

1] setup or modify a temp profile in sso login page to
		C:\Users\Shak Kathirvel\.aws\credentials
		C:\Users\Shak Kathirvel\.aws\config
	from aws admin console after sso login

	[ aws credentials check]
		aws --version
		aws configure list-profiles
		aws --profile 967655172285_ie_dev_AdministratorAccess s3api list-buckets 
			aws --profile 967655172285_ie_dev_AdministratorAccess s3 ls s3://tri-s3-encryption-report  --human-readable --summarize --page-size 1 
		aws --profile 929292782238_tri-na_AdministratorAccess s3api list-buckets  
	#remote-dev  | linux or windows latest CLI VERIFICATION 
	  1 aws --version
	   2 aws configure
	   3 aws configure list
	   4 aws configure help
	   5 aws configure list-profiles
	   6 aws configure get
	   7 aws configure get help
	   8 aws s3 ls s3://tri-aws-cur  --human-readable --summarize --page-size 1 --profile 929292782238_AdministratorAccess
	   9 aws s3 ls s3://tri-aws-cur  --human-readable --summarize --page-size 1
	  10 aws s3 ls s3://tri-aws-cur  --human-readable --summarize --page-size 1 --profile 929292782238_AdministratorAccess
	  11 aws --version
	  12 aws s3 ls s3://tri-aws-cur  --human-readable --summarize --page-size 1 967655172285_ie_dev_AdministratorAccess
	  13 aws s3 ls s3://tri-aws-cur  --human-readable --summarize --page-size 1 --profile 967655172285_ie_dev_Administra...
	  14 aws s3 ls s3://tri-aws-cur  --human-readable --summarize --page-size 1 --profile 967655172285_ie_dev_Administra...
	  15 aws s3 help
	  17 aws s3 ls help
	  18 aws s3api list-buckets --profile 967655172285_ie_dev_AdministratorAccess


2] create a temp instance in account.us-east-1.vpc.privatesubnet
	2.1] generate a pem file.

3] pem key file name = 2021_may_07_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596
	"C:\Users\Shak Kathirvel\Downloads\2021_may_07_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596.pem"
	 	
967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-
4] test the remote session using ssm
aws ssm start-session --target  i-0f1beb04ee3109b92 --profile 967655172285_ie_dev_AdministratorAccess
	https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-troubleshooting.html#plugin-not-found
		https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html
			https://s3.amazonaws.com/session-manager-downloads/plugin/latest/windows/SessionManagerPluginSetup.exe

5] setup %USER%\.ssh\config for OpenSSH
	https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started-enable-ssh-connections.html
		C:\Users\Shak Kathirvel\.ssh\config
		# SSH over Session Manager
		Host i-* mi-*
		    ProxyCommand C:\WINDOWS\system32\WindowsPowerShell\v1.0\PowerShell.exe  "aws ssm start-session --target %h --document-name AWS-StartSSHSession --parameters portNumber=%p --profile 967655172285_ie_dev_AdministratorAccess"

		# SSH over Session Manager [ this article finally helped - https://pub.towardsai.net/how-to-do-remote-development-with-vs-code-using-aws-ssm-415881d249f3]
		Host i-0f1beb04ee3109b92
			IdentityFile "C:\Users\Shak Kathirvel\Downloads\2021_may_07_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596.pem"
			User ubuntu

		https://aws.amazon.com/premiumsupport/knowledge-center/systems-manager-ssh-vpc-resources/

[use short form in the next line] ssh -i "C:\Users\Shak Kathirvel\Downloads\2021_may_07_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596.pem" ubuntu@i-0f1beb04ee3109b92		

6] ssh  ubuntu@i-0f1beb04ee3109b92		

7] scp commands
	[scp recursive copy]  https://www.webtiara.com/how-to-use-ssh-and-scp-windows-10-file-folder-upload/
		scp -r "C:\Users\Shak Kathirvel\Documents\shak.at.hotmail.com.gitroot\aws.code.root\example001"  i-0f1beb04ee3109b92:/home/ubuntu/from_windows/project2

	[dont use ] rsync -avh -e ssh /source/path/ host:/destination/path [from https://unix.stackexchange.com/questions/105140/how-to-copy-only-new-files-using-scp-command]
	[dont use ] C:\Tools\cwrsync_6.2.1_x64\bin\rsync.exe -avuzh -e ssh -F "C:\Users\config" example002/ i-0f1beb04ee3109b92:/home/ubuntu/from_windows/project1

	[scp recurisve copy based on date ]
	cd "C:\Users\Shak Kathirvel\Documents\shak.at.hotmail.com.gitroot\aws.code.root\"
	forfiles /P directory /S /D +04/08/2021 [https://www.windows-commandline.com/find-files-based-on-modified-time/]

	[scp recurisve copy based on file filters and date]
	"C:\Users\Shak Kathirvel\Documents\shak.at.hotmail.com.gitroot"
	forfiles /P aws.code.root\vpc_logs_analysis_with_athena /M *.py /S /D +03/13/2020 /c "cmd /c scp @file  i-0f1beb04ee3109b92:/home/ubuntu/from_windows/project2"

8]  linux tree command.
	ubuntu@ip-10-239-4-181:~$ tree -hfatDU  `pwd`/from_windows/
e.#remote-dev | linux or windows latest CLI setup.
______________
s.#remote-dev | linux or windows latest CLI VERIFICATION 
aws s3api list-buckets --profile 929292782238_tri-na_AdministratorAccess
	aws s3 ls s3://mmt-test --human-readable --summarize --page-size 1 --profile 929292782238_tri-na_AdministratorAccess
e.#remote-dev | linux or windows latest CLI VERIFICATION 	
______________
s.#remote-dev  | SCP  command for copying multiple file filter with date filters. 
[scp]
forfiles /P "C:\Users\Shak Kathirvel\Downloads" /M *.tar.gz /S /D +05/01/2021 /c "cmd /c scp @file  i-0f1beb04ee3109b92:/home/ubuntu/from_windows/project2

[unison]
[error| did not work]C:\Tools\unison-v2.51.3\bin\unison.exe "C:\Users\Shak Kathirvel\gitroot\ouroboros-provision\assets" -servercmd /home/ubuntu/Tools/unison-v2.51.3/bin ssh://i-0f1beb04ee3109b92:/home/ubuntu/from_windows/project2
cd "C:\Users\Shak Kathirvel\Downloads"
[error | did not work] C:\Tools\unison-v2.51.3\bin\unison.exe -testServer delme.json -servercmd /home/ubuntu/Tools/unison-v2.51.3/bin/unison ssh://i-0f1beb04ee3109b92/home/ubuntu/from_windows/project2/delme.json

C:\Users\Shak Kathirvel\Documents>C:\Tools\unison-v2.51.3\bin\unison.exe -testServer delme.json -servercmd /home/ubuntu/Tools/unison-v2.51.3/bin/unison ssh://i-0f1beb04ee3109b92/home/ubuntu/from_windows/project2/delme.json
Unison 2.51.3 (ocaml 4.11.1): Contacting server...
Unison server failed: Fatal error: Fatal error during unmarshaling (input_value_from_block: bad object),
possibly because client and server have been compiled with different versions of the OCaml compiler.
Fatal error: Lost connection with the server

[cause and solution]
	https://github.com/bcpierce00/unison/issues/166
___
c:\Tools\unison-v2.51.4_rc2\bin\unison.exe -testServer delme.json -servercmd /home/ubuntu/Tools/unison-v2.51.4_rc2/bin/unison ssh://i-0f1beb04ee3109b92/home/ubuntu/from_windows/project2/delme.json
e.#remote-dev  | SCP  command for copying multiple file filter with date filters. 
______________
s.#remote-dev | 	file perms for .ssh directory and files under it.
	ubuntu@ip-10-239-4-163:~$ ls -haltF
	total 28K
	drwxr-xr-x 4 ubuntu ubuntu 4.0K May 20 00:05 ./
	drwx------ 2 ubuntu ubuntu 4.0K May 20 00:05 .cache/
	drwx------ 2 ubuntu ubuntu 4.0K May 20 00:04 .ssh/   [ perms for .ssh directory
	drwxr-xr-x 4 root   root   4.0K May 19 23:54 ../
	-rw-r--r-- 1 ubuntu ubuntu  220 Feb 25  2020 .bash_logout
	-rw-r--r-- 1 ubuntu ubuntu 3.7K Feb 25  2020 .bashrc
	-rw-r--r-- 1 ubuntu ubuntu  807 Feb 25  2020 .profile
	ubuntu@ip-10-239-4-163:~$ ls -haltF .ssh
	total 12K
	drwxr-xr-x 4 ubuntu ubuntu 4.0K May 20 00:05 ../
	drwx------ 2 ubuntu ubuntu 4.0K May 20 00:04 ./
	-rw------- 1 ubuntu ubuntu  411 May 20 00:04 authorized_key

1. create a temp instance in account-no_us-east-1_vpcid_privatesubnet [<account>/<us-east-1>/<vpc>.<privatesubnet>] with no ssh keys
	- attach ec2 instance profile role with policy containing **AmazonSSMManagedInstanceCore**
		[refer prereqs: at https://pub.towardsai.net/how-to-do-remote-development-with-vs-code-using-aws-ssm-415881d249f3]
	- wait for 5+ minutes for ssm service "include" the newly created instance as ssm managed instance
	- test with either console or aws cli ssm command.
	- aws ssm start-session --target <instance-id> --profile 967655172285_ie_dev_AdministratorAccess
		[debugging references ]
		https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-troubleshooting.html#plugin-not-found
			https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html
			https://s3.amazonaws.com/session-manager-downloads/plugin/latest/windows/SessionManagerPluginSetup.exe
2. after the instance creation note down the instance number i-XXXXXXXXXXX
#remote-dev | create ssh key pairs using openssh tools
3. create the  ssh key pairs. .pem and .pub files by running ssh-keygen in windows cmd line tools.
	C:\Users\Shak Kathirvel\Documents>echo %PATH% ; echo "%path:;="&echo "%"
	C:\Users\Shak Kathirvel\Documents>DIR /b/d/s C:\WINDOWS\System32\OpenSSH\
		C:\WINDOWS\System32\OpenSSH\scp.exe
		C:\WINDOWS\System32\OpenSSH\sftp.exe
		C:\WINDOWS\System32\OpenSSH\ssh-add.exe
		C:\WINDOWS\System32\OpenSSH\ssh-agent.exe
		C:\WINDOWS\System32\OpenSSH\ssh-keygen.exe
		C:\WINDOWS\System32\OpenSSH\ssh-keyscan.exe
		C:\WINDOWS\System32\OpenSSH\ssh.exe
4. C:\Users\Shak Kathirvel\Documents>C:\WINDOWS\System32\OpenSSH\ssh-keygen.exe	
	[refer prereqs: at https://pub.towardsai.net/how-to-do-remote-development-with-vs-code-using-aws-ssm-415881d249f3]
5. create the ssh keys file name
	2021_may_19_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-0114670297365c953.pem
	<date>_<accountno>_<account-no>_<us-east-1>_<vpcid>_<privatesubnet>_<instanceid>.pem
		C:\Users\Shak Kathirvel\Documents\2021_may_19_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-0114670297365c953.pem
		C:\Users\Shak Kathirvel\Documents\2021_may_19_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-0114670297365c953.pub
6. copy the pubfile contents to the remote machine /home/ubunutu/.ssh/authorized_keys
	- aws ssm start-session --target i-0114670297365c953 --profile 967655172285_ie_dev_AdministratorAccess
	- ssm-user@ip-10-239-4-163:/var/snap/amazon-ssm-agent/3552$ sudo ls -haltF /home/ubuntu/.ssh/
		total 12K
		drwxr-xr-x 4 ubuntu ubuntu 4.0K May 20 00:27 ../
		drwx------ 2 ubuntu ubuntu 4.0K May 20 00:04 ./
		-rw------- 1 ubuntu ubuntu  411 May 20 00:04 authorized_keys
	- ssm-user@ip-10-239-4-163:/var/snap/amazon-ssm-agent/3552$ sudo vim /home/ubuntu/.ssh/authorized_keys
	- cut and paste the .pub file contents.
7. check the acls [rwx--- for the .ssh dir and .ssh/authorized_keys file]
	ubuntu@ip-10-239-4-163:~$ ls -haltF
		total 28K
		drwx------ 2 ubuntu ubuntu 4.0K May 20 00:04 .ssh/   [ perms for .ssh directory

		ubuntu@ip-10-239-4-163:~$ ls -haltF .ssh
		-rw------- 1 ubuntu ubuntu  411 May 20 00:04 authorized_key	
8. ssh  ubuntu@	i-0114670297365c953	
	ubuntu@ip-10-239-4-163:~$ tree -hftpaDu  ~
	/home/ubuntu/.vscode-server/data/User/workspaceStorage/4dde2be01a51f59aa395c55c9798d27e/acc=967655172285.code-workspace
---
e.#remote-dev | 	file perms for .ssh directory and files under it.
______________
s.#remote-dev |  software needed after the instance creation.
sudo apt update 
sudo apt install unzip
sudo apt install tree
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install	
pyenv [https://realpython.com/intro-to-pyenv/]
	sudo apt-get install -y make build-essential libssl-dev zlib1g-dev \
	libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev \
libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python-openssl

	WARNING: seems you still have not added 'pyenv' to the load path.
	
	
	# (The below instructions are intended for common
	# shell setups. See the README for more guidance
	# if they don't apply and/or don't work for you.)
	
	# Add pyenv executable to PATH and
	# enable shims by adding the following
	# to ~/.profile:
	
	export PYENV_ROOT="$HOME/.pyenv"
	export PATH="$PYENV_ROOT/bin:$PATH"
	eval "$(pyenv init --path)"
	
	# If your ~/.profile sources ~/.bashrc,
	# the lines need to be inserted before the part
	# that does that. See the README for another option.
	
	# If you have ~/.bash_profile, make sure that it
	# also executes the above lines -- e.g. by
	# copying them there or by sourcing ~/.profile
	
	# Load pyenv into the shell by adding
	# the following to ~/.bashrc:
	
	eval "$(pyenv init -)"
	
	# Make sure to restart your entire logon session
	# for changes to profile files to take effect.
	
	# Load pyenv-virtualenv automatically by adding
	# the following to ~/.bashrc:
	
	eval "$(pyenv virtualenv-init -)"
	
	exec "$SHELL" # Or just restart your terminal
e.#remote-dev |  software needed after the instance creation.	
______________
s.#remote-dev |  after the instance creation access it using ssm start session <instanceid> and ssh <instanceid>
aws ssm start-session --target i-0416aa3c03d5c6392 --profile 276724084465_tars_account_AdministratorAccess
ssh  ubuntu@i-0416aa3c03d5c6392
e.#remote-dev |  after the instance creation access it using ssm start session <instanceid> and ssh <instanceid>
______________
s.#remote-dev |  apt prep  and prereqs
sudo apt-get upgrade
sudo apt-get update
sudo apt update
sudo apt upgrade
e.#remote-dev |  apt prep  and prereqs
______________
s.#remote-dev |  software needed after the instance creation.
sudo apt update 
sudo apt install unzip
sudo apt install tree
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install	
aws --profile  276724084465_tars_account_AdministratorAccess describe-vpcs 
aws --profile 276724084465_tars_account_AdministratorAccess s3api list-buckets 
	aws --profile 276724084465_tars_account_AdministratorAccess s3 ls s3://tarssagemaker --human-readable --summarize --page-size 1 
e.#remote-dev |  software needed after the instance creation.
______________
s.#remote-dev |  pyenv prereqs
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python-openssl
sudo apt-get upgrade
sudo apt-get update
sudo apt update
sudo apt upgrade
e.#remote-dev |  pyenv prereqs
______________
s.#remote-dev |  pyenv python instal after the instance creation.
curl https://pyenv.run | bash
	update ~/.profile
	update ~/.bashrc
	pyenv install --list | grep " 3\.[678]"
e.#remote-dev |  pyenv python instal after the instance creation.	
______________
s.#remote-dev |  scp commands
124224456861 - ML account!
[scp]
scp   i-0416aa3c03d5c6392:/home/ubuntu/try1.csv "C:\Users\Shak Kathirvel\Documents\." 
scp   i-0416aa3c03d5c6392:/home/ubuntu/276724084465-ALL-REGIONS-ALL-subnets.csv "C:\Users\Shak Kathirvel\Documents\." 
scp   i-0416aa3c03d5c6392:/home/ubuntu/try1.sh "C:\Users\Shak Kathirvel\Documents\." 
forfiles /P "C:\Users\Shak Kathirvel\Downloads" /M *.tar.gz /S /D +05/01/2021 /c "cmd /c scp @file  i-0f1beb04ee3109b92:/home/ubuntu/from_windows/project2
e.#remote-dev |  scp commands
______________
s.#remote-dev |  unison setup and usage.
[unison] config that works 
[ using profile001 ]
	C:\Users\Shak Kathirvel>c:\Tools\unison-v2.51.4_rc2\bin\unison.exe profile001
		Unison 2.51.3.70 (ocaml 4.12.0): Contacting server...
		Connected [//X1C-SKathirvel/C:/Users/Shak Kathirvel/eclipsegit/AWSLambdaFunctions -> //ip-10-239-4-181//home/ubuntu/from_windows/project3]
		Looking for changes
		  Waiting for changes from server
		Reconciling changes
		Nothing to do: replicas have not changed since last sync.

[using default ]
	C:\Users\Shak Kathirvel>c:\Tools\unison-v2.51.4_rc2\bin\unison.exe default
		Unison 2.51.3.70 (ocaml 4.12.0): Contacting server...
		Connected [//X1C-SKathirvel/C:/Tools/cwrsync_6.2.1_x64 -> //ip-10-239-4-181//home/ubuntu/from_windows/project1]
		Looking for changes
		  Waiting for changes from server
		Reconciling changes
		Nothing to do: replicas have not changed since last sync.

[ .unison profiles ]		
	C:\Users\Shak Kathirvel>dir /b/s/d .unison
	C:\Users\Shak Kathirvel\.unison\ar03fb67
	C:\Users\Shak Kathirvel\.unison\ar51c1b0
	C:\Users\Shak Kathirvel\.unison\default.prf
	C:\Users\Shak Kathirvel\.unison\fp03fb67
	C:\Users\Shak Kathirvel\.unison\fp51c1b0
	C:\Users\Shak Kathirvel\.unison\profile001.prf
	C:\Users\Shak Kathirvel\.unison\unison.log
	
[unison command line - not using profile ] 	
	for copying single files between remote and windows machine just use scp	

[visual code control k change for tmux ]
https://stackoverflow.com/questions/50569100/vscode-how-to-make-ctrlk-kill-till-the-end-of-line
	If you have years of muscle memory of Unix shell and want to prevent VSCode to capture your keyboard shortcuts, turn off allowChords. In newer VSCode version you can simply open File -> Preferences -> User (tab), search for allowChords and uncheck it.
	"terminal.integrated.sendKeybindingsToShell": true
e.#remote-dev |  unison setup and usage.	
______________
s.#remote-dev | Git error: �Host Key Verification Failed� when connecting to remote repository
https://stackoverflow.com/questions/13363553/git-error-host-key-verification-failed-when-connecting-to-remote-repository
[this worked - for unix]	ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
[this worked - for windows]	C:\Users\Shak Kathirvel>ssh-keyscan -t rsa github.com >> "C:\Users\Shak Kathirvel\.ssh\known_hosts"
	# github.com:22 SSH-2.0-babeld-22beb20a
e.#remote-dev | Git error: �Host Key Verification Failed� when connecting to remote repository
______________
s.#remote-dev | json keyboard shorts cuts. for maxi terminal and toggle terminal 
	You can access the file using the shortcut cmd+shift+p, then write/select Preferences: Open Keyboard Shortcuts (JSON).  - User kb shortcuts not default.
		edit /C:/Users/Shak Kathirvel/AppData/Roaming/Code/User/keybindings.json
	add the following 
	    {
	        "key": "ctrl+alt+m",
	        "command": "workbench.action.toggleMaximizedPanel",
	        "when": "!terminalFocus"
	    },
	    {
	        "key": "ctrl+`",
	        "command": "-workbench.action.terminal.toggleTerminal",
	        "when": "!terminalFocus"
	    },
	    {
	        "key": "ctrl+alt+m",
	        "command": "workbench.action.terminal.toggleTerminal",
	        "when": "terminalFocus"
	    }

	https://stackoverflow.com/questions/48511956/toggle-between-fullscreen-editor-and-terminal-in-vs-code
e.Wednesday, June 16, 2021
}
e.#remote-dev | json keyboard shorts cuts. for maxi terminal and toggle terminal 
______________
s.#remote-dev | [ unison setup in remote machine ]
	scp -r  "C:\Users\Shak Kathirvel\Downloads\unison-v2.51.4_rc2+ocaml-4.12.0+x86_64.linux.tar.gz" i-0114670297365c953://home/ubuntu/Downloads
[unision path in remote machine] /home/ubuntu/Tools/unison-v2.51.4_rc2/bin/unison
	[in the remote machine] 
		379  cd ~/Downloads/
		380  ls
		381  gunzip unison-v2.51.4_rc2+ocaml-4.12.0+x86_64.linux.tar.gz 
		382  tar tvf unison-v2.51.4_rc2+ocaml-4.12.0+x86_64.linux.tar 
		383  mkdir -p /home/ubuntu/Tools/unison-v2.51.4_rc2/
		384  cd ~/Tools/unison-v2.51.4_rc2/
		385  tar xvf ~/Downloads/unison-v2.51.4_rc2+ocaml-4.12.0+x86_64.linux.tar 
		389  tree -hft ~/Tools/

	c:\Tools\unison-v2.51.4_rc2\bin\unison.exe tri-cur-cdk.prf
e.#remote-dev | [ unison setup in remote machine ]
______________
s.#remote-dev | [ tri tars account inventory work ] 
	scp -r  "C:\Users\Shak Kathirvel\Downloads\unison-v2.51.4_rc2+ocaml-4.12.0+x86_64.linux.tar.gz" i-0416aa3c03d5c6392://home/ubuntu/Downloads
	c:\Tools\unison-v2.51.4_rc2\bin\unison.exe tri-tars-account-work.prf
	github check in.
		C:\Users\Shak Kathirvel\eclipsegit\aws.code.root\tri-tars-work-2021-jun
e.#remote-dev | [ unison setup in remote machine ]
______________

11/9/2021
[using managemnt console]
1. created new instance in the acc/region/vpc/subnet(private) = 9676...2285/us-east-1/vpc-0cd3bba277ee01b95/subnet-014c0958ede34e596
	choose OS
		ubuntu@ip-10-239-4-163:~$ lsb_release -a
		No LSB modules are available.
		Distributor ID: Ubuntu
		Description:    Ubuntu 20.04.2 LTS
		Release:        20.04
		Codename:       focal
	Choose VM 
		t2.2xlarge = 8vCPUs x 32GiB	
	Choose instance profile
		a role that contains **AmazonSSMManagedInstanceCore**
	Choose security group
		inbound allow ssh port 22 from 0.0.0.0/0
		outbound all 
	Choose No keys [ we will generate the keys seperately ]
		* Note down the instance = i-05435d54cc10051ce	

2. Generate the ssh keys [ in the windows laptop ].
	<date>_<accountno>_<account-no>_<us-east-1>_<vpcid>_<privatesubnet>_<instanceid>.pem
		C:\Users\Shak Kathirvel\Documents\2021_nov_11_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-05435d54cc10051ce.pem
		C:\Users\Shak Kathirvel\Documents\2021_may_19_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-05435d54cc10051ce.pub

	C:\Users\Shak Kathirvel>echo "%path:;="&echo "%"
			"C:\Program Files (x86)\Common Files\Oracle\Java\javapath"
			"C:\amazon-corretto-8.232.09.1\jdk1.8.0_232\bin"
			"C:\WINDOWS\system32"
			"C:\WINDOWS"
			"C:\WINDOWS\System32\Wbem"
			"C:\WINDOWS\System32\WindowsPowerShell\v1.0\"
			"C:\WINDOWS\System32\OpenSSH\"
			"C:\Program Files\PuTTY\"
			"C:\Program Files (x86)\WinMerge"
			"C:\Program Files\Intel\WiFi\bin\"
			"C:\Program Files\Common Files\Intel\WirelessCommon\"
			"C:\Program Files\WinMerge"
			"c:\java-se-8u40-ri\bin\"
			"C:\Program Files\Git2.26.0\cmd"
			"C:\AWS_SAM_CLI_64_PY3\bin\"
			"C:\AWSCLIV2\"
			"C:\Program Files\Amazon\SessionManagerPlugin\bin\"
			"C:\Program Files\Docker\Docker\resources\bin"
			"C:\ProgramData\DockerDesktop\version-bin"
			"C:\Users\Shak Kathirvel\.pyenv\pyenv-win\bin"
			"C:\Users\Shak Kathirvel\.pyenv\pyenv-win\shims"
			"C:\Users\Shak Kathirvel\AppData\Local\Programs\Python\Python38-32\Scripts\"
			"C:\Users\Shak Kathirvel\AppData\Local\Programs\Python\Python38-32\"
			"C:\Users\Shak Kathirvel\AppData\Local\Microsoft\WindowsApps"
			"c:\java-se-8u40-ri\bin\"
			"C:\Users\Shak Kathirvel\AppData\Local\GitHubDesktop\bin"
			"C:\Users\Shak Kathirvel\AppData\Local\Programs\Microsoft VS Code\bin"
			"C:\Users\Shak Kathirvel\AppData\Local\hyper\app-3.0.2\resources\bin"

			C:\Users\Shak Kathirvel>cd "C:\WINDOWS\System32\OpenSSH\"
			C:\Windows\System32\OpenSSH>dir
			Volume in drive C is Windows
			Volume Serial Number is 1488-438D

			Directory of C:\Windows\System32\OpenSSH

			03/19/2019  01:21 AM    <DIR>          .
			03/19/2019  01:21 AM    <DIR>          ..
			03/19/2019  01:21 AM           322,560 scp.exe
			03/19/2019  01:21 AM           390,144 sftp.exe
			03/19/2019  01:21 AM           491,520 ssh-add.exe
			03/19/2019  01:21 AM           384,512 ssh-agent.exe
			03/19/2019  01:21 AM           637,952 ssh-keygen.exe
			03/19/2019  01:21 AM           530,432 ssh-keyscan.exe
			03/19/2019  01:21 AM           882,688 ssh.exe
						7 File(s)      3,639,808 bytes
						2 Dir(s)  905,309,401,088 bytes free

		C:\Windows\System32\OpenSSH>ssh-keygen.exe
			Generating public/private rsa key pair.
			Enter file in which to save the key (C:\Users\Shak Kathirvel/.ssh/id_rsa): C:\Users\Shak Kathirvel\Documents\2021_nov_11_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-05435d54cc10051ce.pem
			Enter passphrase (empty for no passphrase):
			Enter same passphrase again:
			Your identification has been saved in C:\Users\Shak Kathirvel\Documents\2021_nov_11_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-05435d54cc10051ce.pem.
			Your public key has been saved in C:\Users\Shak Kathirvel\Documents\2021_nov_11_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-05435d54cc10051ce.pem.pub.

3. copy the pubfile contents from the windows laptop to the new remote machine /home/ubunutu/.ssh/authorized_keys
	cut and paste the .pub file contents.
		from C:\Users\Shak Kathirvel\Documents\2021_nov_11_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-05435d54cc10051ce.pem.pub
		to new remote machine /home/ubunutu/.ssh/authorized_keys
	- aws ssm start-session --target i-0114670297365c953 --profile 967655172285_ie_dev_AdministratorAccess [ in windows laptop]
	Starting session with SessionId: shak.kathirvel.ctr@tri.global-021bc569eadcb0e60
		$ bash
		ssm-user@ip-10-239-4-174:/var/snap/amazon-ssm-agent/4046$ ls -haltF  [ssm-agent home dir]
		total 8.0K
		drwxr-xr-x 5 root root 4.0K Nov 10 01:43 ../
		drwxr-xr-x 2 root root 4.0K Oct 21 23:29 ./
		
		ssm-user@ip-10-239-4-174:/var/snap/amazon-ssm-agent/4046$ sudo ls -haltF /home/ubuntu/.ssh/  [ubuntu home dir]
		total 8.0K
		drwx------ 2 ubuntu ubuntu 4.0K Nov  9 17:23 ./
		drwxr-xr-x 3 ubuntu ubuntu 4.0K Nov  9 17:23 ../
		-rw------- 1 ubuntu ubuntu    0 Nov  9 17:23 authorized_keys
		
		ssm-user@ip-10-239-4-174:/var/snap/amazon-ssm-agent/4046$ sudo vim /home/ubuntu/.ssh/authorized_keys
		ssm-user@ip-10-239-4-174:/var/snap/amazon-ssm-agent/4046$ sudo vim /home/ubuntu/.ssh/authorized_keys
		ssm-user@ip-10-239-4-174:/var/snap/amazon-ssm-agent/4046$ sudo ls -haltF /home/ubuntu/.ssh/
		total 12K
		drwx------ 2 ubuntu ubuntu 4.0K Nov 10 17:43 ./
		-rw------- 1 ubuntu ubuntu  411 Nov 10 17:43 authorized_keys
		drwxr-xr-x 3 ubuntu ubuntu 4.0K Nov  9 17:23 ../
		
4. check the acls /home/ubuntu/.ssh dir  [should be rwx---]
		ssm-user@ip-10-239-4-174:/var/snap/amazon-ssm-agent/4046$ sudo ls -haltF /home/ubuntu/
		total 24K
		drwx------ 2 ubuntu ubuntu 4.0K Nov 10 17:43 .ssh/
		drwxr-xr-x 4 root   root   4.0K Nov  9 17:32 ../
		drwxr-xr-x 3 ubuntu ubuntu 4.0K Nov  9 17:23 ./
		-rw-r--r-- 1 ubuntu ubuntu  220 Feb 25  2020 .bash_logout
		-rw-r--r-- 1 ubuntu ubuntu 3.7K Feb 25  2020 .bashrc
		-rw-r--r-- 1 ubuntu ubuntu  807 Feb 25  2020 .profile
5. check the acls /home/ubuntu/.ssh/authorized_keys file	[-rw-------]	
		ssm-user@ip-10-239-4-174:/var/snap/amazon-ssm-agent/4046$ sudo ls -haltF /home/ubuntu/.ssh
		total 12K
		drwx------ 2 ubuntu ubuntu 4.0K Nov 10 17:43 ./
		-rw------- 1 ubuntu ubuntu  411 Nov 10 17:43 authorized_keys
		drwxr-xr-x 3 ubuntu ubuntu 4.0K Nov  9 17:23 ../
		ssm-user@ip-10-239-4-174:/var/snap/amazon-ssm-agent/4046$ exit
exit

6. update the new instance in OpenSSH config file =  C:\Users\Shak Kathirvel\.ssh\config
	#SSH over Session Manager 967655172285_ie_dev_AdministratorAccess
	Host i-* mi-*
		ProxyCommand C:\WINDOWS\system32\WindowsPowerShell\v1.0\PowerShell.exe  "aws ssm start-session  --profile 967655172285_ie_dev_AdministratorAccess --target %h --document-name AWS-StartSSHSession --parameters portNumber=%p"

	6.1 [comment out this instance]
	# SSH over Session Manager 967655172285_ie_dev_AdministratorAccess |  ssm host=i-0f1beb04ee3109b92
	#Host i-0f1beb04ee3109b92
	#	IdentityFile "C:\Users\Shak Kathirvel\Downloads\2021_may_07_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596.pem"
	#	User ubuntu
		
	6.2 [comment out this instance]
	# SSH over Session Manager 967655172285_ie_dev_AdministratorAccess |  ssm host=i-0114670297365c953
	#Host i-0114670297365c953
	#	IdentityFile "C:\Users\Shak Kathirvel\Documents\2021_may_19_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-0114670297365c953.pem"
	#	User ubuntu	

	6.3 [add this new instance]
	# SSH over Session Manager 967655172285_ie_dev_AdministratorAccess |  ssm host=i-05435d54cc10051ce
	Host i-05435d54cc10051ce
		IdentityFile "C:\Users\Shak Kathirvel\Documents\2021_nov_11_iedev_967655172285_us-east-1_vpc-0cd3bba277ee01b95_subnet-014c0958ede34e596_i-05435d54cc10051ce.pem"
		User ubuntu	

7. login into ssh  ubuntu@i-05435d54cc10051ce	[ in windows laptop]
	ubuntu@ip-10-239-4-163:~$ tree -hftpaDu  ~

8.	test the new machine with meta data [ in the new remote machine ]
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/    

9. install the basics
sudo apt update 
sudo apt install unzip
sudo apt install tree
