---
title: 專案可用性 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音 SDK 具有跨各種程式設計語言和環境的許多方案。 並非所有方案都適用於所有程式設計語言或所有環境。 下面列出了每種方案的可用性。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400151"
---
# <a name="scenario-availability"></a>專案可用性

語音 SDK 具有跨各種程式設計語言和環境的許多方案。 並非所有方案都適用於所有程式設計語言或所有環境。 下面列出了每種方案的可用性。

- **語音辨識 (SR)、短語清單、意圖、翻譯和本地容器**
  - C++/視窗&linux&macOS
  - C# (框架& .NET 核心)/Windows & UWP &统一& Xamarin & Linux & macOS
  - Java(Jre 和安卓)
  - JavaScript (布羅伯和節點JS)
  - Python
  - Swift
  - Objective-C  
- **文字到語音 (TTS)**
  - C++/視窗&Linux
  - C#/視窗& UWP &统一
  - Java(Jre 和安卓)
  - Python
  - Swift
  - Objective-C
  - TTS REST API 可用於其他所有情況。
- **關鍵字識別 (KWS)**
  - C++/視窗&Linux
  - C#/視窗&Linux
  - Python/視窗&Linux
  - Java/Windows&Linux&安卓(語音設備SDK)
  - 關鍵字發現 (KWS) 功能可能適用於任何麥克風類型,但官方 KWS 支援目前僅限於 Azure Kinect DK 硬體或語音裝置 SDK 中的麥克風陣列
- **語音助理**
  - C++/視窗&linux&macOS
  - C#/Windows
  - Java/Windows&Linux& macOS & Android(語音裝置 SDK)
- **對話轉譯**
  - C++/視窗&Linux
  - C# (框架& .NET 核心)/Windows & UWP & Linux
  - Java/Windows&Linux&安卓(語音設備SDK)
- **多裝置交談**
  - C++/窗戶
  - C# (框架& .NET 核心)/視窗
- **通話轉錄**
  - REST API,可在任何情況下使用
- **編解碼器壓縮音訊輸入**
  - C++/Linux
  - C#/Linux
  - Java/Linux、安卓和iOS
