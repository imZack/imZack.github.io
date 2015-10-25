title: Xcode 更新後不能編譯
date: 2014-09-28 20:28:14
tags:
- Xcode
- MAC OS
- GCC
---

前幾天在編譯程式的時候忽然出現...

**Agreeing to the Xcode/iOS license requires admin privileges, please re-run as root via sudo**

我想說慘了，我的 Xcode 該不會壞掉了吧...[上網一查](http://stackoverflow.com/questions/26197347/agreeing-to-the-xcode-ios-license-requires-admin-privileges-please-re-run-as-r)原來是升級之後還沒同意 **License**

直接打開來按一下同意就可以！

command line 可以下 `sudo xcodebuild -license`
