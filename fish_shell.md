 **fish shell de docker-env alias tanımlama:**

```sh
$ function docker-env
               eval (docker-machine env $argv[1])
           end
$ funcsave docker-env
```

The last command creates a file for function in functions folder:
`~/.config/fish/functions/docker-env.fish`
