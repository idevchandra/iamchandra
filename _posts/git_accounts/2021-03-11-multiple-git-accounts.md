---

title: Setup Multiple Git Accounts on macOS
date: 2021-03-11 9:00:00 +00:00
modified: 021-03-11 9:00:00 +00:00
tags: [tech]
description: Setup Multiple Git Accounts on macOS
image: "/assets/images/multgits.png"
---

<figure>
<img src="/assets/images/multgits.png" alt="">
</figure>

Cloning and pushing code would often fail when we have multiple `git` accounts on a single machine. A little setup and organization would help here.

Follow the below steps to setup multiple git accounts on macOS.

1. [Create SSH keys for the accounts](#section-1)
2. [Add the SSH keys to the ssh agent](#section-2)
3. [Add the respective public SSH keys to the Git websites](#section-3)
4. [Create config file on your Mac](#section-4)
5. [Make an Account Git Default](#section-5)
6. [Use Git with Non-default or Secondary Accounts](#section-6)
7. [Push from already cloned repositors](#section-7)

# <a name="section-1"></a>1. Create SSH keys for the accounts 
---------------------------------------

In the below commands, `-C` is for comment, `-f` for output file name.

Best practice for `-f` option is to use `github-{GIT_ACC_NAME}` to identify keys later, to avoid confusion.

```
cd ~/.ssh
$ ssh-keygen -t rsa -C "emailid_one_github@email.com" -f "github-account1"
$ ssh-keygen -t rsa -C "emailid_two_github@email.com" -f "github-account2"
$ ssh-keygen -t rsa -C "emailid_three_bitbucket@email.com" -f "bitbucket-account-2"
```

# <a name="section-2"></a>2. Add the SSH keys to the `ssh` agent
---------------------------------------
Add your keys to ssh agent by alternative set of commands mentioned below. When you use Alternate-1 that uses option `-K`, you might get the below warning. Still, the key gets added. 

```
WARNING: The -K and -A flags are deprecated and have been replaced
         by the --apple-use-keychain and --apple-load-keychain
         flags, respectively.  To suppress this warning, set the
         environment variable APPLE_SSH_ADD_BEHAVIOR as described in
         the ssh-add(1) manual page.
```         

Go ahead, run these commands. Shall you see any such warning, replace `-K` with `--apple-use-keychain`.

#### 2.1 Alternate 1:

```
$ ssh-add -K ~/.ssh/github-account1
$ ssh-add -K ~/.ssh/github-account2
$ ssh-add -K ~/.ssh/bitbucket-account-2
```

#### 2.2 Alternate 2:
```
$ ssh-add --apple-use-keychain ~/.ssh/github-account1
$ ssh-add --apple-use-keychain ~/.ssh/github-account2
$ ssh-add --apple-use-keychain ~/.ssh/bitbucket-account-2
```

# <a name="section-3"></a>3. Add the respective public SSH keys to the Git websites
---------------------------------------

Copy your SSH keys into clipboard with the below command.

```
// Run this command for respective keys
$ pbcopy < ~/.ssh/github-{ACC}.pub
```

Follow the below links to add SSH keys on the website.

1. [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
2. [Bitbucket](https://support.atlassian.com/bitbucket-cloud/docs/add-access-keys/) 
3. [Gitlab](https://docs.gitlab.com/ee/ssh/)

# <a name="section-4"></a>4. Create config file on your Mac
---------------------------------------

Create a file with name `config`, and edit.

```
vi ~/.ssh/config

-- OR --

open -e ~/.ssh/config
```

Now, add the below content to your `config` file.

```
#Github account1
Host github.com-{GIT_ACCOUNT_NAME_1}
    HostName github.com
    User git
    IdentityFile ~/.ssh/github-account1

#Github account2
Host github.com-{GIT_ACCOUNT_NAME_2}
    HostName github.com
    User git
    IdentityFile ~/.ssh/github-account2

#Bitbucket account2
Host bitbucket.org-{BitB_ACCOUNT_NAME}
    HostName bitbucket.org
    User git
    IdentityFile ~/.ssh/bitbucket-account-2
```

# <a name="section-5"></a>5.Make an Account Git Default
---------------------------------------

`git config --list` lists yours default global git configuration values. If you want to change, you can change with the below commands.

```
$ git config --global user.name "Chandra"
$ git config --global user.email "emailid@mail.net"
```

# <a name="section-6"></a>6. Use Git with Non-default or Secondary Accounts
---------------------------------------

Referring the `config` file, you can use this command to use a particular git account.

`$ git clone git@github.com-{GIT_ACCOUNT_NAME_1}:{-{GIT_ACCOUNT_NAME_1}}/{REPO_NAME}.git`

An example would be:

`$ git clone git@github.com-chandra:developerchandra/sample-project.git`

Now, you might not want to use global configurations (email and name) for every repo that you checkout. It would be better to change `git config` for the repositories you check out. So, you can run the following commands once you check out a repo.

```
$ git config user.email "{EMAIL FOR ACCOUNT ONE}"
$ git config user.name "{YOUR NAME FOR ACCOUNT ONE}"

$ git config user.email "{EMAIL FOR ACCOUNT TWO}"
$ git config user.name "{YOUR NAME FOR ACCOUNT TWO}"
```

# <a name="section-7"></a>7. Push from already cloned repositors
---------------------------------------

Referring the `config` file, you can use this command to use a particular git account.
