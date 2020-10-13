---
title: Azure 通訊服務的 SMS 用戶端程式庫概觀
titleSuffix: An Azure Communication Services concept document
description: 提供 SMS 用戶端程式庫及其供應項目的概觀。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d40637b05a5d398124ee52dbca49d55b3112ec95
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758399"
---
# <a name="sms-client-library-overview"></a>SMS 用戶端程式庫概觀

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure 通訊服務 SMS 用戶端程式庫可用來將 SMS 訊息新增至您的應用程式。

## <a name="sms-client-library-capabilities"></a>SMS 用戶端程式庫功能

下列清單顯示我們的用戶端程式庫中目前提供的一組功能。

| 功能群組 | 功能                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| 核心功能 | 傳送和接收 SMS 訊息 </br> *支援 Unicode 表情符號*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 接收傳送訊息的傳遞報表                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 所有字元集 (語言/Unicode 支援)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 長訊息的支援 (最多 2048 個字元)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 長訊息的自動串連                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| 事件            | 使用事件方格設定 Webhook 以接收輸入訊息和傳遞報表 | ✔️   | ✔️    | ✔️    | ✔️      |
| 電話號碼      | 免付費電話號碼                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| 法規        | 退出處理                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| 監視        | 監視已傳送和接收之訊息的使用量                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN 通話      | 將 PSTN 通話功能新增至已啟用 SMS 的免付費電話號碼                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [開始傳送 SMS](../../quickstarts/telephony-sms/send.md)

您可能會對下列文件感興趣：

- 熟悉一般 [SMS 概念](../telephony-sms/concepts.md)
- 取得支援 SMS 的[電話號碼](../../quickstarts/telephony-sms/get-phone-number.md)
- [規劃您的 SMS 解決方案](../telephony-sms/plan-solution.md)
