---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 07/31/2020
ms.author: rgarcia
ms.openlocfilehash: 310c0f547ee11a3243589c364755a30a84be1a25
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810157"
---
## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，請確定您具有下列項目︰

* 已完整閱讀 [Azure Spatial Anchors 概觀](../articles/spatial-anchors/overview.md)。
* 已完成其中一個 [5 分鐘快速入門](../articles/spatial-anchors/index.yml)。
* 已基本了解 C# 和 Unity。
* 已基本了解 <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> (如果您想要使用 Android) 或 <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> (如果您想要使用 iOS)。
* 已安裝具備 [ASP.NET 與網頁程式開發]  工作負載的 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> 或更新版本的 Windows 電腦。
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)。
* 用來部署並執行應用程式的一或多個裝置 (iOS 或 Android)。
  * 如果使用 Android，您必須：
    * 在 Windows 電腦上安裝 <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3</a> \(英文\) 或更新版本、<a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a> \(英文\)，以及 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> \(英文\)。
    * 擁有<a href="https://developer.android.com/studio/debug/dev-options" target="_blank">已啟用開發人員功能</a> \(英文\) 且<a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">具備 ARCore 功能</a> \(英文\) 的 Android 裝置。
  * 如果使用 iOS，您必須：
    * 已安裝 <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> \(英文\) 或更新版本、<a href="https://cocoapods.org" target="_blank">CocoaPods</a> \(英文\)，以及 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a> \(英文\) 的 macOS 電腦。
    * 已啟用開發人員功能且<a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">與 ARKit 相容</a> \(英文\) 的 iOS 裝置。
    * 透過 Homebrew 安裝的 Git。 在終端機中於單一行上輸入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 然後執行 `brew install git`。
