---
title: GPG Sign and Verify git commits
date: 2022-03-23 11:58:47 +00:00
modified: 2022-03-23 11:58:47 +00:00
tags: [tech]
description:
image: "/assets/images/GNUPG.png"
---
<figure>
<img src="/assets/images/GNUPG.png" alt="">
</figure>

# Contents
1. [What's the need for GPG verification?](#section-1)
2. [Installing GPG](#section-2)
3. [Create a GPG key](#section-3)
4. [Associate GPG key for git commits](#section-4)
5. [Test Verify commit](#section-5)
6. [Generate public GPG key](#section-6)
7. [Add GPG public key to GitHub profile](#section-7)
8. [GitHub Mergeability - allow only signed commits](#section-8)
9. [Manually verify old commits](#section-9)

---

# <a name="section-1"></a>1. What's the need for GPG verification?

[GPG (GNU Privacy Guard)](https://gnupg.org) verification of git commits gives confident that the changes are coming from "trusted" sources. For example, on GitHub, the "Verified" badge indicates that the user added another level of trust using GPG to verify `git` commits.

<figure>
<img src="/assets/images/verifiedgitcommit.png" alt="">
<figcaption>An example of "Verified" commit on GitHub</figcaption>
</figure>

While initially configuring `git` on your machine, you would set your name and email to git configuration values with below commands. 

```
git config --global user.name "Chandra Sekhar"
git config --global user.email "chandra@email.com"
```

If you see, on its own, Git does not have a way of authenticating that you are actually who you say you are. I could give whatever the name I wanted to. Some git frontends like GitHub display a small question mark icon next to the user's email if the user email is different than an email associated with the GitHub user's account.

For teams and organizations, this is potentially a security issue. Someone might be posing as a team member. It is strongly advised that every member of your team who has access to git repository shall have GPG signature created on their machiens and verify commits. Also, merging code into master/main/dev branches should not be enabled unless the commits are verified. See section [GitHub Mergeability - allow only signed commits](#section-8).

<a href="#top">↑ Top</a>

---

# <a name="section-2"></a>2. Installing GPG

The below commands work for UNIX-like platforms - macOS/Mac OS X and Linux.

```
brew install gpg
```

<a href="#top">↑ Top</a>

---

# <a name="section-3"></a>3. Create a GPG key


Run the below command. You will get an interactive program running. You will be asked to enter your name, email ID of the git account. Provide details as necessary.

```
gpg --gen-key
```

This will create a `.gnupg` directory in your home directory. 

Running the below command shall display key(s) that you crated.

```
gpg --list-secret-keys --keyid-format LONG
```

Output should look like this. The `MY_KEY` in the below output is the key you are going to use for signing commits. Copy it to clipboard (⌘+C or Ctrl+C).

```
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0x, 0y, 0g, 1u
gpg: next trustdb check due at YYYY-MM-DD
/Users/<<USER>>/.gnupg/pubring.kbx
-----------------------------
sec   ed25519/MY_KEY YYYY-MM-DD [SC] [expires: YYYY-MM-DD]
      <<LONG_KEY>
uid                 [ultimate] FIRSTNAME LASTNAME <chandra@email.com>
ssb   cv25519/KEY YYYY-MM-DD [E] [expires: YYYY-MM-DD]
```

<a href="#top">↑ Top</a>

---

# <a name="section-4"></a>4. Associate GPG key for git commits
Running the below commands (Set-1) associate GPG key globally to your git config. If you do not want to assiciate this key globally on git configuration just `cd` into the desired directory and remove `--global` and run the comands (Set-2)

#### Set - 1

```
git config --global commit.gpgsign true
git config --global user.signingKey MY_KEY
```

#### Set - 2
```
cd directory
git config commit.gpgsign true
git config user.signingKey MY_KEY
```

<a href="#top">↑ Top</a>

---

# <a name="section-5"></a>5. Test Verify commit

Verify if the signing works by running the below command.

```
git verify-commit HEAD
```

If `gpg` key is created correctly, you would see similar output.

```
gpg: Signature made Sun 20 Mar 02:08:21 2022 EDT
gpg:                using RSA key XYZXYZXYZXYZ
gpg: Good signature from "Chandra Sekhar <chandra@email.com>" [ultimate]
```

<a href="#top">↑ Top</a>

---

# <a name="section-6"></a>6. Generate public GPG key

Run the below command to create a public key from the GPG private key. Use the same email ID that you used to create the key.

```
gpg --armor --export chandra@email.com > ~/.gnupg/publicGPGKey.pub
```

Now, run `ls -tral ~/.gnupg/`. You should see a new file `publicGPGKey.pub` created. Just `cat ~/.gnupg/publicGPGKey.pub` and see the output. It should look like any other public key with header `-----BEGIN PGP PUBLIC KEY BLOCK-----` and trailer `-----END PGP PUBLIC KEY BLOCK-----`.

Copy the contents of the public key by running the below command.

```
cat ~/.gnupg/publicGPGKey.pub | pbcopy
```

<a href="#top">↑ Top</a>

---

# <a name="section-7"></a>7. Add GPG public key to GitHub profile

As shown in the below images, 

- Go to your GitHub profile Settings. 
- On the left side menu, in <b>Access</b> section, click on <b>SSH and GPG keys</b> 
- Click on <b>New GPG key</b> button
- Give a meaningful title. 
- Paste the copied public key.
- Click on <b>App GPG key</b> button.

<figure>
<img src="/assets/images/gitset.png" alt="" >
</figure>

<figure>
<img src="/assets/images/gitset2.png" alt="" >
</figure>

<a href="#top">↑ Top</a>

---

# <a name="section-8"></a>8. GitHub Mergeability - allow only signed commits

Git branches can be setup with protection rules. These rules enable repository administrators to enforce security policies. These policies help to protect the git branches from unexptected code commits or deletions by any unauthorized persons or user groups.

Administrators can setup branch protection rules such as

- Set minimum required approvals on Pull Requests
- Allow only signed commits
- Mandatory status checks before merging
- Require Pull Requests review conversations resolution etc.


## 8.1 How to allow only signed commits

As shown the image below, 

1. Click on repository <b>Settings</b> tab
2. Click on <b>Branches</b> section on the left menu
3. In <b>Branch protection rule</b>, enable <b>Require signed commits</b>

<figure>
<img src="/assets/images/mergerule.png" alt="" >
</figure>

<a href="#top">↑ Top</a>

---

# <a name="section-9"></a>9. Manually verify old commits
Well, now that you enabled a protection rule that allows only signed commits, there might be a chance that existing Pull Requests do not have verified commits and hence they can't be merged. So, those old commits might need verification or to be signed. 

Here is how we do it. First we will run `git log` to see what commits were made. Get commit ID for those commit points we need to verify/sign. And, sign by using the commit ID. And then `push`. 

## 9.1 Get the commit history 

```
git log
```

The commit history is shown similar to below output. Get any commit ID. For example: `00A6XfXYZXYZXYZXY`. 

```
commit 00A6XfXYZXYZXYZXY (HEAD -> feature-signin, origin/feature-signin)
Author: Chandra <chandra@email.com>
Date:   Sun Feb 20 16:32:30 2022 -0800

    Update SignInDelegate.swift for OAuth

commit 2XXXXXXe00694d167
Author: Chandra <chandra@email.com>
Date:   Sun Feb 20 16:25:00 2022 -0800

    Delegates are updated

commit 3XXXXXXXXe2
Author: Chandra <chandra@email.com>
Date:   Sun Feb 20 15:57:33 2022 -0800

    Fixing a crash on network call
```

## 9.2 Sign that particular commit

Note: Running the below command would rebase and will appear as a new commit. But, the commit message would not change. The `-S` command is for signing.

```
git rebase -i 00A6XfXYZXYZXYZXY --exec 'git commit --amend --no-edit -S'
```

## 9.3 Push the commit 

```
git push -f
```

Go check on GitHub, your existing PR should have been updated with verified commit.

---

That's it! 

Hope, this helps you and your team!
