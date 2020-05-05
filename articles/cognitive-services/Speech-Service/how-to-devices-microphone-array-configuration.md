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
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82781774"
---
# <a name="how-to-configure-a-microphone-array"></a>如何設定麥克風陣列

在本文中，您將瞭解如何設定[麥克風陣列](https://aka.ms/sdsdk-microphone)。 其中包括設定工作角度，以及如何選取要用於語音裝置 SDK 的麥克風。

語音裝置 SDK 最適合用來根據[我們的指導方針](https://aka.ms/sdsdk-microphone)設計的麥克風陣列。 麥克風陣列設定可由作業系統提供，或透過下列其中一種方法提供。

語音裝置 SDK 一開始支援的麥克風陣列，是從一組固定的設定中選取。

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

在 Windows 上，麥克風陣列設定是由音訊驅動程式提供。

從1.11.0 開始，語音裝置 SDK 也支援從[JSON](https://aka.ms/sdsdk-micarray-json)檔案進行設定。


## <a name="windows"></a>Windows
在 Windows 上，會自動從音訊驅動程式取得麥克風陣列幾何資訊。 因此，屬性`DeviceGeometry`、 `SelectedGeometry`和`MicArrayGeometryConfigFile`都是選擇性的。 我們使用提供的[JSON](https://aka.ms/sdsdk-micarray-json)檔案`MicArrayGeometryConfigFile` ，僅取得波束成形範圍。

如果使用`AudioConfig::FromMicrophoneInput`指定麥克風陣列，則會使用指定的麥克風。 如果沒有指定或`AudioConfig::FromDefaultMicrophoneInput`呼叫麥克風，則會使用 Windows 上的 [聲音設定] 中指定的預設麥克風。
「語音裝置 SDK」中的 Microsoft 音訊堆疊僅針對 16 KHz 整數倍數的取樣速率，支援關閉取樣。

## <a name="linux"></a>Linux
在 Linux 上，必須提供麥克風幾何資訊。 使用`DeviceGeometry`和`SelectedGeometry`仍然受支援。 也可以透過使用`MicArrayGeometryConfigFile`屬性的 JSON 檔案來提供。 類似于 Windows，JSON 檔案可以提供波束成形範圍。

如果使用`AudioConfig::FromMicrophoneInput`指定麥克風陣列，則會使用指定的麥克風。 如果沒有指定或`AudioConfig::FromDefaultMicrophoneInput`呼叫麥克風，我們會從名為*default*的 ALSA 裝置記錄。 根據預設，*預設*一律會指向 [卡片0裝置 0]，但使用者可以在檔案`asound.conf`中變更它。 

「語音裝置 SDK」中的 Microsoft 音訊堆疊僅針對 16 KHz 的整數倍數，支援取樣率的縮減取樣。 此外，也支援下列格式： 32-bit IEEE 小 endian 浮點數、32位小數位格式帶正負號的 int、24位小數位序帶正負號的 int、16位小數位的帶正負號的 int 和8位帶正負號的 int。

## <a name="android"></a>Android
語音裝置 SDK 目前僅支援[Roobo v1](speech-devices-sdk-android-quickstart.md) 。 行為與舊版相同，但是 now `MicArrayGeometryConfigFile`屬性可以用來指定包含波束成形範圍的 JSON 檔案。

## <a name="microphone-array-configuration-json"></a>麥克風陣列設定 JSON

麥克風陣列幾何設定的 JSON 檔案會遵循[json 架構](https://aka.ms/sdsdk-micarray-json)。 以下是遵循架構的一些範例。


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


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇您的語音裝置](get-speech-devices-sdk.md)
