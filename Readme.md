mage-console
============

Alternative MAGE development console, with REPL interface.

![screen capture](./screen.gif)

Features
--------

  - Activates debugging (`--debug` for Node.js < 8.0.0, `--inspect` otherwise)
  - REPL interface which lets you introspect your MAGE application
  - Watches your './lib' folder and automatically reloads the worker on file changes
  - Works for both JavaScript and TypeScript projects

Requirements
------------

### MAGE Version

This module is compatible with MAGE >= 1.0.0. Note that if
you have upgraded an older MAGE application to MAGE 1.0.0, you
will need to make sure that your `./lib/index.js` follows
the new initialisation pattern.

### Application configuration

`mage-console` will only work when your application is configured
in cluster mode to start a single process:

```yaml
### Make sure `config/development.yml is configured as such
server:
  cluster: 1
```

Installation
------------

In your MAGE project:

```shell
npm install --save mage-console
```

Then, in your project's `package.json`, update the develop script job
from `npm run mage` to `mage-console`:

```json
{
    "scripts": {
        "develop": "mage-console"
    }
}
```

If your project is a TypeScript project, set the value to `ts-mage-console` instead:

```json
{
    "scripts": {
        "develop": "ts-mage-console"
    }
}
```

Then simply run `npm run develop` like you would normally do.

Configuration
--------------

You may configure `mage-console` by adding configuration entries into your MAGE
configuration. See [MAGE's documentation](https://mage.github.io/mage/#configuration) for more details.

> config/default.yaml

```yaml
external:
  mage-console:
    # Where to put the socket file for the console.
    # Default: [project-dir]/node_modules/mage-console/mage-console.sock
    sockfile: /tmp/mage-console.sock

    # Watch additional files; we will always watch `./config' and './lib',
    # but you may want to watch additional folders as well.
    watch:
      - /tmp/file
      - ./www
```

Debugging
---------

### Ports

The following ports are used automatically for debugging:

  * TypeScript: 9229 (inspect protocol, using `--inspect` flags);
  * Node 8.0 and up: 9229 (inspect protocol, using `--inspect` flags);
  * Node less than 8.0: 5858 (legacy protocol, using `--debug` flags);

The debugging interface is activated on workers: this means that when you will
save code, the worker will be restarted, and your debugging session will be terminated.

### Remote debugging (docker)

While debugging is activated by default, debugging is attached to localhost;
if you develop in a Docker container, this means you won't be able to connect
remotely to the debugging port.

To solve this issue, you can set the `DEBUG_HOST` environment variable, as follow:

```shell
DEBUG_HOST="0.0.0.0" npm run develop
```

This will instruct `mage-console` to have the worker bind the debugging interface
on all available interfaces instead of only on localhost.


### Visual Studio Code configuration

The default debugging port selection is made to match the behaviour
of VSCode. However, if you are using TypeScript, you will need to specifically
mention that you are using the new inspect protocol:

> .vscode/launch.json

```json
{
  "version": "0.2.0",
  "configurations": [{
    "name": "Attach to MAGE instance",
    "type": "node",
    "request": "attach",
    "sourceMaps": true,
    "outDir": "${workspaceRoot}/dist",
    "protocol": "inspector",
    "internalConsoleOptions": "neverOpen"
  }]
}
```

License
--------

MIT.
