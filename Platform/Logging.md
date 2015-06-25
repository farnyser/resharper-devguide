---
---

# Logging

The ReSharper Platform provides a comprehensive set of logging features, such as logging levels and different log listeners. It can also be configured with different levels and listeners based on a log category, such as "JetBrains.DataFlow" for capturing log events from the DataFlow subsystem.

There are two ways to enable logging, via the command line and via a config file. The command line options are much simpler, and can only control logging level and enable logging to a file. The config file provides much more power and can configure multiple listeners, as well as different configuration options per category. ReSharper even provides an Options page to configure logging, once [Internal Mode](../Intro/InternalMode.md) is enabled.

## Command line

The command line options are very simple:

* `/LogFile` - enables logging to a file. The path to the file can be specified as an argument. If the file path isn't specified, it defaults to a file created in `%TEMP%\JetLogs` with a filename based on the current date, process name and process ID.
* `/LogLevel {level}` - sets the logging level. If this isn't specified, the default is `LoggingLevel.INFO` (see [below](#logging-levels) for more details on logging levels). Using this command line arg automatically enables logging. If `/LogFile` isn't specified, the default filename is used.
* `/Log` simply enables logging with the default values. This switch isn't available in ReSharper ([RSRP-430339](https://youtrack.jetbrains.com/issue/RSRP-430339)).

> **NOTE** As ReSharper is hosted in Visual Studio, it needs to prefix the command line args to prevent clashes with options for other Visual Studio extensions, or Visual Studio itself. This means the command line args are `/ReSharper.LogFile` and `/ReSharper.LogLevel`.
>
> Similarly, command line switches need to be registered with Visual Studio when the extension is installed. Due to an oversight, the `/ReSharper.Log` switch isn't registered, and so isn't available in ReSharper. The switch simply enables logging with the defaults, so specifying either of the other switches will also enable logging.

## Configuration file

Logging can also be enabled by creating a config file, which allows for much more powerful configuration. The config file can enable or disable individual categories, and even route log messages from different categories to different files. Configuration files are described in the [Advanced Configuration section](Logging/AdvancedConfiguration.md).

## Logging levels

Logging levels are used by both the config file and the command line, and are defined in the following enum: 

```csharp
public enum LoggingLevel
{
  OFF = 0,
  FATAL = 1,
  ERROR = 2,
  WARN = 3,
  INFO = 4,
  VERBOSE = 5,
  TRACE = 6
}
```

When logging is enabled, the default/recommended level is `INFO`, which is intended for normal event logging. The `VERBOSE` level is intended to capture debug information, and `TRACE` is for capturing events that are even more verbose than `VERBOSE` - such as call stacks, etc.

By specifying a logging level, you will get all log events at that level and below. So, `INFO` will also get the log events for `WARN`, `ERROR` and `FATAL`.

## Logging options page

The simplest way to create a file is to use the Logging options page when running in [Internal Mode](../Intro/InternalMode.md). This page shows the current configuration of logging, based on the command line args, the currently created and active log files, and what appenders/listeners and loggers are active.

<!-- TODO: Add screenshot -->

From this page, it is possible to create a log file, by clicking the "Create File" link next to the Common or Hive file details. Clicking "Create File" for Common will create the `LogConfiguration.xml` file, while clicking "Create File" for Hive will create the `LogConfiguration.Debug.xml` file. The generated file contains a default setting for `VERBOSE` logging to the `DebugOutputLogEventListener`, which outputs to an attached debugger. It also provides commented-out example syntax to configure file appenders and category-based logging.

The page also shows the current active configuration, including currently active logging command line args, listed appenders and which appender is applied to what category, at what level. It's a very handy at-a-glance view of how the logging is configured. It also includes a "Log Log" window, which the logger uses to log internal events from the logging system (to prevent recursion or loss of information when errors occur while logging).

## System debug and trace

The ReSharper Platform will add a `TraceListener` to route .net generated events through ReSharper's logging system, so it will be possible to capture log events generated by `System.Diagnostics.Debug` and `System.Diagnostics.Trace`.