# Swift Argument Parser
## This Fork

This fork was made to work on any issue(s) to see how to interact with open-source coding.

The issue(s) we will attempt to fix will be here:
- Issue #683 (Link coming soon)

## Issue 683
Issue: "When a property in a ParsableCommand has a custom Decodable implementation, but it is not a Flag, Option or Argument, and the custom decodable implementation uses nestedContainer, the program will crash."

Firstly, is the arg-par is supposed to do such? The official 1.5 release states, "Straightforward, type-safe argument parsing for Swift". "nestedContainer" is a instance method that does take a type and key as a paramter. Since it is a method, it will not pass as a parameter. The issue author did note the expected behavior should be, "[The] property should be ignored", and instead, the program crashes with the error:

```swift
ArgumentParser/ArgumentDecoder.swift:135: Fatal error
[1]    77018 illegal hardware instruction  [executable path] --help
```

Instead of crashing, we want the property to be ignored. I will be checking the file "ParsableArgumentsValidation.swift" to check if there can be a way to have it ignore methods instead of crashing.

## Usage

Begin by declaring a type that defines the information
that you need to collect from the command line.
Decorate each stored property with one of `ArgumentParser`'s property wrappers,
and then declare conformance to `ParsableCommand` and add the `@main` attribute.
(Note, for `async` renditions of `run`, conform to `AsyncParsableCommand` rather
than `ParsableCommand`.) 
Finally, implement your command's logic in the `run()` method.

```swift
import ArgumentParser

@main
struct Repeat: ParsableCommand {
    @Flag(help: "Include a counter with each repetition.")
    var includeCounter = false

    @Option(name: .shortAndLong, help: "The number of times to repeat 'phrase'.")
    var count: Int? = nil

    @Argument(help: "The phrase to repeat.")
    var phrase: String

    mutating func run() throws {
        let repeatCount = count ?? 2

        for i in 1...repeatCount {
            if includeCounter {
                print("\(i): \(phrase)")
            } else {
                print(phrase)
            }
        }
    }
}
```

The `ArgumentParser` library parses the command-line arguments,
instantiates your command type, and then either executes your `run()` method
or exits with a useful message.

`ArgumentParser` uses your properties' names and type information,
along with the details you provide using property wrappers,
to supply useful error messages and detailed help:

```
$ repeat hello --count 3
hello
hello
hello
$ repeat --count 3
Error: Missing expected argument 'phrase'.
Help:  <phrase>  The phrase to repeat.
Usage: repeat [--count <count>] [--include-counter] <phrase>
  See 'repeat --help' for more information.
$ repeat --help
USAGE: repeat [--count <count>] [--include-counter] <phrase>

ARGUMENTS:
  <phrase>                The phrase to repeat.

OPTIONS:
  --include-counter       Include a counter with each repetition.
  -c, --count <count>     The number of times to repeat 'phrase'.
  -h, --help              Show help for this command.
```

## Documentation

For guides, articles, and API documentation see the 
[library's documentation on the Web][docs] or in Xcode.

- [ArgumentParser documentation][docs]
- [Getting Started with ArgumentParser](https://swiftpackageindex.com/apple/swift-argument-parser/documentation/argumentparser/gettingstarted)
- [`ParsableCommand` documentation](https://swiftpackageindex.com/apple/swift-argument-parser/documentation/argumentparser/parsablecommand)
- [`AsyncParsableCommand` documentation](https://swiftpackageindex.com/apple/swift-argument-parser/documentation/argumentparser/asyncparsablecommand)

[docs]: https://swiftpackageindex.com/apple/swift-argument-parser/documentation/argumentparser

#### Examples

This repository includes a few examples of using the library:

- [`repeat`](Examples/repeat/Repeat.swift) is the example shown above.
- [`roll`](Examples/roll/main.swift) is a simple utility implemented as a straight-line script.
- [`math`](Examples/math/Math.swift) is an annotated example of using nested commands and subcommands.
- [`count-lines`](Examples/count-lines/CountLines.swift) uses `async`/`await` code in its implementation.

You can also see examples of `ArgumentParser` adoption among Swift project tools:

- [`swift-format`](https://github.com/apple/swift-format/) uses some advanced features, like custom option values and hidden flags.
- [`swift-package-manager`](https://github.com/apple/swift-package-manager/) includes a deep command hierarchy and extensive use of option groups.

## Project Status

The Swift Argument Parser package is source-stable;
version numbers follow semantic versioning.
Source-breaking changes to public API can only land in a new major version.

The public API of version 1.0.0 of the `swift-argument-parser` package
consists of non-underscored declarations that are marked public in the `ArgumentParser` module.
Interfaces that aren't part of the public API may continue to change in any release,
including the exact wording and formatting of the autogenerated help and error messages,
as well as the package’s examples, tests, utilities, and documentation. 

Future minor versions of the package may introduce changes to these rules as needed.

We want this package to quickly embrace Swift language and toolchain improvements that are relevant to its mandate.
Accordingly, from time to time,
we expect that new versions of this package will require clients to upgrade to a more recent Swift toolchain release.
Requiring a new Swift release will only require a minor version bump.

## Adding `ArgumentParser` as a Dependency

To use the `ArgumentParser` library in a SwiftPM project, 
add it to the dependencies for your package and your command-line executable target:

```swift
let package = Package(
    // name, platforms, products, etc.
    dependencies: [
        // other dependencies
        .package(url: "https://github.com/apple/swift-argument-parser", from: "1.3.0"),
    ],
    targets: [
        .executableTarget(name: "<command-line-tool>", dependencies: [
            // other dependencies
            .product(name: "ArgumentParser", package: "swift-argument-parser"),
        ]),
        // other targets
    ]
)
```

### Supported Versions

The most recent versions of swift-argument-parser support Swift 5.5 and newer. The minimum Swift version supported by swift-argument-parser releases are detailed below:

swift-argument-parser | Minimum Swift Version
----------------------|----------------------
`0.0.1 ..< 0.2.0`     | 5.1
`0.2.0 ..< 1.1.0`     | 5.2
`1.1.0 ..< 1.3.0`     | 5.5
`1.3.0 ...`           | 5.7
