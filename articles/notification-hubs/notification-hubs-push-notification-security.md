---
title: 通知中樞資訊安全模型
description: 瞭解 Azure 通知中樞的安全性模型。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76263756"
---
# <a name="notification-hubs-security"></a>通知中樞安全性

## <a name="overview"></a>總覽

本主題說明 Azure 通知中樞的安全性模型。

## <a name="shared-access-signature-security"></a>共用存取簽章安全性

通知中樞會執行稱為*共用存取*簽章（SAS）的實體層級安全性配置。 每個規則都包含名稱、金鑰值（共用密碼）及一組許可權，如稍後的[安全性宣告](#security-claims)中所述。 

建立中樞時，會自動建立兩個規則：一個具有**接聽**許可權（用戶端應用程式使用），另一個具有**所有**許可權（應用程式後端使用）：

- **DefaultListenSharedAccessSignature**：僅授與**接聽**許可權。
- **DefaultFullSharedAccessSignature**：授與**接聽**、**管理**和**傳送**許可權。 此原則僅適用于您的應用程式後端。 請勿在用戶端應用程式中使用它;使用僅具有**接聽**存取權的原則。 若要使用新的 SAS 權杖來建立新的自訂存取原則，請參閱本文稍後的[適用于存取原則的 sas 權杖](#sas-tokens-for-access-policies)。

從用戶端應用程式執行註冊管理時，若透過通知傳送的資訊不是敏感性資訊 (例如氣象更新)，常見存取通知中樞的方法是將只具接聽存取權限之規則的索引鍵值，授與用戶端應用程式，並將該規則之完整存取權限的索引鍵值，授與應用程式後端。

應用程式不應該在 Windows Store 用戶端應用程式中內嵌金鑰值;相反地，讓用戶端應用程式在啟動時從應用程式後端取出它。

具有**接聽**存取權的金鑰可讓用戶端應用程式註冊任何標記。 如果您的應用程式必須將特定標籤的註冊限制為特定用戶端（例如，當標記代表使用者識別碼時），您的應用程式後端必須執行註冊。 如需詳細資訊，請參閱[註冊管理](notification-hubs-push-notification-registration-management.md)。 請注意，如此一來，用戶端應用程式將無法直接存取通知中樞。

## <a name="security-claims"></a>安全性宣告

與其他實體類似，通知中樞作業允許三種安全性宣告：**接聽**、**傳送**和**管理**。

| 宣告   | Description                                          | 允許的作業 |
| ------- | ---------------------------------------------------- | ------------------ |
| 接聽  | 建立/更新、讀取及刪除單一註冊 | 建立/更新註冊<br><br>讀取註冊<br><br>讀取控制代碼的所有註冊<br><br>刪除註冊 |
| Send    | 將訊息傳送至通知中樞                | 傳送訊息 |
| 管理  | 對通知中樞執行 CRUD (包含更新 PNS 認證及安全性金鑰) 並依標記讀取的註冊 |建立/更新/讀取/刪除中樞<br><br>依標記讀取註冊 |

通知中樞接受以直接在中樞上設定的共用金鑰所產生的 SAS 權杖。

您無法將通知傳送至多個命名空間。 命名空間是通知中樞的邏輯容器，並不涉及傳送通知。

針對命名空間層級的作業使用命名空間層級存取原則（認證）;例如：列出中樞、建立或刪除中樞等。只有中樞層級的存取原則可讓您傳送通知。

### <a name="sas-tokens-for-access-policies"></a>存取原則的 SAS 權杖

若要建立新的安全性宣告或查看現有的 SAS 金鑰，請執行下列動作：

1. 登入 Azure 入口網站。
2. 選取 [所有資源]。
3. 選取您想要建立宣告或查看 SAS 金鑰的通知中樞名稱。
4. 在左側功能表中，選取 [**存取原則**]。
5. 選取 [**新增原則**] 以建立新的安全性宣告。 提供原則的名稱，然後選取您想要授與的許可權。 然後選取 [確定]。
6. [存取原則] 視窗中會顯示完整的連接字串（包括新的 SAS 金鑰）。 您可以將此字串複製到剪貼簿以供稍後使用。

若要從特定原則解壓縮 SAS 金鑰，請選取包含所需 SAS 金鑰之原則旁邊的 [**複製**] 按鈕。 將此值貼到暫存位置，然後複製連接字串的 SAS 金鑰部分。 這個範例會使用名為**mytestnamespace1**的通知中樞命名空間，以及名為**policy2**的原則。 SAS 金鑰是字串結尾附近的值，由**SharedAccessKey**指定：

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![取得 SAS 金鑰](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>後續步驟

- [通知中樞總覽](notification-hubs-push-notification-overview.md)
