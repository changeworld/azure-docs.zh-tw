---
title: 防火牆後翻譯 - Translator Text API
titleSuffix: Azure Cognitive Services
description: Azure 認知服務轉換器文本 API 可以使用功能變數名稱或 IP 篩選在防火牆後面進行轉換。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837393"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>如何使用 Translator Text API IP 進行防火牆後翻譯

Translator Text API 可以在使用網域名稱或 IP 篩選的防火牆後方進行翻譯。 網域名稱篩選是慣用的方法。 我們**不建議**從已篩選 IP 的防火牆後方執行 Microsoft Translator。 安裝程式未來可能會中斷，恕不另行通知。

## <a name="translator-ip-addresses"></a>Translator IP 位址
api.cognitive.microsofttranslator.com的 IP 位址 - 截至 2019 年 8 月 21 日，微軟翻譯器文本 API：

* **亞太地區：** 20.40.125.208， 20.43.88.240， 20.184.58.62， 40.90.139.163， 104.44.89.44
* **歐洲：** 40.90.138.4， 40.90.141.99， 51.105.170.64， 52.155.218.251
* **北美：** 40.90.139.36， 40.90.139.2， 40.119.2.134， 52.224.200.129， 52.249.207.163


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [在 Translator API 呼叫中進行 IP 防火牆後翻譯](reference/v3-0-translate.md)
