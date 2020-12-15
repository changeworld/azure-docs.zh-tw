---
title: Azure API 管理中的訂用帳戶 | Microsoft Docs
description: 深入了解 Azure APIM 中的訂用帳戶概念。 取用者可以使用 Azure API 管理中的訂用帳戶來存取 Api。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511241"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API 管理中的訂用帳戶

訂用帳戶是 Azure APIM 中的重要概念。 API 取用者通常都會使用這些訂用帳戶來存取透過 APIM 執行個體發佈的 API。 本文提供下列概念的概觀。

## <a name="what-are-subscriptions"></a>什麼是訂用帳戶？

透過 APIM 發佈 API 時，安全地存取這些 API 的簡單常用方式是使用訂用帳戶金鑰。 需要使用已發佈 API 的開發人員在呼叫這些 API 時，必須在 HTTP 要求中包含有效的訂用帳戶金鑰。 否則，呼叫會立即遭到 APIM 閘道的拒絕。 這些呼叫並不會轉送到後端服務。

若要取得用於存取 API 的訂用帳戶金鑰，則需要訂用帳戶。 訂用帳戶基本上是一組訂用帳戶金鑰的具名容器。 需要取用已發行 API 的開發人員可以取得訂用帳戶。 而且不需要 API 發行者的核准。 API 發行者還可以為 API 取用者直接建立訂用帳戶。

> [!TIP]
> APIM 也支援其他保護 API 存取權的機制，包括下列範例：
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [用戶端憑證](api-management-howto-mutual-certificates-for-clients.md)
> - [限制呼叫端 Ip](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>訂用帳戶的範圍

訂用帳戶可以與各種範圍相關聯：產品、所有 API 或個別的 API。

### <a name="subscriptions-for-a-product"></a>產品的訂用帳戶

傳統上，APIM 中的訂用帳戶始終與單一 [API 產品](api-management-terminology.md)範圍相關聯。 開發人員可在開發人員入口網站上找到產品清單。 然後他們會針對想要使用的產品提交訂用帳戶要求。 訂用帳戶要求核准之後 (自動或由 API 發行者)，開發人員可以使用其中的金鑰存取產品中的所有 API。 目前，開發人員入口網站只會顯示 [使用者設定檔] 區段下的產品範圍訂閱。 

![產品訂用帳戶](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> 在某些情況下，API 發行者可能會想要公開發佈 API 產品，而不需要訂用帳戶。 他們可以取消選取 Azure 入口網站中產品 [設定] 頁面中的 [需要訂用帳戶] 選項。 因此，無需 API 金鑰即可存取產品下的所有 API。

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>適用於所有 API 或個別 API 的訂用帳戶

當我們引進 APIM 的[使用](https://aka.ms/apimconsumptionblog)層時，我們進行了一些變更以簡化金鑰管理：
- 首先，我們新增了兩個訂用帳戶範圍：所有 API 和單一 API。 訂用帳戶的範圍不再限於 API 產品。 現在可以建立金鑰以授與對 API (或 APIM 執行個體中所有 API) 的存取權，而不需要先建立一項產品，並向其新增 API。 此外，每個 APIM 執行個體現在隨附不可變動的「所有 API」訂用帳戶。 此訂用帳戶可讓測試主控台內的 API 測試和偵錯變得更容易且方便。

- 第二，APIM 現在允許 **獨立** 訂用帳戶。 訂用帳戶不再需要與開發人員帳戶相關聯。 此功能在諸如多個開發人員或小組共用訂用帳戶時的情況下非常有用。

- 最後，API 發行者現在可以直接在 Azure 入口網站中[建立訂用帳戶](api-management-howto-create-subscriptions.md)：

    ![彈性的訂用帳戶](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>後續步驟
取得 APIM 的詳細資訊：

+ 了解 APIM 中的其他[概念](api-management-terminology.md)。
+ 如需深入了解 APIM，請依照我們的[教學課程](import-and-publish.md)。
+ 針對常見的問題，請查看我們的[常見問題集頁面](api-management-faq.md)。
