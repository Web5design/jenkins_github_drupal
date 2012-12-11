![jenktocat](https://api.monosnap.com/image/download?id=9PMKonRKS2i1el0vJZ7cuK1oQ)

## Usage
This script should be executed within Jenkins, and will fail otherwise.

First, call the directory preparer, which moves the pull request to your
webroot, and merges it into master.

`prepare_dir.sh` `[-hiv]` `<webroot>`

Then, call the site cloning script, which uses drush to clone an existing
staging site.

`clone_site.sh` `[-deghilv]` `<source-drush-alias>` `<url>`

To clean up afterwards, call cleanup.sh using the same pull request ID and
location of your webroot.

`cleanup.sh` `[-dhilv]`

## What does it do?
- Moves the checked out repository to a unique directory in the workspace.
- Creates a symlink to the docroot of the drupal directory in the webroot.
- Creates a new branch from the pull request and merges that branch to
  master.
- Copies the settings.php from an existing site to this new drupal site.
- Clones the database from the source site, prefixing any tables with a
  unique identifier to this pull request.
- Rsyncs the files directory from the source site.

## Requirements
- Drush
- A web accessible URL for the pull request. The location of the docroot for
  this URL should be specified with the -l option.
- An existing Drupal 7 site with a site alias, and empty prefix line in the
  database array in settings.php
- A Jenkins job that checks out the Pull Request to 'new_pull_request' directory
  inside the job workspace.

## Arguments
`<webroot>`

  Location of the parent directory of the web root this site will be hosted at.
  Defaults to the job workspace. Note, the Jenkins user must have write
  permissions to this directory.

`<source-drush-alias>`

  The drush alias to the site whose database and files will be cloned to build
  the pull request test environment.

`<url>`

  The parent URL that the destination site will be visible at. Defaults to
  'http://default'. The domain name the site will be set up at. Note, the site
  will be in a subdirectory of this domain using the Pull Request ID, so if the
  Pull Request ID is 234, and you pass https://www.example.com/foo, the site
  will be at https://www.example.com/foo/234.

## Options
* `-e`  Optional. Extra settings to be concatenated to the settings.php file
        when it is cloned. Only used in clone_site.sh.
* `-g`  Optional. The http server's group, such as www-data or apache. This is
        only used in clone_site.sh to ensure that the file permissions are set
        properly on the Drupal file directory.
* `-h`  Show this message
* `-i`  The Github pull request issue number.
* `-l`  The location of the parent directory of the web root. Same as
        `<webroot>`.
* `-d`  The path to drush. Defaults to drush.
* `-v`  Verbose mode, passed to all drush commands.
