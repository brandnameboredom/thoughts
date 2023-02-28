---
title: Resolving localhost
description: Resolving localhost
date: 2018-11-15
tags:
  - Docker
  - Localhost
  - Compose
  - Alternative
---

I ran into an issue the other day trying to test out a Jenkins slave docker image I had built to test some dotnetcore code.  I had an instance of [Sonarqube up and running inside of a docker container].  So, I built my slave container and jumped into the bash of the executing slave container.  Once inside, I wanted to run the following commands:

```
dotnet /sonarqube-scanner/SonarScanner.MSBuild.dll begin /k:"myproject" /d:sonar.host.url=http://localhost:9000 /d:sonar.login="my_sonarqube_provided_login"

dotnet build myproject.sln

dotnet /sonarqube-scanner/SonarScanner.MSBuild.dll end
```
However, I realized that the command would fail because the "localhost" within the sonar.host.url parameter would resolve to the scanner container and not my host machine.  So, how do we address that?  Well, the correct answer is probably create a docker-compose pod network and change localhost to the appropriate name within the compose file.  Now that we have that disclaimer out of the way, we can talk about what I did.  Turns out, docker has this property you can use to get access to the localhost on the host machine running the container.  In my case, this means access to the Sonarqube scanner container running at localhost:9000.  So, I updated my commands to look like:

```
dotnet /sonarqube-scanner/SonarScanner.MSBuild.dll begin /k:"myproject" /d:sonar.host.url=http://docker.for.mac.localhost:9000 /d:sonar.login="my_sonarqube_provided_login"

dotnet build myproject.sln

dotnet /sonarqube-scanner/SonarScanner.MSBuild.dll end
```

Executed them and everything resolved and executed without error.  Sweet!  On to the next problem...
        
FYI there is a windows version of the docker.for.mac.localhost property.  I bet you can guess what it is but in case you are feeling lazy today it is docker.for.win.localhost

[Sonarqube up and running inside of a docker container]: https://hub.docker.com/_/sonarqube/
