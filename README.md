# MarkdownAttributedString

Adding Markdown support to NSAttributedString.

## Why?

Attributed strings, along with CoreText and UI frameworks for iOS and macOS, are a powerful component used in every app.

Yet making those attributed strings is a pain in the butt. Your choices are:

* Write a bunch of code to do it manually.
* Use an external text editor to create a resource which is hard to manage (especially with localization).

Additionally, with the advent of system fonts that can't be used in TextEdit to make the RTF file, there's no avoiding the need for code when you're dealing with text that needs to match the surrounding user interface.

## What?

This project is an Objective-C category that generates rich text by reading Markdown as the source code. It also allows you to write Markdown using attributed strings.

Objective-C was chosen for this project because it is extending code written in the same language. It was a more natural fit.

Swift is fully supported, both on iOS and macOS. You'll find sample apps in both languages and for all Apple platforms including macOS (AppKit) and iOS (UIKit).

The macOS sample app also includes a full suite of tests that check the Markdown parser (for both reading and writing). If you want to add additional Markdown support in this code, familiarize yourself with these tests first.

## How?

[Markdown](https://daringfireball.net/projects/markdown/syntax) is a "visual specification" and I was warned by its author that doing this kind of rich text conversion would be "hairy". It was.

But the end result is worth it. It's incredibly handy to have Markdown as a tool while you're developing apps that use Apple's frameworks. I have used the code for buttons, labels, and text views; the sample apps included in this repository show you how that's done. Here's a quick taste of how you do it in Swift:

```swift
let markdownString = "This is a **_simple_ example** that _shows_ **Markdown** being used for attributed strings."

myLabel.attributedText = NSAttributedString(markdownRepresentation: markdownString, attributes: [.font : UIFont.systemFont(ofSize: 17.0), .foregroundColor: UIColor.systemPurple ])
```

If you've used HTMLto do this in the past, you'll know that it pulls in WebKit, is not particularly fast, and has [thread-safety issues](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/AttributedStrings/Tasks/CreatingAttributedStrings.html). This new approach using Markdown has no external dependencies and can be used off the main thread. The only restriction is that your code can only run on [a single thread at one time](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/ThreadSafetySummary/ThreadSafetySummary.html) because it mutates foundation objects. The code is fast and typically used for operations that are not performed repeatedly, so it's unlikely that you'll have any issues.

Both producing and consuming Markdown involves a lot of text scanning. When going from Markdown to an attributed string, each type of marker (e.g. \*\* or \_ ) is checked and styling is added incrementally to the string. Going the other direction, each attribute range is checked and markers are emitted accordingly. Of course, once you get into the details of the implementation, you'll realize that it gets more complicated than you'd first expect. The only regular expressions used are to detect URLs and email addresses. So yeah, "hairy".

One of the potential uses I see for this code is with localization. Putting Markdown into your .strings files will be a lot easier than juggling separate RTF files. Be careful about styles that are only available in Latin languages. A good example is the Japanese phrase これはテストです which can be rendered with a bold font (**これはテストです**) variant, but not italic (_これはテストです_). In these cases, you're better off using  `styleAttributes` to add an underline or some other visual emphasis that doesn't rely on the glyph's structure.

## Where?

[Tot](http://tot.rocks).

This app was the first place I used this code and when you start using it, you'll understand why. Working in both rich text and Markdown is a seamless experience. As folks have been using this app over the past few months, many weird edge cases have emerged: the parsing and generation of Markdown is robust as a result.

## Who?

This code was written by Craig Hockenberry. If you'd like to show your appreciation, there are several ways to do that:

* [Get Tot!](http://tot.rocks) - if you love Markdown, you'll love the app that inspired this code. It's free on macOS and a one-time purchase on iOS.
* [Support our Patreon](https://patreon.com/iconfactory) - you'll be supporting a good cause and get tons of cool stuff in return.
* [Buy our apps](https://iconfactoryapps.com) - the Iconfactory has been making software for over twenty years and we're sure to have something that will appeal to you.

As with any software, there is plenty of room for improvement. Feel free to send pull requests and file issues. I'm likely to ignore any issues without test cases.

## License

This code uses a MIT License:

Copyright (c) 2020 The Iconfactory, Inc. <https://iconfactory.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.