---
title: 快速入門：使用 REST API 取得意圖 - LUIS
description: 在本 REST API 快速入門中，使用可用的公用 LUIS 應用程式，從交談文字判斷使用者的用意。
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 2fd52011ed0d139e98740c8de077987edfae2c32
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873155"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>快速入門：使用 REST API 取得意圖

在本快速入門中，使用 LUIS 應用程式，從交談文字中判斷使用者的意圖。 以文字形式將使用者的意圖傳送至披薩應用程式的 HTTP 預測端點。 LUIS 會在端點套用披薩應用程式的模型來分析自然語言文字的意義、判斷整體意圖，並擷取與應用程式主體網域相關的資料。

在本文中，您需要免費 [LUIS](https://www.luis.ai) 帳戶。

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
