# Run Sonarqube

docker run -d --name sonarqube -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true -p 9000:9000 sonarqube:latest

# Check sonarqube status

docker logs sonarqube -f

Expected output :

2023.01.27 08:28:09 INFO ce[][o.s.c.c.computeenginecontainerimpl] Running Community edition
2023.01.27 08:28:09 INFO ce[][o.s.ce.app.ceserver] Compute Engine is started
2023.01.27 08:28:09 INFO app[][o.s.a.schedulerimpl] Process[ce] is up
2023.01.27 08:28:09 INFO app[][o.s.a.schedulerimpl] SonarQube is operational

# The Project

Each time the vm restarted need to start the docker container
