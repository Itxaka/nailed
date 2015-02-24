[![Code Climate](https://codeclimate.com/github/MaximilianMeister/nailed/badges/gpa.svg)](https://codeclimate.com/github/MaximilianMeister/nailed)
## What is nailed?

`nailed` consists of a back-end CLI for data collection and a sinatra based web front-end for visualization of relevant development data of Products that have their bugtracker on Bugzilla and (optionally) their codebase on GitHub.

`Be aware` that the bugzilla layout (metadata) is still SUSE specific, which may not be useful for everybody.
e.g. it relies on bugs being tagged as L3. The plan is to make it optional in the future.

## Installation using gem
`gem install 'nailed'`
Note that this is always a bit outdated.

## Installation using git (recommended)
You can use nailed directly from a git checkout as well. Make sure to fetch the dependencies and call `nailed` from the `bin` directory.
### SUSE
```
zypper in libxml2-devel sqlite3-devel gcc make ruby-devel
bundle install
```

## Usage

```
$ nailed -h
Options:
      --migrate, -m:   Set database to pristine state
      --upgrade, -u:   Upgrade database
     --bugzilla, -b:   Refresh bugzilla database records
       --github, -g:   Refresh github database records
     --list, -l <s>:   List github repositories within organization
       --server, -s:   Start a dashboard webinterface
         --help, -h:   Show this message
```

## Initial setup

* for the bugzilla API make sure you have an `.oscrc` file with your credentials in ~
* for the github API make sure you have a `.netrc` with a valid GitHub OAuth-Token in ~
```
# example .netrc

machine api.github.com
  login MaximilianMeister
  password <your OAuth Token>
```
* configure your `products.yml`
* to setup the database run
```
nailed --migrate
```

## Configuration

All configuration is read from `config/config.yml`

``` yaml
---
debug: # true for debugging
title: # Give me a name, or (default) call me Dashboard
port: # Specify a port where you can reach the webinterface, or (default) 4567
bugzilla:
  url: # Url of your Bugzilla instance
products:
  example_product: # Just a a short key/name for the product (can be arbitrary)
    versions:
    # Array of Bugzilla products (typically different versions of one product)
    # Exact names have to be given, as they appear in Bugzilla (can not be arbitrary)
    organization: # here goes the organization name (under which your repos are hosted) as it appears in GitHub
    repos:
    # Array of GitHub repository names, as they appear in GitHub
    # query available repos with nailed -l <organization>
  # continue adding more products here

```

## Changes in production

* in production, after adding products/changes, to upgrade the database with the new changes run
```
nailed --upgrade
```
* make sure to fetch new data with
```
nailed --bugzilla
nailed --github
```
* restart the webserver

## Run

* create a `cronjob` for automated data collection with `nailed`
* start the webserver with `nailed --server`
