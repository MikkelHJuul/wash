# WASH - Wrapper for BASH
or "`wash` away your boilerplate"

`wash` is a `bash` script that you can call with a file (or from your file via shebang). `wash` handles command invocation, variables and help-messages.

## Usage
### Exporting your method
it's use is fairly simple, given a bash script, any `function` starting with `_` is exported and callable in the reference of `wash` ie.:
```bash
#!/usr/bin/env wash

function _my_func() {
   ... does nothing
}
```
is a callable script, with `my_script my_func` calling the method `_my_func`.

### variables
The function syntax of your script must give all variables before the command.
```bash
my_script -my-variable some_value -other-var=john --third-var Ellis --flag my_func
```
will expand variables: `_MY_VARIABLE=some_value, _OTHER_VAR=john, _THIRD_VAR=Ellis, _FLAG=my_func`
to be freely referenced in you script

### adding help messages
your script has a help method. the help method prints any text from your script following the syntax `#HELP`

to sum it all up; given!
```bash
#!/usr/bin/env wash

#HELP my function does almost nothing:\n_PROGRAM -my-variable <value> -other-var=<value> -third-var <value> -flag my_func
function _my_func() {
   echo "$_MY_VARIABLE, $_OTHER_VAR, $_THIRD_VAR, $_FLAG"
   echo "Hello $@" # these are all the passed arguments
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


## Licence notice
This project is not my invention, I generalised the concepts, and great work of [p8952/bocker](https://github.com/p8952/bocker).
Which is also the reason this project has the GPL v3 license (condition of reuse of intellectual property)

