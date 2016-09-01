---
layout: page
title: "bitbucket"
category: tools
date: 2016-08-26 09:01:37
---

## BitBucket

BitBucket 是另外一个提供 git 托管服务的网站，飞出国有些内容会托管在 bitbucket 上，bitbucket 使用与 github 极其类似，因此，github 里介绍的内容在 bitbucket 里基本适用。

生成的 ssh key 可以在bitbucket上重用，设置到 bitbucket 账户里就可以了。具体参考官方指南。

- [Set up SSH for Git](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html)
- [Add an SSH key to an account](https://confluence.atlassian.com/bitbucket/add-an-ssh-key-to-an-account-302811853.html)

## Step 1. Ensure you have an SSH client installed

SSH should be included with the version of Git you installed. To make sure, do the following to verify your installation:

1.  From the Git Bash window, enter the following command to verify that SSH client is available:
    
		$ ssh -v  
		OpenSSH_4.6p1, OpenSSL 0.9.8e 23 Feb 2007  
		usage: ssh [-1246AaCfgkMNnqsTtVvXxY] [-b bind_address] [-c cipher_spec]  
		[-D [bind_address:]port] [-e escape_char] [-F configfile]  
		[-i identity_file] [-L [bind_address:]port:host:hostport]  
		[-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-p port]  
		[-R [bind_address:]port:host:hostport] [-S ctl_path]  
		[-w local_tun[:remote_tun]] [user@]hostname [command]
		
	If you have `ssh` installed, the terminal returns version information.
	If you don't have `ssh` installed, install it now with your package manager.

2.  List the contents of your `~/.ssh` directory.  
	    
	If you have not used SSH on **Git Bash** yet, you might see something like this:
	    
	    `$ ls -a ~/.ssh  
	    ls: /c/Users/emmap1/.ssh: No such file or directory`
	    
	If you have a default identity already, you'll see two `id_*` files:
	    
	    `$ ls -a ~/.ssh  
	    .    ..    id_rsa    id_rsa.pub  known_hosts`
	    
	In this case, the default identity uses RSA encryption (`id_rsa.pub`). If you want to use an existing default identity for your Bitbucket account, skip the next section and go to [create a config file](#SetupSSHforGit-step-4-ssh-for-git).

## Step 2. Set up your default identity

By default, the system adds keys for all identities to the `/Users/.ssh` directory. The following procedure creates a default identity.

1.  Open a terminal in your local system.
2.  Enter `ssh-keygen` at the command line.   
    The command prompts you for a file to save the key in:
    
    	$ ssh-keygen  
    Generating public/private rsa key pair.  
    Enter file in which to save the key (/c/Users/emmap1/.ssh/id_rsa):`
    
3.  Press enter to accept the default key and path, `/c/Users/.ssh/id_rsa`, or you can create a key with another name.  
    To create a key with a name other than the default, specify the full path to the key. For example, to create a key called `my-new-ssh-key`, you would enter a path like this at the prompt:
    
    `$ ssh-keygen  
    Generating public/private rsa key pair.  
    Enter file in which to save the key (/c/Documents and Settings/emmap1/.ssh/id_rsa): /c/Users/emmap1/My Documents/keys/my-new-ssh-key`
    
4.  Enter and renter a passphrase when prompted.  
    Unless you need a key for a process such as script, you should always provide a passphrase.   
    The command creates your default identity with its public and private keys. The whole interaction looks similar to the following: 
    
    	$ ssh-keygen  

	    Generating public/private rsa key pair.  
	    Enter file in which to save the key (/c/Users/emmap1/.ssh/id_rsa):  
	    Created directory '/c/Users/emmap1/.ssh'.  
	    Enter passphrase (empty for no passphrase):  
	    Enter same passphrase again:  
	    Your identification has been saved in /c/Users/emmap1/.ssh/id_rsa.  
	    Your public key has been saved in /c/Users/emmap1/.ssh/id_rsa.pub.  
	    The key fingerprint is: e7:94:d1:a3:02:ee:38:6e:a4:5e:26:a3:a9:f4:95:d4 emmap1@EMMA-PC`
    
5.  List the contents of `~/.ssh` to view the key files.  
    You should see something like the following:
    
	    $ ls ~/.ssh  
	    id_rsa id_rsa.pub`
    
    The command created two files, one for the public key (for example `id_rsa.pub`) and one for the private key (for example, `id_rsa`).

## Step 3. Create a SSH config file

1.  Using your favorite text editor, create a new file (at `~/.ssh/config`) or edit the file if it already exists.
2.  Add an entry to the configuration file using the following format:
    
		Host bitbucket.org
    		IdentityFile ~/.ssh/<privatekeyfile>

    The second line is indented. That indentation (a single space) is important, so make sure you include it. The second line is the location of your private key file.  If you are following along with these instructions, enter `id_rsa` for ``.
    When you are done editing, your configuration looks similar to the following:
    
		Host bitbucket.org
		 IdentityFile ~/.ssh/id_rsa
		  
3.  Save and close the file.
4.  Restart the GitBash terminal.

## Step 4. Update your .bashrc profile file

It is a good idea to configure your **GitBash** shell to automatically start the agent when you launch **GitBash**. The `.bashrc` file is the shell initialization file. It contains commands that run each time your **GitBash** shell starts. You can add commands to the `.bashrc` file that start the agent when you start **GitBash**. The folks at GitHub have developed a nice script for this (their script was developed from [a post by Joseph M. Reagle Jr. from MIT](http://www.cygwin.com/ml/cygwin/2001-06/msg00537.html) on the cygwin list). To start the agent automatically, do the following.

1.  Start **GitBash**.
2.  Edit your `~/.bashrc` file.
    If you don't have a `.bashrc` file you can create the file using your favorite text editor. Keep in mind the file must be in your `~` (home) directory and must be named exactly _._ `bashrc` .
3.  Add the following lines to the file:
    
    Chrome and Opera introduce ASCII xa0 (non-breaking space characters) on paste that can appear in your destination file. If you copy and paste the lines below, copy from another browser to avoid this problem.
    
		SSH_ENV=$HOME/.ssh/environment
		  
		# start the ssh-agent
		function start_agent {
		    echo "Initializing new SSH agent..."
		    # spawn ssh-agent
		    /usr/bin/ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
		    echo succeeded
		    chmod 600 "${SSH_ENV}"
		    . "${SSH_ENV}" > /dev/null
		    /usr/bin/ssh-add
		}
		  
		if [ -f "${SSH_ENV}" ]; then
		     . "${SSH_ENV}" > /dev/null
			 ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
			    start_agent;
			}
		else
		    start_agent;
		fi
    
4.  Save and close the file.
5.  Close **GitBash**, then reopen **GitBash**.
6.  The system prompts you for your passphrase:

		Welcome to Git (version 1.7.8-preview20111206)
		Run 'git help git' to display the help index.
		Run 'git help <command>' to display help for specific commands.
		Enter passphrase for /c/Documents and Settings/manthony/.ssh/id_rsa:
    
7.  Enter your passphrase.  
    After accepting your passphrase, the system displays the command shell prompt. 
8.  Verify that the script identity added your identity successfully by querying the SSH agent:
    
		$ ssh-add -l
		2048 0f:37:21:af:1b:31:d5:cd:65:58:b2:68:4a:ba:a2:46 /Users/manthony/.ssh/id_rsa (RSA)
    
After you install your public key to Bitbucket, having this script should prevent you from having to enter a password each time you push or pull a repository from Bitbucket.

## Step 5. Install the public key on your Bitbucket account

1.  From Bitbucket, choose **_avatar_ > Bitbucket settings** from the application menu.   
    The system displays the **Account settings** page.
2.  Click **SSH keys**.  
    The** SSH keys** page displays. It shows a list of any existing keys. Then, below that, a dialog for labeling and entering a new key.
3.  In your terminal window, `cat` the contents of the public key file by entering the following:
    
    	$ cat ~/.ssh/id_rsa.pub
    
4.  Select and copy the key output in the clipboard.  
    If you have problems with copy and paste, you can open the file directly with Notepad. Select the contents of the file (just avoid selecting the end-of-file character).
5.  Back in your browser, enter a **Label** for your new key, for example, `Default public key`.
6.  Paste the copied public key into the SSH **Key** field.  
    ![](https://confluence.atlassian.com/bitbucket/files/304578655/755335794/1/1434638786771/add_ssh_key.png)
7.  Press **Add key**.  
    The system adds the key to your account. Bitbucket sends you an email to confirm addition of the key.
8.  Return to the terminal window and verify your configuration by entering the following command.
    
    	$ ssh -T git@bitbucket.org
    
    The command message tells you which Bitbucket account can log in with that key.
    
    
		conq: logged in as tutorials.
		You can use git or hg to connect to Bitbucket. Shell access is disabled.

    
9.  Verify that the command returns your account name.
    
      If you got a permission denied (publickey) message.
    
    The command tests your connection to Bitbucket as a Git user. It first sees if your SSH Agent has an identity loaded. The command then checks if that private key matches a public key for an existing Bitbucket account. You might have either problem.
    To make sure your identity is loaded, enter the following command:
    
    	$ ssh-add -l
    
    If the identity isn't loaded, check your work in Step 5 above. If it is loaded, try reinstalling your public key on your Bitbucket account. 
    Still having troubles? You can try our [Troubleshoot SSH Issues](/bitbucket/troubleshoot-ssh-issues-271943403.html) page or email [support@bitbucket.org](mailto:support@bitbucket.org).
    
    
## Step 6. Configure your repository to use the SSH protocol

The URL you use for a repo depends on which protocol you are using, HTTPS or SSH. The **Clone** button of your repository has a quick way for you to see these URLS.

![](https://confluence.atlassian.com/bitbucket/files/304578655/746520869/2/1434639735558/git+clone.png)

Experiment for a moment, clicking back and forth between the **SSH** and the **HTTPS** protocol links to see how the URLs differ. The table below shows the format based on protocol.

<table>
<tr>
<th>SSH URL format</th>
<td>
 git@bitbucket.org :< accountname>/<reponame>.git<br>

or<br>

ssh://git@bitbucket.org /< accountname>/<reponame>.git 
</td>
</tr>
<tr>
<th>HTTPS URL format</th>
<td>https://@[bitbucket.org/_/](http://bitbucket.org/).git</td>
</tr>
</table>

To make the change, go to a terminal on your local system and navigate to your repository locally. Then, do the following:

1.  View your current repo configuration.  
    You should see something similar to the following:
    
	    $ cd ~/  
	    $ cat .git/config  
	    [core]  
	        repositoryformatversion=0  
	        filemode=true  
	        bare=false  
	        logallrefupdates=true  
	        ignorecase=true  
	        precomposeunicode=true  
	    [remote "origin"]  
	        fetch=+refs/heads/*:refs/remotes/origin/*  
	        url=https://emmap1@bitbucket.org/emmap1/bitbucketspacestation.git  
	    [branch "master"]  
	        remote=origin  
	        merge=refs/heads/master
    
    As you can see, the `url` is using the HTTPS protocol. There are a number of ways to change this value, the easiest way is just to edit the repo's configuration file.
2.  Open the `~//.git/config` file with your favorite editor.
3.  Change the `url` value to use the SSH format for that repo.  
	When you are done you should see something similar to the following:
	 [remote "origin"]
	    fetch = +refs/heads/*:refs/remotes/origin/*
	    url = git@bitbucket.org:emmap1/bitbucketspacestation.git
    
    
## Step 7. Make a change under the new protocol
1.  Edit the `README` file in your repository.
2.  Add a new line to the file, for example:
    
		Welcome to My First Repo
		-------------------------------
		This repo is a practice repo I am using to learn bitbucket.
		You can access this repo with SSH or with HTTPS.
    
    
3.  Save and close the file.
4.  Add and then commit your change to your local repo.
    
	    $ git add README  
	    $ git commit -m "making a change under the SSH protocol"
    
5.  Push your changes.  
    The system warns you that it is adding the Bitbucket host to the list of known hosts.
    
	    $ git push  
	    Counting objects: 5, done.  
	    Delta compression using up to 2 threads.  
	    Compressing objects: 100% (2/2), done.  
	    Writing objects: 100% (3/3), 287 bytes, done.  
	    Total 3 (delta 1), reused 0 (delta 0)  
	    remote: bb/acl: newuserme is allowed. accepted payload.  
	    To git@bitbucket.org:newuserme/bb101repo.git  
	      056c29c..205e9a8 master -> master
    
6.  Open the repo **Overview** in Bitbucket to view your commit.
