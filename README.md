Here are some example use-cases for docker-compose issue #745.

Each folder means to illustrate different possibility to define a project name in a docker-compose project.

Folders share the same docker compose project that contains 2 docker-compose files:
* docker-compose.yml defines container1
* docker-compose2.yml defines container2 and customize container1

`docker-compose ... down` is used for testing the project name. It results in removing the network; from the network's name you can understand the project names.

# without-project-name

Docker-compose files do not define a project name. User can specify project name by -p option:

```bash
docker-compose -p MYPROJ -f without-project-name/docker-compose.yml -f without-project-name/docker-compose2.yml down
# => project name: myproj
cd without-project-name && docker-compose -p MYPROJ -f docker-compose.yml -f docker-compose2.yml down
# => project name: myproj
```

Conclusion: it works consistent but we can't persist project name in cvs.

# compose-project-name

Use COMPOSE_PROJECT_NAME env variable via .env file.

```bash
docker-compose -f compose-project-name/docker-compose.yml -f compose-project-name/docker-compose2.yml down
# => project name: composeprojectname
cd compose-project-name && docker-compose -f docker-compose.yml -f docker-compose2.yml down
# => project name: myproj
```

Conclusion: we can persist project name but behaviour is inconsistent, it only works from proj folder.

# x-project-name

Note: it only works with docker-compose PR #5378.

Use `x-project-name` feature in docker-compose file.

```bash
COMPOSE_X_PROJECT_NAME=1 docker-compose -f x-project-name/docker-compose.yml down
# => project name: myproj
docker-compose -f x-project-name/docker-compose.yml down
# => project name: xprojectname
cd x-project-name && docker-compose -f docker-compose.yml down
# => project name: myproj
```

Conclusion: we can persist project name but behaviour is inconsistent, it only works from proj folder.
