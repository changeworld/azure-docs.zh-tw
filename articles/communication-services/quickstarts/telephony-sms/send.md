---
title: 快速入門 - 傳送 SMS 訊息
titleSuffix: An Azure Communication Services quickstart
description: 了解如何使用 Azure 通訊服務傳送 SMS 訊息。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 52cec861ba81a804081c4b879ab73333a3281e61
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360287"
---
# <a name="quickstart-send-an-sms-message"></a>快速入門：傳送簡訊

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>疑難排解

若要對 SMS 傳遞的相關問題進行疑難排解，您可以[使用事件方格啟用傳遞報告](./handle-sms-events.md)以擷取傳遞詳細資料。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除通訊服務訂閱，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。 深入了解如何[清除資源](../create-communication-resource.md#clean-up-resources)。

## <a name="next-steps"></a>下一步

在本快速入門中，您已了解如何使用 Azure 通訊服務來傳送 SMS 訊息。

> [!div class="nextstepaction"]
> [訂閱 SMS 事件](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [規劃您的 PSTN 解決方案](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [進一步了解 SMS](../../concepts/telephony-sms/concepts.md)