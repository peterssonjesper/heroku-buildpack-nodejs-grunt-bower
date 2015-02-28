Heroku Buildpack for Node.js with Grunt, and Bower
============================

This is a fork of the official [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for Node.js apps. Grunt, and Bower, have been added to the buildpack so that you can keep your dependencies and compiled code out of your repository. 


How it Works
------------

Here's an overview of what this buildpack does:

- Uses the [semver.io](https://semver.io) webservice to find the latest version of node that satisfies the [engines.node semver range](https://npmjs.org/doc/json.html#engines) in your package.json.
- Allows any recent version of node to be used, including [pre-release versions](https://semver.io/node.json).
- Uses an [S3 caching proxy](https://github.com/heroku/s3pository#readme) of nodejs.org for faster downloads of the node binary.
- Discourages use of dangerous semver ranges like `*` and `>0.10`.
- Uses the version of `npm` that comes bundled with `node`.
- Puts `node` and `npm` on the `PATH` so they can be executed with [heroku run](https://devcenter.heroku.com/articles/one-off-dynos#an-example-one-off-dyno).
- Caches the `node_modules` directory across builds for fast deploys.
- Doesn't use the cache if `node_modules` is checked into version control.
- Runs `npm rebuild` if `node_modules` is checked into version control.
- Always runs `npm install` to ensure [npm script hooks](https://npmjs.org/doc/misc/npm-scripts.html) are executed.
- Always runs `npm prune` after restoring cached modules to ensure cleanup of unused dependencies.
- Installs `bower` via `npm`
- Installs bower components to the directory defined in `.bowerrc` or `bower_components`
- Caches installed bower components for faster deploys
- Always runs `bower prune` after restoring cached components to ensure cleanup of unused dependencies.
- Installs `grunt`
- Runs the `grunt` task `build` or `build:$NODE_ENV` if the node env variable has been set

For more technical details, see the [heavily-commented compile script](https://github.com/heroku/heroku-buildpack-nodejs/blob/master/bin/compile).


Usage
-----

Create a new app with this buildpack:

    heroku create myapp --buildpack https://github.com/TeehanLax/heroku-buildpack-nodejs.git

Or add this buildpack to your current app:

    heroku config:add BUILDPACK_URL=https://github.com/TeehanLax/heroku-buildpack-nodejs.git
    
Enable heroku `user-env-compile` lab:
    
    heroku labs:enable user-env-compile

Set the `NODE_ENV` environment variable (e.g. `development` or `production`):

    heroku config:set NODE_ENV=production

Create your Node.js app and add a Gruntfile named  `Gruntfile.js` (or `Gruntfile.coffee` if you want to use CoffeeScript, or `grunt.js` if you are using Grunt 0.3) with a `heroku` task:

    grunt.registerTask('build:development', 'clean less mincss');
    
or

    grunt.registerTask('build:production', 'clean less mincss uglify');

Push to heroku

    git push heroku master
