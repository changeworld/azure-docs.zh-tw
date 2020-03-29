---
title: 使用語音 SDK 資料流編解碼器壓縮音訊 - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音 SDK 將壓縮音訊資料流到語音服務。 適用于linux、Android中的JAVA和iOS中的目標C的C++、C#和JAVA。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 3fab02d3dc567a2c54edad5bfb05abe7d99f7b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943869"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>將編解碼器壓縮音訊輸入與語音 SDK 一起使用

語音服務 SDK**壓縮音訊輸入流**API 提供了一種使用 或`PullStream`將壓縮音訊資料流到語音服務`PushStream`的方法。

> [!IMPORTANT]
> 當前支援 C#、C++、Linux 上的 JAVA（Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8、CentOS 8）的流壓縮輸入音訊。 在 Android 和 iOS 平臺中，JAVA 和 Objective-C 也支援它。
> 語音 SDK 版本 1.7.0 或更高版本是必需的（RHEL 8 版本 1.10.0 或更高版本，CentOS 8）。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Prerequisites

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>使用編解碼器壓縮音訊輸入的示例代碼

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何識別語音](quickstarts/speech-to-text-from-microphone.md)
