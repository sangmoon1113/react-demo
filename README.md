# react-demo
### 1. Spring boot Server Port
* `/src/main/resources/application.properties`
    ```javascript
    server.port = 8080
    ```

### 2. `/src/main/webapp` 폴더 생성
### 3. React Project 생성
* `/src/main/webapp`
    ```shell
    npx create-react-app reactjs --skip-git
    ```
### 4. React 포트 변경
* `/src/main/webapp/reactjs/package.json`
    ```javascript
    "proxy": "http://localhost:8080",
    ```
### 5. Gradle 빌드 설정
* `/build.gradle`
    ```javascript
    // .......
    
    def reactDir = "$projectDir/src/main/webapp/reactjs";
    
    sourceSets{
        main{
            resources{
                srcDirs = ["$projectDir/src/main/resources"]
            }
        }
    }
    
    processResources{
        dependsOn "copyReactBuildFiles"
    }
    
    task installReact(type:Exec){
        workingDir "$reactDir"
        inputs.dir "$reactDir"
        group = BasePlugin.BUILD_GROUP
    
        if(System.getProperty('os.name').toLowerCase(Locale.ROOT).contains('windows')){
            commandLine "npm.cmd", "audit", "fix"
            commandLine 'npm.cmd', 'install'
        }else{
            commandLine "npm", "audit", "fix"
            commandLine 'npm', 'install'
        }
    }
    
    task buildReact(type:Exec){
        dependsOn "installReact"
        workingDir "$reactDir"
        inputs.dir "$reactDir"
        group = BasePlugin.BUILD_GROUP
    
        if(System.getProperty('os.name').toLowerCase(Locale.ROOT).contains('windows')){
            commandLine "npm.cmd", "run-script", "build"
        }else{
            commandLine "npm", "run-script", "build"
        }
    }
    
    task copyReactBuildFiles(type:Copy){
        dependsOn "buildReact"
        from "$reactDir/build"
        into "$projectDir/src/main/resources/static"
    }
    ```
### Build
```shell
gradlew build
```
### Build Output
* `/build/libs/{projectname}-0.0.1-SNAPSHOT.jar`

### Run
```shell
java -jar react-demo-0.0.1-SNAPSHOT.jar
```