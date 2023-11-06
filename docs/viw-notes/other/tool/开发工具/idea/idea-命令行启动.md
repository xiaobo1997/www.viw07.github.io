


# command-line-start-idea 

`https://www.jetbrains.com/help/idea/working-with-the-ide-features-from-command-line.html`
`https://www.jetbrains.com/help/idea/opening-files-from-command-line.html`

> 命令行启动一个project

## mac 

在 `/usr/local/bin` 创建一个 idea  使用 `vi idea`

```shell

open -na "IntelliJ IDEA.app" --args "$@"

```

`chmod 777 idea`


打开一个porject
`idea  [project_name]`