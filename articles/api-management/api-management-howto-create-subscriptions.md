---
title: 在 Azure API 管理中建立訂用帳戶 | Microsoft Docs
description: 了解如何在 Azure API 管理中建立訂用帳戶。 需要有訂用帳戶，才能取得允許存取 Api 的訂用帳戶金鑰。
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
ms.openlocfilehash: 191323a4c150c00c93245be35c9c8af381e26b42
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904849"
---
# <a name="create-subscriptions-in-azure-api-management"></a>在 Azure API 管理中建立訂用帳戶

透過 Azure API 管理發佈 API 時，安全地存取這些 API 的簡單常用方式是使用訂用帳戶金鑰。 必須使用已發佈 API 的用戶端應用程式在呼叫這些 API 時，必須在 HTTP 要求中包含有效的訂用帳戶金鑰。 若要取得用於存取 API 的訂用帳戶金鑰，則需要訂用帳戶。 如需訂用帳戶的詳細資訊，請參閱[AZURE API 管理中](api-management-subscriptions.md)的訂用帳戶。

本文將逐步引導您在 Azure 入口網站中建立訂用帳戶。

## <a name="prerequisites"></a>必要條件

若要採取本文中的步驟，必要條件如下：

+ [建立 API 管理實例](get-started-create-service-instance.md)。
+ 了解 [API 管理中的訂用帳戶](api-management-subscriptions.md)。

## <a name="create-a-new-subscription"></a>建立新的訂用帳戶

1. 選取左邊功能表的 [訂用帳戶]****。
2. 選取 [訂用帳戶]****。
3. 提供訂用帳戶的名稱並選取範圍。
4. （選擇性）選擇訂用帳戶是否應與使用者相關聯。
5. 選取 [儲存]。

![彈性的訂用帳戶](./media/api-management-subscriptions/flexible-subscription.png)

建立訂用帳戶之後，會提供兩個 API 金鑰來存取 API。 一個是主要金鑰，一個是次要金鑰。 

## <a name="next-steps"></a>後續步驟
取得 APIM 的詳細資訊：

+ 了解 APIM 中的其他[概念](api-management-terminology.md)。
+ 如需深入了解 APIM，請依照我們的[教學課程](import-and-publish.md)。
+ 針對常見的問題，請查看我們的[常見問題集頁面](api-management-faq.md)。