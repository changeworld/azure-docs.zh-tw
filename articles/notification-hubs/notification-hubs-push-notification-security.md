---
title: 通知中樞資訊安全模型
description: 瞭解 Azure 通知中心的安全模型。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263756"
---
# <a name="notification-hubs-security"></a>通知中心安全性

## <a name="overview"></a>總覽

本主題說明 Azure 通知中樞的安全性模型。

## <a name="shared-access-signature-security"></a>共用訪問簽名安全性

通知中心實現一個實體級安全方案，稱為*共用訪問簽名*（SAS）。 每個規則都包含一個名稱、一個鍵值（共用金鑰）和一組許可權，如後來在[安全聲明](#security-claims)中所述。 

創建中心時，將自動創建兩個規則：一個具有**偵聽**許可權（用戶端應用使用），另一個具有**所有**許可權（應用後端使用）：

- **預設偵聽共用訪問簽名**：僅授予**偵聽**許可權。
- **預設完全共用訪問簽名**：授予**偵聽**、**管理和****發送**許可權。 此策略將僅在應用後端中使用。 請勿在用戶端應用程式中使用它;使用僅具有**偵聽**存取權限的策略。 要使用新的 SAS 權杖創建新的自訂訪問策略，請參閱本文後面有關[訪問策略的 SAS 權杖](#sas-tokens-for-access-policies)。

從用戶端應用程式執行註冊管理時，若透過通知傳送的資訊不是敏感性資訊 (例如氣象更新)，常見存取通知中樞的方法是將只具接聽存取權限之規則的索引鍵值，授與用戶端應用程式，並將該規則之完整存取權限的索引鍵值，授與應用程式後端。

應用不應在 Windows 應用商店用戶端應用中嵌入索引碼;但是，應用不應在 Windows 應用商店用戶端應用中嵌入該值。相反，讓用戶端應用在啟動時從應用後端檢索它。

具有**偵聽**訪問的金鑰允許用戶端應用註冊任何標記。 如果應用必須將註冊限制為特定用戶端（例如，當標記表示使用者 ID 時），則應用後端必須執行註冊。 有關詳細資訊，請參閱[註冊管理](notification-hubs-push-notification-registration-management.md)。 請注意，如此一來，用戶端應用程式將無法直接存取通知中樞。

## <a name="security-claims"></a>安全性宣告

與其他實體類似，通知中心操作允許用於三個安全聲明：**偵聽**、**發送****和管理。**

| 宣告   | 描述                                          | 允許的作業 |
| ------- | ---------------------------------------------------- | ------------------ |
| 接聽  | 建立/更新、讀取及刪除單一註冊 | 建立/更新註冊<br><br>讀取註冊<br><br>讀取控制代碼的所有註冊<br><br>刪除註冊 |
| Send    | 向通知中心發送消息                | 傳送訊息 |
| 管理  | 對通知中樞執行 CRUD (包含更新 PNS 認證及安全性金鑰) 並依標記讀取的註冊 |創建/更新/讀取/刪除集線器<br><br>依標記讀取註冊 |

通知中心接受在中心上直接配置的共用金鑰生成的 SAS 權杖。

無法向多個命名空間發送通知。 命名空間是通知中心的邏輯容器，不參與發送通知。

使用命名空間級訪問策略（憑據）進行命名空間級操作;例如：列出中心、創建或刪除集線器等。只有中心級訪問策略才能發送通知。

### <a name="sas-tokens-for-access-policies"></a>用於訪問策略的 SAS 權杖

要創建新的安全聲明或查看現有的 SAS 金鑰，請執行以下操作：

1. 登入 Azure 入口網站。
2. 選取 [所有資源]****。
3. 選擇要為其創建聲明或查看 SAS 金鑰的通知中心的名稱。
4. 在左側功能表中，選擇 **"訪問策略**"。
5. 選擇 **"新策略"** 以創建新的安全聲明。 為策略指定名稱，並選擇要授予的許可權。 然後選擇 **"確定**"。
6. 完整的連接字串（包括新的 SAS 鍵）顯示在"訪問策略"視窗中。 您可以將此字串複製到剪貼簿，以便以後使用。

要從特定策略中提取 SAS 金鑰，請選擇包含所需 SAS 鍵的策略旁邊的 **"複製**"按鈕。 將此值粘貼到臨時位置，然後複製連接字串的 SAS 鍵部分。 此示例使用稱為**mytestnamespace1**的通知中心命名空間和名為**policy2**的策略。 SAS 鍵是字串末尾附近的值，由**SharedAccessKey**指定：

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![獲取 SAS 金鑰](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>後續步驟

- [通知中心概述](notification-hubs-push-notification-overview.md)
