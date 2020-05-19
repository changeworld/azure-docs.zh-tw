---
title: 在防火牆後方轉譯-Translator
titleSuffix: Azure Cognitive Services
description: Azure 認知服務 Translator 可以使用功能變數名稱或 IP 篩選在防火牆後方轉譯。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: dce41879f77d0bed44daa89c1dabbcc3f92e145e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592520"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>如何使用 Translator 在 IP 防火牆後方轉譯

Translator 可以使用功能變數名稱或 IP 篩選在防火牆後方轉譯。 網域名稱篩選是慣用的方法。 我們**不建議**從已篩選 IP 的防火牆後方執行 Microsoft Translator。 安裝程式未來可能會中斷，恕不另行通知。

## <a name="translator-ip-addresses"></a>Translator IP 位址
2019年8月21日起的 api.cognitive.microsofttranslator.com-Translator IP 位址：

* **亞太地區：** 20.40.125.208、20.43.88.240、20.184.58.62、40.90.139.163、104.44.89.44
* **歐洲：** 40.90.138.4、40.90.141.99、51.105.170.64、52.155.218.251
* **北美洲：** 40.90.139.36、40.90.139.2、40.119.2.134、52.224.200.129、52.249.207.163

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [翻譯工具中的 IP 防火牆後方轉譯](reference/v3-0-translate.md)
