Piece of Cake - Simple Cakefile for CoffeeScript and Stylus compilation
=============

Very simple cake file for development. Auto-compiles and watches server-side Coffeescript files. Auto-compiles Stylus files. Runs main script using Supervisor. This is used in https://github.com/NullEmpire/coffeescript-express. Which is a coffeescript / express bootstrap.
## Requirements
* `sudo npm install -g stylus`
* `sudo npm install -g supervisor`
* `sudo npm install -g coffee-script`

```coffeescript
fs = require 'fs'
{print} = require 'sys'
{log, error} = console; print = log
{spawn, exec} = require 'child_process'

run = (name, args...) ->
  proc = spawn(name, args)
  proc.stdout.on('data', (buffer) -> print buffer if buffer = buffer.toString().trim())
  proc.stderr.on('data', (buffer) -> error buffer if buffer = buffer.toString().trim())
  proc.on('exit', (status) -> process.exit(1) if status isnt 0)

task 'dev', 'Watch src/ for changes, compile, then output to lib/ ', (options) ->
  
  #server side coffeescript files
  run 'coffee', '-o', 'lib/', '-wc', 'src/'

  #client side coffeescript files
  run 'coffee', '-o', 'public/js/', '-wc', 'public/js/src/'

  #stylus
  run 'stylus','-o', 'public/css', '-w', 'public/css/src'

  #run server.js using Supervisor
  run 'supervisor', ['server.js']
```