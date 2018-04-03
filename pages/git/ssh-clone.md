# Clone a repo using a SSH private key

To clone a repo using a previously registred SSH key

```
ssh-agent bash -c 'ssh-add /somewhere/yourkey; git clone git@github.com:user/project.git'
```

See this (StackOverflow quesetion)[https://stackoverflow.com/questions/4565700/specify-private-ssh-key-to-use-when-executing-shell-command]


