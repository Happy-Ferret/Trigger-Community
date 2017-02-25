# Trigger Community
Repository of custom commands shared by users of the Trigger browser extension. Please do not manualy submit pull requests. Please use Trigger dashboard to create and submit user scripts.

# API
Here is the documentation on how to write custom commands (user scripts) for Trigger.

| Interfaces              |
|:------------------------|
| **[browser](#browser)** |
| **[system](#system)**   |

## browser
The complete `browser.*` interface of WebExtensions is available. You can read about the details here at [MDN :: WebExtensions - APIs](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API).

There is two critical difference.

1. Callbacks cannot be used.
2. All methods return a promise, even if they MDN documentation says they do not.

For example, in the Trigger localization file I have a string called `error`. Normally you would obtain this string by doing `let str_error = browser.i18n.getMessage('error')`, becuase MDN docs says this returns a string synchronously. However we are in the Trigger user script scope, so it returns a promise. Here are some ways to do this:

  > *ES5*
  > ```
  > browser.i18n.getMessage('error').then(function(result) {
  >     let str_error = result;
  > });
  > ```
  >
  > *ES7*
  > ```
  > let str_error = await browser.i18n.getMessage('error');
  > ```

## system
This branch offers a collection of modules similar to the `browser` API. The functionality here is focused on operating system specifics.

| Modules                         |
|:--------------------------------|
| **[file](#systemfile)**         |
| **[keyboard](#systemkeyboard)** |
| **[path](#systempath)**         |

### system.file
This module enables manipulating files on the system.

| Methods                                                                 |
|:------------------------------------------------------------------------|
| **[launch](#launch)** - `promise<> system.file.launch(string path)` |

#### Methods
##### launch
`promise<> system.file.launch(string path)`

Tries to launch the file at `path`. If the file is directory, it will open it in the platform file viewer. If the file is a special type of file (text, image, etc) it will open it with the default application for that file type. If the file is a program, it will launch it detached. The promise is resolved with no arguments after the attempt to launch is made. If the attempt to launch fails for any reason, it is still not resolved with any arguments.

### system.keyboard
This module works with the keyboard on a platform level.

| Methods                                                                        |
|:-------------------------------------------------------------------------------|
| **[sendKey](#sendkey)** - `promise<> system.keyboard.sendKey(string code)` |


#### Methods
##### sendKey
`promise<> system.keyboard.sendKey(string keyname)`

This sends a synthesized key to whatever is currently focused. It returns a promise which resolves with no arguments after the key was sent. If the key fails to send it is silent, meaning there are still no arguments passed to the resolving callback. The argument `keyname` is a string and is platform dependent. For instance the `keyname` for the "A" key on Windows is "VK_A", on Linux it is "XK_A", and on Mac it is "kVK_ANSI_A". You can find a list of these codes at:

* Windows - [MSDN :: Virtual Key Codes](https://msdn.microsoft.com/en-us/library/windows/desktop/dd375731(v=vs.85).aspx)
* Linux - [Github :: Noitidart / ostypes - ostypes_x11.jsm L1128-L1578](https://github.com/Noitidart/ostypes/blob/master/ostypes_x11.jsm#L1128-L1578)
* Mac - [Github :: Noitidart / ostypes - ostypes_mac.jsm L630-L747](https://github.com/Noitidart/ostypes/blob/master/ostypes_mac.jsm#L630-L747)

You can optionally set code to a string of the number surrounded by single quotes. For example, the code "XK_A" corresponds to a number value of 65 - so calling `system.file.sendKey("XK_A")` is the same as calling `system.keyboard.sendKey("'65'")`

There are future plans to make the keys cross platform, and the paltform dependent keys will maintain the platform prefix.

### system.path
This module provides information and methods on the system file paths.

| Types                     |
|:--------------------------|
| **[PathName](#pathname)** |

| Methods                                                                 |
|:------------------------------------------------------------------------|
| **[get](#get)** - `promise<string path> system.path.get(PathName name)` |


#### Types
##### PathName
The first argument passed to `getPath`. Identifies that you want special path on the system.

| Enum                                                        |
|:------------------------------------------------------------|
| "desktop", "temp", "home", "user_app_data", "program_files" |

* *Windows Only* - "user_app_data", "program_files"
* *Linux Only* - "home"

#### Methods
##### get
`promise<string path> system.path.get(PathName name)`

Returns a promise which resolves with the first argument `path` being a string of the special system path associated with the `name`. If `name` is not valid then it resolves with a `path` being a blank string.
