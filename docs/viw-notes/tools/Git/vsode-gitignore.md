1.项目路径下创建 `.ignore`

2.添加编写

```json

  
  "search.exclude": {        
    "**/node_modules": true,
    "**/bower_components": true,
    "build/": true,
    "temp/": true,
    "library/": true,
    "**/*.anim": true
},
"files.exclude": {
    "**/.git": true,
    "**/.svn": true,
    "**/.hg": true,
    "**/CVS": true,
    "**/.DS_Store": true,
    "**/*.meta": true,
    "**/.history": true,
    "**/.idea": true,
    "**/idea": true,
    "**/logs": true,
    "**/target": true,
    "**/pid": true,
}
```

3.这个只针对本项目下有效，或者把这个添加到项目的  `package.json` 中就行



**IDEA:**



```
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties
dependency-reduced-pom.xml
buildNumber.properties
.mvn/timing.properties
.mvn/wrapper/maven-wrapper.jar
mvnw
mvnw.cmd
HELP.md
!.mvn/wrapper/maven-wrapper.jar
!**/src/main/**
!**/src/test/**
LICENSE
README.en.md
### STS ###
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache

### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr

### NetBeans ###
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/
build/

### VS Code ###
.vscode/
/**/.mvn/
.gitignore
!/viwmall/.gitignore

```

