# How to persist a project-name in docker-compose

This repo contains some use-cases for docker-compose issue [#745](https://github.com/docker/compose/issues/745).

Each folder means to illustrate different possibility to define a project name in a docker-compose project.

Folders share the same docker compose project that contains 2 docker-compose files:
* docker-compose.yml defines container1
* docker-compose2.yml defines container2 and customize container1

`docker-compose ... down` is used for testing the project name. It results in removing the network; from the network's name you can understand the project names.

## without-project-name

Docker-compose files do not define a project name. User can specify project name by -p option:

```bash
docker-compose -p MYPROJ -f without-project-name/docker-compose.yml -f without-project-name/docker-compose2.yml down
# => project name: myproj --- GOOD
cd without-project-name && docker-compose -p MYPROJ -f docker-compose.yml -f docker-compose2.yml down
# => project name: myproj --- GOOD
```

Conclusion: it works consistent but we can't persist project name in cvs.

## compose-project-name

Use COMPOSE_PROJECT_NAME env variable via .env file.

```bash
docker-compose -f compose-project-name/docker-compose.yml -f compose-project-name/docker-compose2.yml down
# => project name: composeprojectname --- FAIL
cd compose-project-name && docker-compose -f docker-compose.yml -f docker-compose2.yml down
# => project name: myproj --- GOOD
```

Conclusion: we can persist project name but behaviour is inconsistent, it only works from proj folder.

## [PR_5378](https://github.com/docker/compose/pull/5378)

Use `x-project-name` feature in docker-compose file plus env variable. (is it really different from compose-project-name?)

```bash
COMPOSE_X_PROJECT_NAME=1 docker-compose -f PR_5378/docker-compose.yml -f PR_5378/docker-compose2.yml down
# => project name: myproj --- GOOD
docker-compose -f PR_5378/docker-compose.yml -f PR_5378/docker-compose2.yml down
# => project name: xprojectname --- FAIL
cd PR_5378 && docker-compose -f docker-compose.yml -f docker-compose2.yml down
# => project name: myproj --- GOOD
```

Conclusion: we can persist project name but behaviour is inconsistent, it only works from proj folder.

## [PR_5369](https://github.com/docker/compose/pull/5369)

Use `project_name` feature in docker-compose file.

```bash
docker-compose -f PR_5369/docker-compose.yml -f PR_5369/docker-compose2.yml down
# => project name: myproj --- GOOD
cd PR_5369 && docker-compose -f docker-compose.yml -f docker-compose2.yml down
# => project name: myproj --- GOOD
```

Conclusion: we can persist project name so that docker-compose would respect it indepentent of the working directory.
