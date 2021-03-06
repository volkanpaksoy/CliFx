### v1.6 (06-Dec-2020)

- Added support for custom value validators. You can now create a type that inherits from `CliFx.ArgumentValueValidator<T>` to implement reusable validation logic for command arguments. To use a validator, include it in the `Validators` property on the `CommandOption` or `CommandParameter` attribute. (Thanks [@Oleksandr Shustov](https://github.com/AlexandrShustov))
- Added `CliFx.ArgumentValueConverter<T>` class that you can inherit from to implement custom value converters. `CliFx.IArgumentValueConverter` interface is still available, but it is recommended to inherit from the generic class instead, due to the type safety it provides. The interface may become internal or get removed in one of the future major versions.
- Updated requirements for option names and short names: short names now must be letter characters (lowercase or uppercase), while names must now start with a letter character. This means option names can no longer start with a digit or a special character. This change makes it possible to pass negative number values without the need to quote them, i.e. `--my-number -5`.

### v1.5 (23-Oct-2020)

- Added pretty-printing for unhandled exceptions thrown from within the application. This makes the errors easier to parse visually and should help in troubleshooting. This change does not affect `CommandException`, as it already has special treatment. (Thanks [@Mårten Åsberg](https://github.com/89netraM))
- Added support for custom value converters. You can now create a type that implements `CliFx.IArgumentValueConverter` and specify it as a converter for your parameters or options via the `Converter` named property. This should enable conversion between raw argument values and custom types which are not string-initializable. (Thanks [@Oleksandr Shustov](https://github.com/AlexandrShustov))
- Improved help text so that it also shows minimal usage examples for child and descendant commands, besides the actual command it was requested on. This should improve user experience for applications with many nested commands. (Thanks [@Nikiforov Alexey](https://github.com/NikiforovAll))

### v1.4 (20-Aug-2020)

- Added `VirtualConsole.CreateBuffered()` method to simplify test setup when using in-memory backing stores for output and error streams. Please refer to the readme for updated recommendations on how to test applications built with CliFx.
- Added generic `CliApplicationBuilder.AddCommand<TCommand>()`. This overload simplifies adding commands one-by-one as it also checks that the type implements `ICommand`.

### v1.3.2 (31-Jul-2020)

- Fixed an issue where a command was incorrectly allowed to execute when the user did not specify any value for a non-scalar parameter. Since they are always required, a parameter needs to be bound to (at least) one value. (Thanks [@Daniel Hix](https://github.com/ADustyOldMuffin))
- Fixed an issue where `CliApplication.RunAsync(...)` threw `ArgumentException` if there were two environment variables, whose names differed only in case. Environment variable names are now treated case-sensitively. (Thanks [@Ron Myers](https://github.com/ron-myers))

### v1.3.1 (19-Jul-2020)

- Running the application with the debug directive (`myapp [debug]`) will now also try to launch a debugger instance. In most cases it will save time as you won't need to attach the debugger manually. (Thanks [@Volodymyr Shkolka](https://github.com/BlackGad))
- Fixed an issue where unhandled generic exceptions (i.e. not `CommandException`) sometimes caused the application to incorrectly return successful exit code due to an overflow issue on Unix systems. Starting from this version, all unhandled generic exceptions will produce `1` as the exit code when thrown. Instances of `CommandException` can still be configured to return any specified exit code, but it's recommended to constrain the values between `1` and `255` to avoid overflow issues. (Thanks [@Ihor Nechyporuk](https://github.com/inech))

### v1.3 (23-May-2020)

- Changed analyzers to report errors instead of warnings. If you find that some analyzer works incorrectly, please report it on GitHub. You can also configure inspection severity overrides in your project if you need to.
- Improved help text by showing default values for non-required options. This only works on types that have a custom override for `ToString()` method. Additionally, if the type implements `IFormattable`, the overload with a format provider will be used instead. (Thanks [@Domn Werner](https://github.com/domn1995))
- Changed default version text to only show 3 version components instead of 4, if the last component (revision) is not specified or is zero. This makes the default version text compliant with semantic versioning.
- Fixed an issue where it was possible to define a command with an option that has the same name or short name as built-in help or version options. Previously it would lead to the user-defined option being ignored in favor of the built-in option. Now this will throw an exception instead.
- Changed the underlying representation of `StreamReader`/`StreamWriter` objects used in `SystemConsole` and `VirtualConsole` to be thread-safe.

### v1.2 (11-May-2020)

- Added built-in Roslyn analyzers that help catch incorrect usage of the library. Currently, all analyzers report issues as warnings so as to not prevent the project from building. In the future that may change.
- Added an optional parameter to `new CommandException(...)` called `showHelp` which can be used to instruct CliFx to show help for the current command after printing the error. (Thanks [@Domn Werner](https://github.com/domn1995))
- Improved help text shown for enum options and parameters by providing the list of valid values that the enum can accept. (Thanks [@Domn Werner](https://github.com/domn1995))
- Fixed an issue where it was possible to set an option without providing a value, while the option was marked as required.
- Fixed an issue where it was possible to configure an option with an empty name or a name consisting of a single character. If you want to use a single character as a name, you should set the option's short name instead.
- Added `CursorLeft` and `CursorTop` properties to `IConsole` and its implementations. In `VirtualConsole`, these are just auto-properties.
- Improved exception messages.
- Improved exceptions related to user input by also showing help text after the error message. (Thanks [@Domn Werner](https://github.com/domn1995))

### v1.1 (16-Mar-2020)

- Changed `IConsole` interface (and as a result, `SystemConsole` and `VirtualConsole`) to support writing binary data. Instead of `TextReader`/`TextWriter` instances, the streams are now exposed as `StreamReader`/`StreamWriter` which provide the `BaseStream` property that allows raw access. Existing usages inside commands should remain the same because `StreamReader`/`StreamWriter` are compatible with their base classes `TextReader`/`TextWriter`, but if you were using `VirtualConsole` in tests, you may have to update it to the new API. Refer to the readme for more info.
- Changed argument binding behavior so that an error is produced if the user provides an argument that doesn't match with any parameter or option. This is done in order to improve user experience, as otherwise the user may make a typo without knowing that their input wasn't taken into account.
- Changed argument binding behavior so that options can be set to multiple argument values while specifying them with mixed naming. For example, `--option value1 -o value2 --option value3` would result in the option being set to corresponding three values, assuming `--option` and `-o` match with the same option.
