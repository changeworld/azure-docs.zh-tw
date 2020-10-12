---
title: 如何設定麥克風陣列-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何設定麥克風陣列，讓語音裝置 SDK 可以使用它。
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: a2652bed6c8e7dec0a6fe8f9471793c3873646bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82781774"
---
# <a name="how-to-configure-a-microphone-array"></a>如何設定麥克風陣列

在本文中，您將瞭解如何設定 [麥克風陣列](https://aka.ms/sdsdk-microphone)。 它包括設定工作角度，以及如何選取要用於語音裝置 SDK 的麥克風。

語音裝置 SDK 最適合與已根據 [我們的指導方針](https://aka.ms/sdsdk-microphone)設計的麥克風陣列搭配使用。 麥克風陣列設定可以由作業系統提供，或透過下列其中一種方式提供。

語音裝置 SDK 一開始會支援麥克風陣列，方法是從一組固定的設定中進行選取。

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

在 Windows 上，麥克風陣列設定是由音訊驅動程式所提供。

從 v 1.11.0，語音裝置 SDK 也支援 [JSON](https://aka.ms/sdsdk-micarray-json)檔案的設定。


## <a name="windows"></a>Windows
在 Windows 上，麥克風陣列幾何資訊會自動從音訊驅動程式取得。 因此，屬性 `DeviceGeometry` 、  `SelectedGeometry` 和 `MicArrayGeometryConfigFile` 都是選擇性的。 我們會使用提供的 [JSON](https://aka.ms/sdsdk-micarray-json) 檔案 `MicArrayGeometryConfigFile` ，僅取得波束成形範圍。

如果使用指定麥克風陣列 `AudioConfig::FromMicrophoneInput` ，則會使用指定的麥克風。 如果未指定或 `AudioConfig::FromDefaultMicrophoneInput` 呼叫麥克風，則會使用預設的麥克風（在 Windows 上的 [音效設定] 中指定）。
語音裝置 SDK 中的 Microsoft 音訊堆疊僅支援以 16 KHz 的整數倍數進行取樣率的取樣率。

## <a name="linux"></a>Linux
在 Linux 上，必須提供麥克風幾何資訊。 使用 `DeviceGeometry` 並 `SelectedGeometry` 保持支援。 您也可以使用屬性，透過 JSON 檔案來提供 `MicArrayGeometryConfigFile` 。 類似于 Windows，JSON 檔案可以提供波束成形範圍。

如果使用指定麥克風陣列 `AudioConfig::FromMicrophoneInput` ，則會使用指定的麥克風。 如果未指定或 `AudioConfig::FromDefaultMicrophoneInput` 呼叫麥克風，則我們會從名為 *DEFAULT*的 ALSA 裝置記錄。 依預設， *預設* 一律會指向卡片0裝置0，但使用者可以在檔案中變更它 `asound.conf` 。 

語音裝置 SDK 中的 Microsoft 音訊堆疊僅支援以 16 KHz 的整數倍數取樣率進行縮減取樣。 此外，也支援下列格式：32位的 IEEE 位元組由小到大浮點數、32位的位元組由小到大帶正負號的 int、24位小位元組、帶正負號的 int、16位小位元組帶正負號的 int，以及8位帶正負號的 int。

## <a name="android"></a>Android
語音裝置 SDK 目前僅支援 [Roobo v1](speech-devices-sdk-android-quickstart.md) 。 行為與舊版相同，但現在 `MicArrayGeometryConfigFile` 屬性可以用來指定包含波束成形範圍的 JSON 檔案。

## <a name="microphone-array-configuration-json"></a>麥克風陣列設定 JSON

麥克風陣列幾何設定的 JSON 檔案會遵循 [json 架構](https://aka.ms/sdsdk-micarray-json)。 以下是遵循架構的一些範例。


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Or


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Or

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [選擇您的語音裝置](get-speech-devices-sdk.md)
