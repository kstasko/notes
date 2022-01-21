# git-personal-auth-token
## Useful Links
[Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) \n
[Using Personal Access Tokens with GIT and Github](https://www.edgoad.com/2021/02/using-personal-access-tokens-with-git-and-github.html)

##Solution

* generate a personal access token on github and copy it
* in terminal - attempt to clone a repo
** when prompted for username and password give (username, personal access token)

> Lastly, to ensure the local coputer remembers the token, we can enable caching of the credentials. This configures the computer to remember the complex token so that we do not have too.

###### Cache Personal Access Token 
```
git config --global credential.helper cache
```

First iteration - Generated a 30-day personal access token

# Authenticate Github using SSH Key
## Useful Links
[Using Git with SSH Keys](https://linuxkamarada.com/en/2019/07/14/using-git-with-ssh-keys/#.YenwuS-B19c)

###### Undo cache-ing of Personal Access token
```
git config --global --unset credential.helper
```

###### Generate, Add SSH-Key & Authenticate with Github 
```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
cat id_rsa.pub | pbcopy
ssh -T git@github.com
```

