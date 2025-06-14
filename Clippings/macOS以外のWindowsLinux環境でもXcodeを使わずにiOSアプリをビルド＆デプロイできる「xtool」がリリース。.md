---
Title: "macOS以外のWindows/Linux環境でもXcodeを使わずにiOSアプリをビルド＆デプロイできる「xtool」がリリース。"
Source: "https://applech2.com/archives/20250526-xtool-cross-platform-xcode-replacement.html"
ClippedDate: 2025-06-08
Tags:
  - "clippings"
---
macOS以外のWindowsやLinuxでもXcodeを使わずにiOSアプリをビルド＆デプロイできる「xtool」がリリースされています。詳細は以下から。

![ターミナルアプリ](https://applech2.com/wp-content/uploads/2025/05/Apple-Terminal-logo-icon.webp)  

　現在、iOSをはじめAppleプラットフォームで動作するアプリはAppleのXcodeやSwift Playgroundアプリを利用して開発する必要がありますが、macOS以外のWindowsやLinuxプラットフォーム環境でもiOSアプリをビルド＆デプロイできるツール **「xtool」** が新たに公開されています。

![xtool](https://applech2.com/wp-content/uploads/2025/05/xtool-for-Windows-and-Linux-Xcode-command-Hero.webp)

> xtool is a cross-platform (Linux/WSL/macOS) tool that replicates Xcode functionality with open standards.
> 
> - ✅ Build a SwiftPM package into an iOS app
> - ✅ Sign and install iOS apps
> - ✅ Interact with Apple Developer Services programmatically
> 
> xtool-org/xtool: Cross-platform Xcode replacement. Build and deploy iOS apps with SwiftPM on Linux, Windows, macOS. – GitHub

　xtoolはWWDC 17 scholarで現在は米Rampのソフトウェアエンジニアを務めるKabir Oberaiさんが、2023年に **「 [Swift SDK Darwin for Linux](https://github.com/kabiroberai/swift-sdk-darwin) 」** として公開したDarwin/Linux向けのSwift SDKをアップデートしたもので、

> Hi folks! A few months ago I shared my Swift SDK for Darwin, which allows you to build iOS Swift Packages on Linux, amongst other things. I mentioned that a lot of work still needed to be done, such as handling codesigning, packaging, and bundling.I’m super excited to share that we’ve finally reached the point where all of these things are now possible with cross-platform, open source software. Enter, xtool!
> 
> Xtool: cross-platform Xcode replacement. Build iOS apps on Linux and more! – Swift Forums

macOSに加えWindowsとLinuxプラットフォームで動作し、 [チュートリアル](https://xtool.sh/tutorials/xtool/first-app/) にある通りxtoolをインストールして”xtool new HogeApp”でアプリを作成、Apple IDでログインし”xtool dev”するだけで、iPhone上にアプリをインストールすることが可能です。

![xtool](https://applech2.com/wp-content/uploads/2025/05/xtool-for-Windows-and-Linux-Xcode-command-features-1.webp)

xtoolでビルド＆デプロイしたHogeAppa

　xtoolはSwiftPMからのiOSアプリのビルドにも対応しており、App Storeへの配信などは行えないものの、macOS+Xcode環境以外のWindows+VS Code環境でもアプリの開発ができるので、気になる方は試してみてください。

![xtool](https://applech2.com/wp-content/uploads/2025/05/xtool-for-Windows-and-Linux-Xcode-command-features-2.webp)

- [Build your first iOS app on Linux / Windows](https://xtool.sh/) — xtool
- [xtool-org/xtool: Cross-platform Xcode replacement. Build and deploy iOS apps with SwiftPM on Linux, Windows, macOS.](https://github.com/xtool-org/xtool) – GitHub