# Guide to Installing Requirements for a Local Instance of IRIDA Next

## Links

- [Installing IRIDA Next](https://github.com/phac-nml/irida-next)
- [Registering pipelines in IRIDA Next](https://phac-nml.github.io/irida-next/docs/configuration/pipelines)
- [GA4GH WES Sapporo Setup](https://github.com/phac-nml/irida-next/blob/main/docs-site/docs/development/integration/ga4gh_wes_sapporo_setup.md)

### _Before you begin ensure your user account has sudo privileges configured._

```bash
# Replace $USER with desired or existing username to grant and set up sudo privledges
$ sudo useradd $USER
$ sudo passwd $USER
$ sudo usermod -aG sudo $USER
```

# [Installing IRIDA Next](https://github.com/phac-nml/irida-next)

## Installng [asdf](https://asdf-vm.com/guide/getting-started.html) core

`asdf` is a tool version manager that uses a single `.tool-versions` file to ensure consistency. It simplifies development by replacing multiple CLI version managers with a unified interface and configuration file, supporting various tools through an easy plugin interface.

- Required dependencies: `git` and `curl`

**1. Download `asdf`**

```bash
# replace `v0.14.0` with the latest release tag available on the asdf GitHub release page
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.0
```

**2. Add `asdf` to your shell configuation**

- To integrate `asdf` with your shell you need to add the following lines to your shell configuration file (`~/.bashrc`, `~/.bash_profile`, etc.)

_Edit bash profile using a text editor_

```bash
vi ~/.bashrc
```

_Add the following lines at the end of the file_

```bash
# set up .asdf directory and file paths
export ASDF_DIR="$HOME/.asdf"

. ~/.asdf/asdf.sh

. ~/.asdf/completions/asdf.bash
```

_Source the shell configuration_

```bash
source ~/.bashrc
```

_Verify the installation_

```bash
asdf --version
# you should see the version number of asdf printed if it has been installed correctly
```

## Installing the required IRIDA Next plugins

**_To maintain consistency between IRIDA Next and your global installation, verify the `.tool-versions` file in the irida-next repository before installing the necessary plugins._**

**1. Install IRIDA Next and Verify Plugin Versions**

```bash
cd /path/to/git/repos
git clone https://github.com/phac-nml/irida-next
# Check .tool-versions and document plugin versions for installations
cd irida-next
less .tool-versions
```

**2. Install the [Node.js](https://github.com/asdf-vm/asdf-nodejs) Plugin**

`node.js` allows developers to run JavaScript on the server-side, facilitating scalable and efficient web applications with its event-driven architecture and extensive module ecosystem.

- Required dependencies: `apt-get` `install` `dirmngr` `gpg` `curl` and `gawk`

_Install the plugin_

```bash
asdf plugin add nodejs
```

_Install and set a global version_

```bash
# To check the available versions of the tool and find the version used by IRIDA Next.
asdf list all nodejs
# Install the desired $VERSION
# You can substitute the desired version with 'latest' to install the newest release of the plugin. This may cause incompatibility with IRIDA Next and is not recommended.
asdf install nodejs $VERSION
# Verify the install
which node
# Set global version for projects to use by default
asdf global nodejs $VERSION
# Verify that node.js was added to the .tools-versions file
less /home/$USER/.tools-versions
node --version
```

**3. Install the [pnpm](https://github.com/jonathanmorley/asdf-pnpm) Plugin**

`pnpm` is a fast, disk space-efficient package manager for JavaScript.

- Required dependencies: `curl` `grep` `cut` `sort` `xargs` `tar` and `ln`

_Install the plugin_

```bash
asdf plugin add pnpm
```

_Install and set a global version_

```bash
# To install the desired $VERSION
asdf install pnpm $VERSION
which pnpm
# Set global version for projects to use by default
asdf global pnpm $VERSION
# Verify that pnpm was added to the .tools-versions file
less /home/$USER/.tools-versions
pnpm --version
```

**4. Install the [ruby](https://github.com/asdf-vm/asdf-ruby) Plugin**

`ruby` is a high-level interpreted programming language known for its simplicity, readability, and productivity.

- Required dependencies: `apt-get` and `install`

_Install the `ruby-build` dependencies_

```bash
sudo apt-get install autoconf patch build-essential rustc libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libgmp-dev libncurses5-dev libffi-dev libgdbm6 libgdbm-dev libdb-dev uuid-dev
# Depending on your OS version, libgdbm6 may not be available. In that case, try an earlier version, such as libgdbm5
```

_Install the plugin_

```bash
asdf plugin add ruby
```

_Install and set a global version_

```bash
# To install the desired $VERSION
asdf install ruby $VERSION
which ruby
# Set global version for projects to use by default
asdf global ruby $VERSION
# Verify that ruby was added to the .tools-versions file
less /home/$USER/.tools-versions
ruby --version
```

**5. Install the [postgres](https://github.com/smashedtoatoms/asdf-postgres) Plugin**

`postgres` is an open-source, powerful, and advanced relational database management system (RDBMS) known for its robustness, extensibility, and compliance with SQL standards.

- Required dependencies: `apt-get` `install` and `curl`

_Install `postgres` specific dependencies_

```bash
# Ubuntu
sudo apt-get install linux-headers-$(uname -r) build-essential libssl-dev libreadline-dev zlib1g-dev libcurl4-openssl-dev uuid-dev icu-devtools libicu-dev
# Ubuntu (WSL)
sudo apt-get install build-essential libssl-dev libreadline-dev zlib1g-dev libcurl4-openssl-dev uuid-dev icu-devtools libicu-dev
```

_Install the plugin_

```bash
asdf plugin add postgres
```

_Install and set a global version_

```bash
# To install the desired $VERSION
asdf install postgres $VERSION
which postgres
# Set global version for projects to use by default
asdf global postgres $VERSION
# Verify that ruby was added to the .tools-versions file
less /home/$USER/.tools-versions
postgres --version
```

**6. Ensure bundler and remaining dependencies are installed**

```bash
# Ensure you are in the irida-next directory
cd /path/to/irida-next
# Ensure bundler installed
gem install bundler
# Install remaining dependencies
bundle && pnpm install
```

**7. Setup Postgres Server**

_Start by running:_

```bash
# Set correct $USER and postgres #VERSION
/home/$USER/.asdf/installs/postgres/$VERSION/bin/pg_ctl -D /home/$USER/.asdf/installs/postgres/$VERSION/data -l logfile start
```

_Create a new role (user) and set the password_

```bash
createuser -s test -P -U postgres
# When prompted for a password for the `test` role above, set the password as `test`. These are the credentials used by the development and test databases.
```

**8. Generate and configure the environment credentials**

Refer to [GA4GH WES Sapporo Setup](#ga4gh-wes-sapporo-setup-for-development) for how to setup and configure the IRIDA Next environment credentials for development purposes.

**9. Initialize the databases**

_Run:_

```bash
cd /path/to/irida-next
bin/rails db:create db:migrate db:seed
```

**10. Register pipelines**

Refer to [Registering pipelines in IRIDA Next](#registering-pipelines-in-irida-next) for how to register Nextflow pipelines.

**11. Restarting**

Rebuild and restart the sapporo docker container, restart postgres, and start Irida Next following the steps found here: [Running a local instance of IRIDA Next](#running-a-local-instance-of-irida-next).

# [GA4GH WES Sapporo Setup for development](https://github.com/phac-nml/irida-next/blob/main/docs-site/docs/development/integration/ga4gh_wes_sapporo_setup.md)

## Prerequisites

You will need to have a Docker Compose plugin installed to manage Sapporo.

_Install Docker Compose plugin on Ubuntu_

```bash
sudo apt-get update
sudo apt-get install docker-compose-plugin
# Verify installation
docker compose version
# Should be version v2.26.1 or newer
```

_Add yourself to the docker group to run docker commands_

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
# Reboot, log out/log in, or run the following command
newgrp docker
#If you still have issues with running docker, you may need to change the permissions of the docker socket using the following command
sudo chmod 666 /var/run/docker.sock
```

## Configure IRIDA Next environment developer credentials

_Check that your active storage service is set to `:local` in `config/environments/development.rb`_

```bash
# check if the active storage service is set to :local
grep -q "config.active_storage.service = :local" config/environments/development.rb && echo "Active storage service is set to :local" || echo "Active storage service is not set to :local"
```

_Configure environment developer credentials_

```bash
EDITOR="vim --nofork" bin/rails credentials:edit --environment development
```

```bash
# Add the following to the credentials file and save in the text editor
ga4gh_wes:
  server_url_endpoint: 'http://localhost:1122/'
```

- Make sure that there's no comment characters (`#`) preceding these lines!

- You may replace `vim` with any text editor of your preference.

## Setup Sapporo (WES implementation)

_Download and run the [PHAC-NML Sapporo]() fork in dev docker mode. If docker permissions are setup correctly you should not have to use `sudo` when running these commands._

```bash
cd ~/path/to/git/repos
git clone https://github.com/phac-nml/sapporo-service
cd sapporo-service
# This branch has a custom docker compose script for IRIDA Next
git checkout irida-next
# Replace /PATH/TO/IRIDA/NEXT/REPO with your irida next repo path.
# This allows the docker container read/write access to the repo
# This is needed for it to read the input files, and write the output files back to the blob directories
IRIDA_NEXT_PATH=/PATH/TO/IRIDA/NEXT/REPO MYUID="$(id -u)" MYGID="$(id -g)" docker compose -f compose.irida-next.yml up -d --build
IRIDA_NEXT_PATH=/PATH/TO/IRIDA/NEXT/REPO MYUID="$(id -u)" MYGID="$(id -g)" docker compose -f compose.irida-next.yml exec app bash
# Within docker container, start sapporo
sapporo
```

_In a new terminal confirm Sapporo is running_

```bash
# This should output all the service information for this ga4gh wes instance
curl -X GET http://localhost:1122/service-info
```

# [Registering pipelines in IRIDA Next](https://phac-nml.github.io/irida-next/docs/configuration/pipelines)

Pipelines are registered at `postgres` server startup, requiring restarts to register new pipelines added to the configuration file.

- Currently only Nextflow pipelines are supported and they must have a Github repository.
  - Ensure there is a `nextflow_schema.json` file at the top level of the repository and a `schema_input.json` file located in an `assets` directory.

**_Setup the configuration file to register pipelines_**

The `pipelines.json` configuration file to register pipelines is stored in the `/home/$USER/irida-next/config/pipelines/` directory.

- The `pipelines.json` file should be in the format below and must contain the following:
  - **URL** of the pipeline Github repository
  - **name** of the pipeline
  - **description** of the pipeline
  - **versions** of the pipeline that should be available to launch. **Note**: The `name` in the versions refers to the `-r` flag used by nextflow.

_E.g._

```bash
[
  {
    "url": "https://github.com/phac-nml/gasnomenclature",
    "name": "phac-nml/gasnomenclature",
    "description": "Gas Nomenclature assignment pipeline",
    "versions": [
      {
        "name": "dev"
      }
    ]
  },
  {
    "url": "https://github.com/phac-nml/speciesabundance",
    "name": "phac-nml/speciesabundance",
    "description": "SpeciesAbundance Pipeline",
    "versions": [
      {
        "name": "2.1.1"
      }
    ]
  }
]
```

# Running a local instance of IRIDA Next

**1. Run the PHAC-NML Sapporo fork in dev docker mode**

```bash
cd /path/to/sapporo-service
git checkout irida-next
IRIDA_NEXT_PATH=/PATH/TO/IRIDA/NEXT/REPO MYUID="$(id -u)" MYGID="$(id -g)" docker compose -f compose.irida-next.yml up -d --build
IRIDA_NEXT_PATH=/PATH/TO/IRIDA/NEXT/REPO MYUID="$(id -u)" MYGID="$(id -g)" docker compose -f compose.irida-next.yml exec app bash
sapporo
```

_Open a new terminal to confirm that Sapporo is running and then continue to step 2_

```bash
curl -X GET http://localhost:1122/service-info
```

**2. Start the postgres server**

```bash
/home/$USER/.asdf/installs/postgres/14.6/bin/pg_ctl -D /home/$USER/.asdf/installs/postgres/14.6/data -l logfile start
```

**3. Serve the local instance of the IRIDA Next web application**

```bash
cd /path/to/irida-next
bin/dev
```

Navigate to your browser to http://localhost:3000

The first time you serve IRIDA Next, you will need to **Register a local account**.
Once completed, you can use IRIDA Next as usual in the web browser.

# Additional Installation and Usage Details

## Using Another IP Address

If you want to use another IP address than `localhost`, you'll need to make the following changes:

1. Developer Credentials

```bash
EDITOR="vim --nofork" bin/rails credentials:edit --environment development
```

```bash
ga4gh_wes:
  server_url_endpoint: 'http://IP:1122/'
```

- Make sure that there's no comment characters (`#`) preceding these lines!

2. Docker Conatiner

   https://github.com/phac-nml/sapporo-service/blob/irida-next/compose.irida-next.yml#L32

```bash
    ports:
      - ip:1122:1122
```

- Make sure to rebuild the docker container, relaunch, and start sapporo.

3. Irida Next

   `Procfile.dev`

```bash
web: bin/rails server -b ip -p 3000
```

4. Restarting

   To be safe, rebuild and restart the sapporo docker container, restart postgres, and restart Irida Next.

## Pipelines that Require Databases

- When selecting databases remember that Sapporo operates within a Docker container and does not have full access to the host filesystem.
  - To address this, you can provide the full path to the location where the GitHub repository codes are downloaded by the Sapporo service:

```bash
/PATH/TO/sapporo-service/run/.nextflow/assets/phac-nml/...
```

## Finding Error Logs from a Workflow Execution in Local IRIDA Next Instance

- If a workflow execution stalls with an **error** `state` and a `RUN ID` is provided, you can find the error logs at:

```bash
# Replace `[RUN ID]` with the run ID from IRIDA Next

#Remote error logs
http://localhost:1122/runs/[RUN ID]

# Local error logs
# Replace [##] with the first two digits of the RUNID
sapporo-service/run/[##}/[RUN ID]/.nextflow.log
```

## Updating IRIDA Next

In order to update IRIDA Next the following steps can be taken:

```bash
cd path/to/irida-next
git fetch origin
git pull origin main
# Resolve any conflicts that arise with the pull, such as changes made to the `pipelines.json` file
```

You may need to repeat the installation of IRIDA Next dependencies and/or update the existing `asdf` plugins.

- To update IRIDA Next dependencies:

```bash
# Ensure newest bundler is installed
gem install bundler
# Check remaining dependencies
bundle && pnpm install
# Re-initialize the database
bin/rails db:create db:migrate db:seed
```

- If prompted to update plugins to new versions, check the `irida-next/.tool-versions` file and refer to the section [Installing the required IRIDA Next plugins](#installing-the-required-IRIDA-Next-plugins)

_E.g._

```bash
# May need to update the plugin in order to install the required version
asdf plugin update ruby
asdf install ruby $VERSION
asdf global ruby $VERSION
ruby --version
# If you no longer need the old version of the plugin you can remove it
asdf uninstall ruby <old-version>
```

Make sure to rebuild and restart the Sapporo Docker container, restart the postgres server, and start IRIDA Next.
