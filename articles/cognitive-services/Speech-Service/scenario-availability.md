---
title: 案例可用性-語音服務
titleSuffix: Azure Cognitive Services
description: 語音 SDK 具有各種不同程式設計語言和環境的多種案例。 並非所有的案例都適用于所有的程式設計語言或所有環境。 以下列出每個案例的可用性。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400151"
---
# <a name="scenario-availability"></a>案例可用性

語音 SDK 具有各種不同程式設計語言和環境的多種案例。 並非所有的案例都適用于所有的程式設計語言或所有環境。 以下列出每個案例的可用性。

- **語音辨識（SR）、片語清單、意圖、翻譯和內部部署容器**
  - C + +/Windows & Linux & macOS
  - C # （Framework & .NET Core）/Windows & UWP & Unity & Xamarin & Linux & macOS
  - JAVA （Jre 和 Android）
  - JavaScript （瀏覽器和 NodeJS）
  - Python
  - Swift
  - Objective-C  
- **文字轉換語音（TTS）**
  - C + +/Windows & Linux
  - & UWP & Unity 的 c #/Windows
  - JAVA （Jre 和 Android）
  - Python
  - Swift
  - Objective-C
  - TTS REST API 可以在其他所有情況下使用。
- **關鍵字找出（KWS.TABLE）**
  - C + +/Windows & Linux
  - C #/Windows & Linux
  - Python/Windows & Linux
  - JAVA/Windows & Linux & Android （語音裝置 SDK）
  - 關鍵字找出（KWS.TABLE）功能可能適用于任何麥克風類型，但官方 KWS.TABLE 支援目前僅限於在 Azure Kinect DK 硬體或語音裝置 SDK 中找到的麥克風陣列
- **語音助理**
  - C + +/Windows & Linux & macOS
  - C#/Windows
  - JAVA/Windows & Linux & macOS & Android （語音裝置 SDK）
- **對話轉譯**
  - C + +/Windows & Linux
  - C # （Framework & .NET Core）/Windows & UWP & Linux
  - JAVA/Windows & Linux & Android （語音裝置 SDK）
- **多裝置交談**
  - C + +/Windows
  - C # （Framework & .NET Core）/Windows
- **撥打電話中心轉譯**
  - REST API 和可以在任何情況下使用
- **編解碼器壓縮的音訊輸入**
  - C + +/Linux
  - C #/Linux
  - JAVA/Linux、Android 和 iOS
