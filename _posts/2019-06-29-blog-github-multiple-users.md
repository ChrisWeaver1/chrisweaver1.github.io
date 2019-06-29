---
layout: default
permalink: /b/github-multiple-users
customcss: /assets/css/markdown.css
title: Managing Multiple Github Users In Local Repositories
description: Short rundown of how to go about managing multiple Github users over different local repositories. I had an issue where I wanted to have 2 Github accounts, one that would be a work centric account, the other would be more for personal projects and contributions. So how does that work with local repositories?
image: /assets/images/icons/github.svg
catagories: ['blog']
tags: ['git', 'github', 'ssh']
---

## Managing Multiple Github Users In Local Repositories

Short rundown of how to go about managing multiple Github users over different local repositories. I had an issue where I wanted to have 2 Github accounts, one that would be a work centric account, the other would be more for personal projects and contributions. So how does that work with local repositories? Well its actually quite straight forward, but the answer isn't in plain sight, at least not that I found, so I figured I would write a quick rundown of how to setup and manage it.

### SSH Keys

Hopefully we are all somewhat familiar with Githubs support for using SSH keys for user authentication, if not, you can read about it [here](https://help.github.com/en/articles/connecting-to-github-with-ssh). We will start off creating 2 SSH keys - one for each account. If you already have one setup, you can just generate one new key here, just make sure to save it to  different file.

```
ssh-keygen -t rsa -b 4096 -C "main_account_email@foo.bar" -f ~/.ssh/id_rsa_main
ssh-keygen -t rsa -b 4096 -C "secondary_account_email@foo.bar" -f ~/.ssh/id_rsa_secondary
```

As you can see, we are specifying the output file in the command, it's important that they are named differently so we have 2 seperate keyfiles for the 2 accounts. Switch out the email addresses for ones that are attached to your Github accounts, this isn't overly important but helps you remember which key file is for which account, which could come in handy later down the line.

```
touch ~/.ssh/config
chmod 600 ~/.ssh/config
nano ~/.ssh/config
```

Now we are creating an ssh config file, this will decide which key should be used when. The `chmod 600 ~/.ssh/config` will set this permissions up, so the file is only readable & writable by the owner. For me, this was required as git would reject the file saying it had bad permissions otherwise, this might be different depending on your setup but these are likely the correct permissions for the file so I thought I'd include it anyway. 

`~/.ssh/config`
```
Host github.com-secondary
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_rsa_secondary

Host github.com
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_rsa_main
```

The config file should look like the one above. Main things to note here are the `Host` variable on line 1, we can see I added `-secondary` to the end. This will be what decides which keyfile to use for the request. I leave this alone for the main key as then it doesn't require any additional repository setup.

You can then go ahead and associate the generated keys to their corresponding Github accounts, you can do this by going to settings > [SSH and GPG keys](https://github.com/settings/keys) and adding the keys there.

### Repository

For your main account you shouldn't have to do any additional setup, it will still use the global (or repository) configuration as it would normally. For the secondary user we need to change a few things, namely the remote url. You can do this by either cloning the repository down again or setting the remote url of an existing repository again, examples below. It needs to be set to the same as the `HOST` variable in `~/.ssh/config` for the secondary user. 

```
git clone git@github.com-secondary:foo/bar.git
git remote set-url origin git@github.com-secondary:foo/bar.git
```

Notice the host url is changed to `github.com-secondary`. This is what will determine which SSH key is used when interacting with the remote repository. You will then want to set the user details for the repository, so it isn't using your global user and instead uses the user associated with the secondary Github account.

```
git config user.email <email>
git config user.name <username>
```

This will setup the user for the current repository, meaning the commits shown in Github should associate to the secondary account.

And that's about it, just remember to change that url and the user configuration in the secondary users repositories.