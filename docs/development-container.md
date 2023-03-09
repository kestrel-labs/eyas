# development-container
The development container allows developers to sandbox repository dependencies
from their host machine while allowing them to use familiar development tools.

# TL;DR
Build a new development image
```
mkdir -p ~/.eyas/ccache
export UID=$(id -u) export GID=$(id -g); docker compose -f .devcontainer/compose.dev.yml build
```
Start an interactive development container
```
docker compose -f .devcontainer/compose.dev.yml run development bash
```
Build the repository in the container
```
username@eyas-dev:~/ws$ colcon build
```

# Troubleshooting
#### ccache cannot compile
```
"/usr/lib/ccache/cc"

  is not able to compile a simple test program.
```
Likely, the host ccache directory in your [home directory](#ccache) was not created properly.
Ensure that the directory is created and owned by the host user.
```
mkdir -p ~/.eyas/ccache
sudo chown -R $(id -u):$(id -g) ~/.eyas/ccache
