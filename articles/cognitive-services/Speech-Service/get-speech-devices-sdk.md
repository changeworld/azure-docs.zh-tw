---
title: 取得語音裝置 SDK
titleSuffix: Azure Cognitive Services
description: 語音服務可與各種裝置和音訊來源搭配運作。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 在本文中，您將瞭解如何取得語音裝置 SDK 的存取權，並開始進行開發。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 0bc1a7b5e443de0c1a95fa209d2e5a280cf28ef2
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385835"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>取得認知服務語音裝置 SDK

語音裝置 SDK 是一種 pretuned 程式庫，其設計目的是要與特定用途的開發工具組搭配使用，以及改變麥克風陣列設定。

## <a name="choose-a-development-kit"></a>選擇開發工具組

|裝置|規格|描述|案例|
|--|--|--|--|
|[Urbetter 開發工具組](http://www.urbetter.com/products_56/278.html) ![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7個 Mic 陣列、ARM SOC、WIFI、Ethernet、HDMI、USB 攝影機。 <br>Linux|一種業界層級的語音裝置 SDK，可調整 Microsoft Mic 陣列並支援擴充的 i/o，例如 HDMI/Ethernet 和更多 USB 週邊設備 <br> [連絡人 Urbetter](http://www.urbetter.com/products_56/278.html)|對話轉譯、教育、醫院、機器人、OTT Box、語音代理程式、磁片磁碟機|
|[Roobo 智慧型音訊開發工具組](http://ddk.roobo.com)<br>[安裝程式](speech-devices-sdk-roobo-v1.md)  / [快速入門](speech-devices-sdk-android-quickstart.md) ![Roobo 智慧型音訊開發工具組](media/speech-devices-sdk/device-roobo-v1.jpg)|7個 Mic 陣列、ARM SOC、WIFI、音訊輸出、IO。 <br>[Android](speech-devices-sdk-android-quickstart.md)|第一個語音裝置 SDK，用於調整 Microsoft Mic Array 和 front 處理 SDK，以用於開發高品質的轉譯和語音案例|對話轉譯，智慧型說話者，語音代理程式，穿戴式|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[安裝程式](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk)  / [快速入門](speech-devices-sdk-windows-quickstart.md) ![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Mic 陣列 RGB 和深度相機。 <br>[Windows](speech-devices-sdk-windows-quickstart.md) /[Linux](speech-devices-sdk-linux-quickstart.md)|一種開發人員套件，具備先進的人工智慧（AI）感應器，可用於建立精密的電腦視覺和語音模型。 它結合了頂尖的空間麥克風陣列和深度相機與攝影機和方向感應器，全都在一個小型裝置上，具有多個模式、選項和 Sdk，以容納一系列的計算類型。|對話轉譯、機器人、智慧建築|
|Roobo 智慧型音訊開發工具組2<br>[安裝程式](speech-devices-sdk-roobo-v2.md)<br>![Roobo 智慧型音訊開發工具組2](media/speech-devices-sdk/device-roobo-v2.jpg)|7個 Mic 陣列、ARM SOC、WIFI、藍牙、IO。 <br>Linux|第2代語音裝置 SDK，可在符合成本效益的參考設計中提供替代的作業系統和更多功能。|對話轉譯，智慧型說話者，語音代理程式，穿戴式|


## <a name="download-the-speech-devices-sdk"></a>下載語音裝置 SDK

下載[語音裝置 SDK](https://aka.ms/sdsdk-download)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始使用語音裝置 SDK](https://aka.ms/sdsdk-quickstart)
