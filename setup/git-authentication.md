# git-personal-auth-token.md
## Useful Links
[Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
[Using Personal Access Tokens with GIT and Github](https://www.edgoad.com/2021/02/using-personal-access-tokens-with-git-and-github.html)

##Solution

* generate a personal access token on github and copy it
* in terminal - attempt to clone a repo
** when prompted for username and password give (username, personal access token)

> Lastly, to ensure the local coputer remembers the token, we can enable caching of the credentials. This configures the computer to remember the complex token so that we do not have too.

```
git config --global credential.helper cache
```

First iteration - Generated a 30-day personal access token
