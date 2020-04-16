---
title: 取得語音裝置 SDK
titleSuffix: Azure Cognitive Services
description: 語音服務適用於各種設備和音訊源。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 在本文中,您將學習如何存取語音裝置 SDK 並開始開發。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 756ec976c4643c1cd80552b7fa552b70fd1f5d24
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391261"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>取得認知服務語音裝置 SDK

語音裝置 SDK 是一個預調諧的庫,旨在使用專用的開發套件和不同的麥克風陣列配置。

## <a name="choose-a-development-kit"></a>選擇開發工具套件

|裝置|規格|描述|案例|
|--|--|--|--|
|[URbetter T11 開發委員會](https://www.alibaba.com/product-detail/URBETTER-Dev-Kits-for-Microsoft-Speech_62358746402.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 麥克風陣列,ARM SOC,WIFI,乙太網,HDMI,USB攝像機。 <br>Linux|行業級語音裝置 SDK,可調整 Microsoft 麥克風陣列並支援擴展 I/O,如 HDMI/乙太網和更多 USB 外圍設備|對話轉錄, 教育, 醫院, 機器人, OTT 盒, 語音代理, 驅動器 Thru|
|[羅博智慧音訊開發套件](https://ddk.roobo.com)<br>[設定](speech-devices-sdk-roobo-v1.md) / [快速入門](speech-devices-sdk-android-quickstart.md)![羅博智慧音訊開發套件](media/speech-devices-sdk/device-roobo-v1.jpg)|7 麥克風陣列,ARM SOC,WIFI,音訊出,IO。 <br>[Android](speech-devices-sdk-android-quickstart.md)|首款用於調整 Microsoft Mic 陣列和前端處理 SDK 的語音裝置 SDK,用於開發高品質的轉錄和語音場景|對話轉錄, 智慧揚聲器, 語音代理, 可穿戴|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[設定](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [快速入門](speech-devices-sdk-windows-quickstart.md)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Mic 陣列 RGB 和深度攝像機。 <br>[視窗](speech-devices-sdk-windows-quickstart.md)[Linux](speech-devices-sdk-linux-quickstart.md) /|一個帶有進階人工智慧 (AI) 感測器的開發人員套件,用於建構複雜的電腦視覺和語音模型。 它將一流的空間麥克風陣列和深度攝像機與攝像機和方向感測器相結合,所有設備都位於一個小型設備中,具有多種模式、選項和 SDK,以適應各種計算類型。|對話轉錄,機器人,智慧建築|
|羅博智慧音訊開發套件 2<br>[安裝](speech-devices-sdk-roobo-v2.md)<br>![羅博智慧音訊開發套件 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 麥克風陣列,ARM SOC,WIFI,藍牙,IO。 <br>Linux|第二代語音設備 SDK,在經濟高效的參考設計中提供替代操作系統和更多功能。|對話轉錄, 智慧揚聲器, 語音代理, 可穿戴|


## <a name="download-the-speech-devices-sdk"></a>下載語音裝置 SDK

下載[語音裝置 SDK](https://aka.ms/sdsdk-download)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始使用語音裝置 SDK](https://aka.ms/sdsdk-quickstart)
