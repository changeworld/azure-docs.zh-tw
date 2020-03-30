---
title: 選擇前端開發平臺，使用 Visual Studio 和 Azure 服務構建用戶端應用程式
description: 瞭解支援本機和跨平臺語言以構建用戶端應用程式。
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240853"
---
# <a name="choose-mobile-development-frameworks"></a>選擇移動開發框架
開發人員可以使用用戶端技術來構建移動應用程式，方法是使用跨平臺方法的特定框架和模式。 根據他們的決策因素，開發人員可以構建：
- 通過使用目標 C 和 JAVA 等語言實現本機單平臺應用程式
- 使用 Xamarin、.NET 和 C 的跨平臺應用程式#
- 使用科爾多瓦及其變體的混合應用

## <a name="native-platforms"></a>本機平臺
構建本機應用程式需要特定于平臺的程式設計語言、SDK、開發環境以及作業系統供應商提供的其他工具。

### <a name="ios"></a>iOS
iOS由蘋果創建和開發，用於在蘋果設備上構建應用程式，即iPhone和iPad。

- **程式設計語言**： 目標 C， Swift
- **IDE**： Xcode
- **SDK**： iOS SDK

### <a name="android"></a>Android
Android由谷歌和世界上最流行的作業系統設計，用於構建可在一系列智慧手機和平板電腦上運行的應用程式。

- **程式設計語言**： JAVA， 科特林 
- **IDE**： Android 工作室和 Android 開發人員工具 
- **SDK**： 安卓 SDK

### <a name="windows"></a>Windows
- **程式設計語言**： C#
- **IDE**： 視覺工作室， 視覺工作室代碼
- **SDK**： Windows SDK

#### <a name="pros"></a>優點
- 良好的使用者體驗
- 具有高性能且能夠與本機庫介面的回應式應用程式
- 高度安全的應用程式

#### <a name="cons"></a>缺點
- 應用程式僅在一個平臺上運行
- 構建應用程式佔用更多開發人員資源且成本高昂
- 降低代碼重用率

## <a name="cross-platforms-and-hybrid-applications"></a>跨平臺和混合應用
跨平臺應用程式使您能夠編寫本機移動應用程式一次，共用代碼，並在 iOS、Android 和 Windows 上運行它們。

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/)由 Microsoft 擁有，用於在 C# 中構建強大的跨平臺移動應用程式。 Xamarin 具有一個類庫和運行時，可跨許多平臺（如 iOS、Android 和 Windows）工作。 它還編譯提供高性能的本機（非解釋）應用程式。 Xamarin 結合了本機平臺的所有功能，並增加了其自身的許多強大功能。

- **程式設計語言**： C#
- **IDE**： 視窗或 Mac 上的視覺化工作室

### <a name="react-native"></a>React Native \(英文\)
由 Facebook 于 2015 年發佈[，React Native](https://facebook.github.io/react-native/)是一個[開源](https://github.com/facebook/react-native)JavaScript 框架，用於編寫適用于 iOS 和 Android 的真實本地呈現移動應用程式。 它基於 React，Facebook 的 JavaScript 庫，用於構建使用者介面。 它不是針對瀏覽器，而是針對移動平臺。 React 本機使用本機組件而不是 Web 元件作為構建基塊。
 
- **程式設計語言**：JavaScript
- **IDE**： 視覺工作室代碼

### <a name="unity"></a>Unity
 Unity 是專為創建遊戲而優化的引擎。 您可以使用它為 Windows、iOS、Android 和 Xbox 等平臺使用 C# 製作高品質的 2D 或 3D 應用程式。

### <a name="cordova"></a>Cordova
Cordova 允許您使用適用于 Apache Cordova 的視覺工作室工具或帶有科爾多瓦擴展功能的視覺工作室代碼構建混合應用程式。 使用混合方法，您可以與網站共用元件，並將基於 Cordova 的託管 Web 應用程式方法重用基於 Web 服務器的應用程式。

#### <a name="pros"></a>優點
- 通過為多個平臺創建一個代碼庫，提高代碼可用性
- 跨多個平臺面向更廣泛的受眾
- 顯著縮短開發時間
- 易於啟動和更新

#### <a name="cons"></a>缺點
- 性能更低
- 缺乏靈活性
- 每個平臺都有一組獨特的特性和功能，使本機應用程式更具創意
- 增加 UI 設計階段
- 工具限制
