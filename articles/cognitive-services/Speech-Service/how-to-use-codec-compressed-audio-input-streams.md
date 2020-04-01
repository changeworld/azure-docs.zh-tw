---
title: 使用語音 SDK 串流式傳輸編解碼器壓縮音訊 - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音 SDK 將壓縮音訊流式傳輸到語音服務。 適用於linux、Android中的JAVA和iOS中的目標C的C++、C#和JAVA。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 6aabd27ae38cac0bb9effad2adcadc4935a28c6e
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409662"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>將編解碼器壓縮音訊輸入與語音 SDK 一起使用

語音服務 SDK**壓縮音訊輸入流**API 提供了`PullStream`一種使用 或將壓縮音訊流`PushStream`式傳輸到語音服務 的方法。

> [!IMPORTANT]
> 當前支援 C#、C++、Linux 上的 Java(Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8、CentOS 8)的流壓縮輸入音訊。 在 Android 和 iOS 平臺中,JAVA 和 Objective-C 也支援它。
> 語音 SDK 版本 1.7.0 或更高版本是必需的(RHEL 8 版本 1.10.0 或更高版本,CentOS 8)。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Prerequisites

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>使用編解碼器壓縮音訊輸入的範例代碼

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何辨識語音](quickstarts/speech-to-text-from-microphone.md)
