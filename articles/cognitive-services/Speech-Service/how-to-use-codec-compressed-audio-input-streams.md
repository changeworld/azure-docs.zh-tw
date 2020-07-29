---
title: 使用語音 SDK 的串流編解碼器壓縮音訊-語音服務
titleSuffix: Azure Cognitive Services
description: '瞭解如何使用語音 SDK 將壓縮的音訊串流至語音服務。 適用于 Linux 的 c + +、c # 和 JAVA、Android 中的 JAVA 和 iOS 中的目標-C。'
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 14f87dff680fb72654d1d3e20fe2f0516f9709aa
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282722"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>搭配使用編解碼器壓縮的音訊輸入與語音 SDK

語音服務 SDK**壓縮的音訊輸入資料流程**API 提供了一種方式，可以使用或，將壓縮的音訊串流至語音服務 `PullStream` `PushStream` 。

目前支援在 Windows 上針對 c #、c + +、JAVA 和 Python 提供串流處理壓縮的輸入音訊（不支援 UWP 應用程式）和 Linux （Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 7/8、CentOS 7/8）。 Android 中的 JAVA 也支援此功能。
* RHEL 8 和 CentOS 8 需要1.10.0 或更新版本的語音 SDK
* Windows 需要1.11.0 或更新版本的語音 SDK。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>必要條件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>使用編解碼器壓縮音訊輸入的範例程式碼

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何辨識語音](quickstarts/speech-to-text-from-microphone.md)
