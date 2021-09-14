# Mixtape

This repo meant to help ramp up Accelerators new to Ruby & Padrino

## Setup

#### Forking repo

In the top right of the screen (below your profile picture) there should
be an option to **fork** the repo. Click the **fork** button and select the
first option.
 
#### Cloning repo
In the command line enter
````shell
$ cd
$ git clone git@github.com:<GITHUB-USERNAME>/accelerate-mixtape.git
````

#### Setting up docker

Make sure you have docker installed
```shell
docker -v
```
If the command outputs an error then download docker from here
https://www.docker.com/products/docker-desktop

After installation launch the Docker app in "Applications" and wait for
the app to start running (status is shown in the menu bar)

Move to the directory with the docker compose file (essentially the docker
setup instructions)
```shell
$ cd accelerate-mixtape/mongo/_docker
```

#### Setting up MongoDB docker image

Check to see if you have the proper MongoDB image already:
````shell
$ docker images
````
In the output the `mongo` repository should be listed with the tag `4.0`:
```shell
$ docker images
REPOSITORY   TAG          IMAGE ID       CREATED        SIZE
mongo        4.0          5323653a5708   7 months ago   427MB
```

If this is not true, run the following commands:
```shell
$ brew tap mongodb/brew 
$ brew install mongodb-community@4.0
```
It could take a while

Once that's done, run:
```shell
$ docker-compose up -d
```
This starts up the MongoDB container with the instructions found in
the `docker-compose.yml` file.

At this point the database is up and running, but it is not initialized.
To do this we have to connect to the Mongo instance and run a quick
command:

```shell
$ mongo --host localhost:27018
> rs.initiate({_id:"rsm0", members: [{"_id":1, "host":"localhost:27018"}]})
```
There :) If you have Robo3T installed you can attempt to connect with the
address localhost:27108. The database will be empty, but you should be able
to connect to it.

#### Setting up the Padrino project
##### Installing Brew
_Skip this if you already have brew installed_

Run: 
```shell
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```
##### Install Ruby
_Skip this if you already have Ruby and RVM installed_

Download RVM (ruby version manager)
```shell
curl -L https://get.rvm.io | bash -s stable
```
Quit and re-open the terminal

Download `Ruby 2.6.6`
```shell
rvm install ruby-2.6.6
rvm use
```

If you were unable to download Ruby, run:
```shell
$ rvm autolibs enable
```
and try again

##### Installing Bundler
_Skip this if you already downloaded bundler_

Run the following commands
```shell
$ brew install libpqxx
$ brew install postgresql
$ gem install bundler -v 1.17.3
```

##### Install the padrino gem
Run:
```shell
$ gem install padrino
```
##### Create the project
Run:
```shell
$ padrino g project mixtape -t rspec -e haml -c scss -s jquery
```
This starts a Padrino project that uses `rspec` for testing `haml` for 
rendersing `sass` for formatting and `jquery` for scripts.

For sake of Nutella conventions rename the just created`mixtape` directory
to `web`:
```shell
$ mv mixtape web
```
Now lets install all our gems:
```
bundle install
```
_If you don't have bundler installed run_:
```
$ gem install bundler
```

##### Setting up code organization
Create a `common` folder that will hold most of the code that does not fall
under controllers or  presenters
```
$ mkdir common
```
We want to load this directory on app startup so we have to add it to
the `boot.rb` file. Open this file and look for the line that looks
something like:
```ruby
Padrino.dependency_paths.unshift(
  Padrino.root('config/initializers/*.rb')
)
```
and change it to:
```ruby
Padrino.dependency_paths.unshift(
    Padrino.root('config/initializers/*.rb'),
    Padrino.root("common/**/*.rb")
#   this will be commented out when we're ready to create our presenters!
#   Padrino.root("api/presenters/**/*.rb")
)
```
This tells Padrino to load any and all ruby files in the `common` directory.

Now create `models` in the common directory then add the subdirectories
`actions`, `commands`, `entities`, and `queries`:
```
$ cd common
$ mkdir models
$ cd models
$ mkdir actions
$ mkdir commands
$ mkdir entities
$ mkdir queries
```
Models holds the code that is coupled closely to our data models.

`actions` will contain code used to log different actions taken on the
models (e.g creating, updating, or deleting a model).

`commands` will contain code that actually performs those actions. It
strictly writes to the database and is not to do anything really fancy.

`entities` will contain the code that is the abstraction of our models.

`queries` will contain code that will run queries to the database, e.g
`find`.

Move back to the `common` directory and create a stores directory
with the subdirectory `database`:
```
cd ../
mkdir stores
cd stores
mkdir database
```
This database directory will contain the code that abstracts how we
interact with our Mongo database

## Next steps

Congrats! Setup is complete :)

Up next is completing the exercises! You'll be able to find them in
 the folder named `exercises`. The first one will be creating that
 abstraction layer just mentioned.