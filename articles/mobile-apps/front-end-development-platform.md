---
title: 選擇使用 Visual Studio 和 Azure 服務來建立用戶端應用程式的前端開發平臺
description: 瞭解支援的原生和跨平臺語言，以建立用戶端應用程式。
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240853"
---
# <a name="choose-mobile-development-frameworks"></a>選擇行動開發架構
開發人員可以使用跨平臺方法的特定架構和模式，透過用戶端技術來建立行動應用程式本身。 根據其決策因素，開發人員可以建立：
- 使用目標 C 和 JAVA 等語言的原生單一平臺應用程式
- 使用 Xamarin、.NET 和 C 的跨平臺應用程式#
- 使用 Cordova 及其變體的混合式應用程式

## <a name="native-platforms"></a>原生平臺
建立原生應用程式需要平臺特定的程式設計語言、Sdk、開發環境，以及 OS 廠商所提供的其他工具。

### <a name="ios"></a>iOS
IOS 是由 Apple 建立和開發，用來建立 Apple 裝置上的應用程式，亦即 iPhone 和 iPad。

- 程式**設計語言**：目標-C、Swift
- **IDE**： Xcode
- **SDK**： iOS SDK

### <a name="android"></a>Android
Android 是由 Google 和全球最熱門的 OS 所設計，用來建立可在各種智慧型手機和平板電腦上執行的應用程式。

- 程式**設計語言**： JAVA、Kotlin 
- **IDE**： Android Studio 和 Android 開發人員工具 
- **SDK**： Android SDK

### <a name="windows"></a>Windows
- 程式**設計語言**： C#
- **IDE**： Visual Studio、Visual Studio Code
- **SDK**： Windows SDK

#### <a name="pros"></a>優點
- 良好的使用者體驗
- 具有高效能的回應式應用程式，以及使用原生程式庫的介面
- 高度安全的應用程式

#### <a name="cons"></a>缺點
- 應用程式只在一個平臺上執行
- 建立應用程式所需的開發人員資源更高且昂貴
- 較低的程式碼重複使用

## <a name="cross-platforms-and-hybrid-applications"></a>跨平臺和混合式應用程式
跨平臺應用程式可讓您在 iOS、Android 和 Windows 上撰寫原生行動應用程式一次、共用程式碼，並加以執行。

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/)是由 Microsoft 所擁有，用來在 c # 中建立強大的跨平臺行動應用程式。 Xamarin 具有可跨許多平臺運作的類別庫和執行時間，例如 iOS、Android 和 Windows。 它也會編譯可提供高效能的原生（非解讀）應用程式。 Xamarin 結合了原生平臺的所有功能，並加入了許多強大的功能。

- 程式**設計語言**： C#
- **IDE**： Windows 或 Mac 上的 Visual Studio

### <a name="react-native"></a>React Native \(英文\)
2015中的 Facebook 發行， [React Native](https://facebook.github.io/react-native/)是[開放原始](https://github.com/facebook/react-native)碼的 JavaScript 架構，用於撰寫適用于 iOS 和 Android 的真實、原生轉譯行動應用程式。 它是根據回應，Facebook 的 JavaScript 程式庫，用於建立使用者介面。 它是以行動平臺為目標，而不是以瀏覽器為目標。 React Native 會使用原生元件，而不是 web 元件做為建立區塊。
 
- 程式**設計語言**： JavaScript
- **IDE**： Visual Studio Code

### <a name="unity"></a>Unity
 Unity 是針對建立遊戲而優化的引擎。 您可以使用它，利用適用于 Windows、iOS、Android 和 Xbox 等平臺的 c #，製作高品質的2D 或3D 應用程式。

### <a name="cordova"></a>Cordova
Cordova 可讓您使用適用于 Apache Cordova 的 Visual Studio Tools Visual Studio Code 或使用適用于 Cordova 的延伸模組來建立混合式應用程式。 透過混合式方法，您可以與網站共用元件，並使用以 Cordova 為基礎的託管 web 應用程式來重複使用網頁伺服器應用程式。

#### <a name="pros"></a>優點
- 為多個平臺建立一個程式碼基底，以提高程式碼的可用性
- 滿足多個平臺之間的更廣大物件
- 大幅減少開發時間
- 易於啟動和更新

#### <a name="cons"></a>缺點
- 效能降低
- 缺乏彈性
- 每個平臺都有一組獨特的特性和功能，可讓原生應用程式更具創意
- 增加 UI 設計階段
- 工具限制
