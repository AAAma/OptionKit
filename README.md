**NOTE**: OPTIONKIT IS NOT YET READY FOR USE

OptionKit - Option Parsing in Swift
=========

OptionKit is an OS X framework to parse basic command-line options in pure Swift. It
does not currently support sub-parsers.

### Overview

OptionKit currently supports three types of options:

* Short options, triggered by flags of the type `-f`
* Long options, triggered by flags of the type `--long-option`
* Mixed options, triggered by either type, such as `-v` or `--version`

An option (of any type) can have zero or more required parameters. Parameters are restricted
in that they cannot begin with `-` or `--`, as they would be confused with triggers.

OptionKit's `OptionParser` class returns a dictionary of `Option` objects mapped to
their parameters, if any. This is different from the way Ruby's OptionParse works, which is by
providing callbacks to be triggered by option calls.

### Example

A simple, full example called `optionsTest.swift` might be:

```swift
#!/usr/bin/env xcrun swift -F /Library/Frameworks

import Foundation
import OptionKit

let opt1 = Option(trigger:.Mixed("e", "echo"))
let opt2 = Option(trigger:.Mixed("h", "help"))
let opt3 = Option(trigger:.Mixed("a", "allow-nothing"))
let opt4 = Option(trigger:.Mixed("b", "break-everything"))
let opt5 = Option(trigger:.Mixed("c", "counterstrike"))
let parser = OptionParser(definitions:[opt1, opt3, opt4, opt5])

let result = parser.parse(Process.arguments)

switch result {
case .Success(let box):
  let options = box.value
  if options[opt1] != nil {
    println("\(Process.arguments[2..<Process.arguments.count])")
  }

  if options[opt2] != nil {
    println(parser.helpStringForCommandName("optionTest"))
  }
case .Failure(let err):
  println(err)
}
```

The output would be:

```
~: ./optionTest.swift -e hello
[hello]
~: ./optionTest.swift --echo hello world
[hello, world]
~: ./optionTest.swift -h
usage: optionTest [-e|--echo] [-a|--allow-nothing] [-b|--break-everything]
                  [-c|--counterstrike] [-h|--help]

~: ./optionTest.swift --help
usage: optionTest [-e|--echo] [-a|--allow-nothing] [-b|--break-everything]
                  [-c|--counterstrike] [-h|--help]

~: ./optionTest.swift -d
Invalid option: -d
```

### Installation

Minimum system requirements:

* Xcode 6.1 GM Seed 2
* OS X Mavericks 10.9.5

Steps:

* Clone this github repository, and build the project.
* Run the tests, just for sanity. They should all pass.
* Copy `OptionKit.framework` from the `DerivedData` directoy to `/Library/Frameworks`
  (this will require `sudo` access)

OptionKit should be available for use from a command line, as long as the shebang line
includes `-F /Library/Frameworks`. (This should not be necessary, but currently seems to be.)

### To Do

* Depend on [LlamaKit][]'s Result, rather than on a custom Result object.
* Have a simple way to access the non-option parameters after parsing. Right now there is no way to do that, and it's the main reason the library isn't ready for general usage.
* Add support for sub-parsers.
* Add support for descriptions of options.

[LlamaKit]:https://github.com/LlamaKit/LlamaKit
