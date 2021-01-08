# WASH - Wrapper for bASH
or "`wash` away your boilerplate"

`wash` is a `bash` script that you can call with a bash file (or from your file via shebang). `wash` handles command invocation, variables and help-messages.

## Usage
With `wash` the character `_` is a special character; variables are expanded to properties that start with `_` and methods are exported when they start with `_`, this is simply a gimmick to make the script work and the properties clearly noticable.
### Exporting your method
`wash`'s use is fairly simple, given a bash script, any `function` starting with `_` is exported and callable in the reference of `wash` ie.:
```bash
#!/usr/bin/env wash

function _my_func() {
   ... does nothing
}
```
is a callable script, with `my_script my_func` calling the method `_my_func` (notice the shebang above).

### Variables
The function syntax of your script must give all variables before the command. Or all variables after the command.
```bash
my_script -my-variable some_value -other-var=john --third-var Ellis --flag my_func
```
will expand variables: `_MY_VARIABLE=some_value, _OTHER_VAR=john, _THIRD_VAR=Ellis, _FLAG=my_func`
to be freely referenced in you script, as you see the variables are expected to be `kebab-case` and will be expanded to `SCREAMING_SNAKE_CASE` with a prefixed underscore.

The generalised syntax is
```bash
script_name [command <properties> | <properties> command] args...
```

note: flags isn't actually a real thing; if you use the syntax `script_name command <properties> args...` and want to put a `flag` as the last parameter set it as `-flag=` or it will consume the first argument. This is an accepted limitation of the script. Please suggest the user the syntax `my_script <command> <flags> <properties> <args>`.

version 0.5 further fixes a bug where `--key=value` or `--flag` as the last parameter would fail.
### Adding help messages
your script has a help method. the help method prints any text from your script following the syntax `#HELP`

to sum it all up; given!
```bash
#!/usr/bin/env wash

#HELP my function does almost nothing:\n_PROGRAM -my-variable <value> -other-var=<value> -third-var <value> -flag my_func
function _my_func() {
   echo "$_MY_VARIABLE, $_OTHER_VAR, $_THIRD_VAR, $_FLAG"
   echo "Hello $@"  # these are all the passed arguments
}
```
will print help message
```bash
my function does almost nothing:
	my_script -my-variable <value> -other-var=<value> --third-var <value> -flag my_func

display this message:
	my_script help

```
and calling the method:
```bash
my_script -my-variable some_value -other-var=john --third-var Ellis --flag my_func Lilly and others
some_value, john, Ellis, my_func
Hello Lilly and others
```

The help message output can be overridden by implementing the method `_help` in your script. 

### bash settings
The default best practice (ie. boilerplate) for running scripts are in short `set -euo pipefail`. 
I originally inherited this setting from `bocker` and I decided in version `0.4` to keep these settings 
(but to remove `shopt -s nullglob` - `shopt` settings are contestable, but `nullglob` and `failglob` together are the most common duo) 

You should write bash scripts using settings: `errexit`, `nounset` and `pipefail` always! - ie `set -euo pipefiail` or `set -o errexit -o nounset -o pipefail` 

This default can be ~un~set with `set +euo pipefail` in the beginning of your file. or locally if wanted. see the [bash settings test](tests/test_bash_settings).

#### Running with trace
in version `0.4` I added the feature to run `wash` with trace (`set -o xtrace`), run with the flag `-x`. 
To do this you have to run with the direct path to wash in your shebang, ie. `#!/usr/bin/wash -x` or insert into you file via: `sed -i "1i \#!$(which wash) -x" my_script`  <sup>try saying `which wash` fast 5 times in a row</sup>

If you want trace you can, of course, skip tracing the `wash` wrapping code by simply adding `set -x` at the top of your file, or at a local context.

## Installation
it's a `bash` script; install the script `wash` in an executable file named `wash` on your `PATH`. This enables calling `wash` from anywhere, and the shebang syntax (`#!/usr/bin/env wash`).

##### install via curl:
```bash
curl https://raw.githubusercontent.com/MikkelHJuul/wash/main/wash > /usr/bin/wash
chmod +x /usr/bin/wash
```

##### install via docker
```bash
docker run --rm -d --name wash mjuul/wash tail -f /dev/null
docker cp wash:/usr/bin/wash /usr/bin/wash
docker stop wash
chmod +x /usr/bin/wash
```

##### install in docker image
```Dockerfile
...
COPY --from=mjuul/wash [--chmod=myuser] /usr/bin/wash /usr/bin/wash
...
```

### Prerequisites
Bash 4 (4.4?)

## Licence notice
This project is not my invention, I generalised the concepts and great work of [p8952/bocker](https://github.com/p8952/bocker).

This is also the reason for this project to license under the GPL v3 license (condition of reuse of intellectual property). I would have picked UNLICENSE.
