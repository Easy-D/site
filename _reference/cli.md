---
title: "CLI"
---

## Convox Update

You can easily update the CLI to get bugfixes and features:

    $ convox update
    OK, 20160617165137

    $ convox --version
    client: 20160617165137
    server: 20160615213630 (console.convox.com)

## Switching Between Racks

The Convox Console makes it easy to install and manage multiple Racks, like one for development, staging and production. The `convox` CLI offers a few strategies to switch between these environments.

#### Switch Command

You can use `convox racks` and `convox switch` to select a Rack that your computer will operate against by default.

    $ convox racks
    myorg/staging                    running    
    myorg/production                 running    
    personal/staging                 running

    $ convox switch production
    Switched to myorg/production

    $ convox switch staging
    ERROR: You have access to multiple racks with that name, try one of the following:
    convox/staging
    personal/staging

    $ convox switch personal/staging
    Switched to personal/staging

    $ convox switch
    personal/staging

#### Rack Flag

You can specify a specific Rack on a per-command basis with the `--rack` flag:

    $ convox switch
    myorg/staging

    $ convox apps
    APP           STATUS 
    api-staging   running
    docs-staging  running

    $ convox apps --rack personal/staging
    APP    STATUS 
    httpd  running
    rails  running

#### CONVOX_RACK Environment Variable 

You can specify a specific Rack for a new terminal session with the CONVOX_RACK environment variable:

    $ convox switch
    myorg/staging

    $ export CONVOX_RACK=personal/staging

    $ convox switch
    personal/staging

    $ convox apps
    APP    STATUS 
    httpd  running
    rails  running

#### App Repository Setting

You can set a default Rack name in an app's repository. This will take precedence over the `convox switch` setting:

    $ cd rails
    $ mkdir .convox
    $ echo personal/staging > .convox/rack
    $ convox deploy

#### Client Rack Header

All of the above tools control what `Rack` header is sent in API commands. You can also control this explicitly when using the API:

    $ curl -u :$API_KEY -H "Rack: personal/staging" https://console.convox.com/apps
    [
      {
        "name": "httpd",
        "release": "RLBTJENIMQI",
        "status": "running"
      },
      {
        "name": "rails",
        "release": "RGPIVDMTGVH",
        "status": "running"
      }
    ]

#### Precedence

The order of precedence is:

* --rack flag
* CONVOX_RACK environment variable
* ./convox/rack app repository setting
* `convox switch` default setting in ~/.convox/rack

When you want to manage multiple racks in multiple terminals you should use `CONVOX_RACK`.

When you want to pin an app to a specific Rack you should use `./convox/rack` which can only be overridded by an explicit `--rack` flag.

## Bash Autocomplete and PS1 Helpers

The `convox` CLI offers bash autocompletion and command prompt utilities.

To set this up on OS X with Homebrew, save the convox autocomplete helper in the `bash_completion.d` directory, then add an autocomplete initializer and `PS1` variable to your `~/.bash_profile`:

    $ curl -o $(brew --prefix)/etc/bash_completion.d/convox https://raw.githubusercontent.com/codegangsta/cli/master/autocomplete/bash_autocomplete

    $ cat ~/.bash_profile
    if [ -f $(brew --prefix)/etc/bash_completion ]; then
        . $(brew --prefix)/etc/bash_completion
    fi

    __convox_switch() { [ -e ~/.convox/rack ] && convox switch || echo unknown; }
    export PS1="\h:\W \u (\$(__convox_switch))\$(__git_ps1)$ "


Now open a new tab and try `convox` and `convox builds` followed by the TAB key:
 
    Study:rails noah (unknown) (master)$ convox switch personal/staging
    Study:rails noah (personal/staging) (master)$ 

    Study:rails noah (personal/staging) (master)$ convox <TAB>
    api         builds      env         help        instances   proxy       racks       run         ssl         uninstall   
    apps        certs       exec        init        login       ps          registries  scale       start       update      
    build       deploy      h           install     logs        rack        releases    services    switch    

    Study:rails noah (personal/staging) (master)$ convox builds <TAB>
    copy    create  delete  h       help    info 