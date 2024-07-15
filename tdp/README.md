# Jupyterlab Build

For building Jupyterlab, you need to recover the build container here :
https://github.com/TOSIT-IO/TDP/tree/main/build-env-python

## Start the container

Next, start the container with specific values regarding your environment but with the official build image above.

For example :

```sh
DCT_GIT="$HOME/git"
DCT_PIP="$HOME/.cache/pip"
DCT_BUILD="$HOME/build"
```

The git directory matchs to the local directory that contains the git repositories.
The build directory matchs to the local directory where the archive is created.

And the command :

```sh
docker run --rm=true -it --env "USER_UID=$(id -u)" --env "USER_GID=$(id -g)" --volume "$DCT_PIP:/home/builder/.cache/pip/" --volume "$DCT_GIT:/home/builder/git/" --volume "$DCT_BUILD:/home/builder/build" --workdir /home/builder/ tdp_builder_python
```

## Sourcing the NodeJS environment

```sh
source /usr/local/nvm/nvm.sh
source /usr/local/nvm/bash_completion
```

## Configuration

If you haven't cloned the repository before, do it, otherwise, it should be mounted with a volume. Verify that you're using the default branch of the repository.

In addition to the git repository, create the requirement file to generate wheel files. The requirement file must contain :

```sh
echo "pip>=21.3.1
jupyterhub==2.3.1
jupyter-hdfscm==0.2.0
jupyterhub_yarnspawner==0.4.0" > $HOME/build/requirements.txt
```

## Generate wheels

```sh
python3.6 -m pip wheel --wheel-dir $HOME/build/dependencies/ --requirement $HOME/build/requirements.txt $HOME/git/jupyterlab/
```

## Modify requirement files for installation

Add the next line :

```sh
echo "jupyterlab==3.2.9" >> $HOME/build/requirements.txt
```

## Create archive

Create the archive with wheel files and the requirement file, then create the checksum

```sh
cd $HOME
tar cf jupyterlab-3.2.9-0.0.tar.gz build/
sha256sum jupyterlab-3.2.9-0.0.tar.gz | sed 's#.*/# #' > jupyterlab-3.2.9-0.0.tar.gz.sha256
```

