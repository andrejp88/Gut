Also supplied in this repo is the `gut_cmdln.gd` script that can be run from the command line so that you don't have to create a scene to run your tests.  One of the main reasons to use this approach instead of going through the editor is that you get to see error messages generated by Godot in the context of your running tests.  You also see any `print` statements you put in  your code in the context of all the Gut generated output.  It's a bit quicker to get started and is a bit cooler if I do say so.  The biggest downside is that debugging your code/tests is a little more difficult since you won't be able to interact with the editor's debugger when something blows up.

__Note:__ All the examples here come from my Mac/Bash.  I'm not sure how this works on Windows unless you are using a terminal emulator like ConEMU or something.  This might work with GitBash but I haven't tested it.  This should work fine in Bash on whatever else you use.

If you don't have a `godot` command, you'll have to make one.  Here's the one I use
```
alias godot='/Applications/Godot.app/Contents/MacOS/Godot'
```
From the command line, at the root of your project, use the following command to run the script.  Use the options below to run tests.

```
godot -d -s --path $PWD addons/gut/gut_cmdln.gd
```

The `-d` option tells Godot to run in debug mode which is helpful.  The `-s` option tells Godot to run a script. `--path $PWD` tells Godot to treat the current directory as the root of a project.

When running from command line, `0` will be returned if all tests pass and `1` will be returned if any fail (`pending` doesn't affect the return value).

### Options
_Output from the command line help (-gh)_
```
---  Gut  ---
---------------------------------------------------------
This is the command line interface for the unit testing tool Gut.  With this
interface you can run one or more test scripts from the command line.  In order
for the Gut options to not clash with any other godot options, each option
starts with a "g".  Also, any option that requires a value will take the form
of "-g<name>=<value>".  There cannot be any spaces between the option, the "=",
or inside a specified value or godot will think you are trying to run a scene.

Options
-------
  -gtest                    Comma delimited list of full paths to test scripts to run.
  -gdir                     Comma delimited list of directories to add tests from.
  -gprefix                  Prefix used to find tests when specifying -gdir.  Default "test_".
  -gsuffix                  Suffix used to find tests when specifying -gdir.  Default ".gd".
  -ghide_orphans            Display orphan counts for tests and scripts.  Default "False".
  -gmaximize                Maximizes test runner window to fit the viewport.
  -gexit                    Exit after running tests.  If not specified you have to manually close the window.
  -gexit_on_success         Only exit if all tests pass.
  -glog                     Log level.  Default 1
  -gignore_pause            Ignores any calls to gut.pause_before_teardown.
  -gselect                  Select a script to run initially.  The first script that was loaded using -gtest or -gdir that contains the specified string will be executed.  You may run others by interacting with the GUI.
  -gunit_test_name          Name of a test to run.  Any test that contains the specified text will be run, all others will be skipped.
  -gh                       Print this help, then quit
  -gconfig                  A config file that contains configuration information.  Default is res://.gutconfig.json
  -ginner_class             Only run inner classes that contain this string
  -gopacity                 Set opacity of test runner window. Use range 0 - 100. 0 = transparent, 100 = opaque.
  -gpo                      Print option values from all sources and the value used, then quit.
  -ginclude_subdirs         Include subdirectories of -gdir.
  -gdouble_strategy         Default strategy to use when doubling.  Valid values are [partial, full].  Default "partial"
  -gdisable_colors          Disable command line colors.
  -gpre_run_script          pre-run hook script path
  -gpost_run_script         post-run hook script path
  -gprint_gutconfig_sample  Print out json that can be used to make a gutconfig file then quit.
  -gfont_name               Valid values are:  [AnonymousPro, CourierPro, LobsterTwo, Default].  Default "CourierPrime"
  -gfont_size               Font size, default "16"
  -gbackground_color        Background color as an html color, default "ff262626"
  -gfont_color              Font color as an html color, default "ffcccccc"
  -gjunit_xml_file          Export results of run to this file in the Junit XML format.
  -gjunit_xml_timestamp     Include a timestamp in the -gjunit_xml_file, default False
---------------------------------------------------------
```

### Examples

Run godot in debug mode (-d), run a test script (-gtest), set log level to lowest (-glog), exit when done (-gexit)

`godot -s addons/gut/gut_cmdln.gd -d --path $PWD -gtest=res://test/unit/sample_tests.gd -glog=1 -gexit`

Load all test scripts that begin with 'me_' and end in '.res' and run me_only_only_me.res (given that the directory contains the following scripts:  me_and_only_me.res, me_only.res, me_one.res, me_two.res).  I don't specify the -gexit on this one since I might want to run all the scripts using the GUI after I run this one script.

`godot -s addons/gut/gut_cmdln.gd -d --path $PWD -gdir=res://test/unit -gprefix=me_ -gsuffix=.res -gselect=only_me`

### Config file
To cut down on the amount of arguments you have to pass to gut and to make it easier to change them, you can optionally use a json file to specify some of the values.  By default `gut_cmdln` looks for a config file at `res://.gutconfig.json`.  You can specify a different file using the `-gconfig` option.

Here is a sample file.  You can print out the text for a gutconfig file using the `-gprint_gutconfig_sample` option.
#### Example
``` json
{
  "dirs":["res://test/unit/","res://test/integration/"],
  "double_strategy":"partial",
  "ignore_pause":false,
  "include_subdirs":true,
  "inner_class":"",
  "log_level":3,
  "opacity":100,
  "prefix":"test_",
  "selected":"",
  "should_exit":true,
  "should_maximize":true,
  "suffix":".gd",
  "tests":[],
  "unit_test_name":"",
}
```
### Alias
Make your life easier by creating an alias that includes your most frequent options.  Combined with the config file, you can get a nice and easy to use `gut` command.  Here's the ones I use in bash on a Mac:

```
alias godot='/Applications/Godot.app/Contents/MacOS/Godot'
alias gut='godot --debug-collisions --path $PWD -d -s addons/gut/gut_cmdln.gd'
```

With this, if I want to run 'test_one.gd', I just enter
```
gut -gselect=test_one.gd
```

### Common Errors
I really only know of one so far, but if you get a space in your command somewhere, you might see something like this:
```
ERROR:  No loader found for resource: res://samples3
At:  core\io\resource_loader.cpp:209
ERROR:  Failed loading scene: res://samples3
At:  main\main.cpp:1260
```
I got this one when I accidentally put a space instead of an "=" after `-gselect`.
