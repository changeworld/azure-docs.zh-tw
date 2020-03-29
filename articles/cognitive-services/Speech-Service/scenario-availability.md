---
title: 方案可用性 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音 SDK 具有跨各種程式設計語言和環境的許多方案。 並非所有方案都適用于所有程式設計語言或所有環境。 下面列出了每種方案的可用性。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: 6f8f892e7ca81881b0cc00e1708e3f05052c573c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76264198"
---
# <a name="scenario-availability"></a>方案可用性

語音 SDK 具有跨各種程式設計語言和環境的許多方案。 並非所有方案都適用于所有程式設計語言或所有環境。 下面列出了每種方案的可用性。

- **語音辨識 （SR）、片語清單、意圖、翻譯和本地容器**
  - 所有有箭頭連結的程式設計語言/環境 <img src="media/index/link.jpg" height="15" width="15"></img> [在快速](https://aka.ms/csspeech)入門表中。
- **文本到語音 （TTS）**
  - C++/視窗&Linux
  - C#/視窗& UWP &統一
  - JAVA（Jre 和安卓）
  - Python
  - Swift
  - Objective-C
  - TTS REST API 可用於其他所有情況。
- **關鍵字識別 （KWS）**
  - C++/視窗&Linux
  - C#/視窗&Linux
  - Python/視窗&Linux
  - JAVA/Windows&Linux&安卓（語音設備SDK）
  - 關鍵字發現 （KWS） 功能可能適用于任何麥克風類型，但官方 KWS 支援目前僅限於 Azure Kinect DK 硬體或語音設備 SDK 中的麥克風陣列
- **語音助理**
  - C++/視窗&linux&macOS
  - C#/Windows
  - JAVA/Windows&Linux& macOS & Android（語音設備 SDK）
- **對話轉譯**
  - C++/視窗&Linux
  - C# （框架& .NET 核心）/Windows & UWP & Linux
  - JAVA/Windows&Linux&安卓（語音設備SDK）
- **多裝置交談**
  - C++/窗戶
  - C# （框架& .NET 核心）/視窗
- **話務中心轉錄**
  - REST API，可在任何情況下使用
- **編解碼器壓縮音訊輸入**
  - C++/Linux
  - C#/Linux
  - JAVA/Linux、安卓和iOS
