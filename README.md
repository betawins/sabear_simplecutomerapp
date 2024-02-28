## How to execute this pipeline

1) Sonarscanner
2) Sonarqube server
3) jenkins server
4) nexus server
5) change the credetilas of nexus server as per our requirement
6) create a new repo in nexus with version policy as "snapshot"
7) modiy the pipeline as per our sonarscanner name and sonarqube server name


Use the below code in sonarqube analysis report.
```sh 
sonar.projectKey=Ncodeit
sonar.projectName=Ncodeit
sonar.projectVersion=2.0
sonar.sources=/var/lib/jenkins/workspace/$JOB_NAME/src/
sonar.binaries=target/classes/com/visualpathit/account/controller/
sonar.junit.reportsPath=target/surefire-reports
sonar.jacoco.reportPath=target/jacoco.exec
sonar.java.binaries=src/com/room/sample
```
