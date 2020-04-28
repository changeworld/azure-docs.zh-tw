---
title: 在 Azure API 管理中使用外部快取 | Microsoft Docs
description: 了解如何在 Azure API 管理中設定及使用外部快取。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: 2e8863eed774884a99de8643c9e497378368d166
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "70072495"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>在 Azure API 管理中使用外部 Azure Redis 快取

除了使用內建快取之外，「Azure API 管理」也允許將回應快取在外部「Azure Redis 快取」中。

使用外部的快取能夠克服內建快取的一些限制。 如果您想要執行下列作業，這特別有幫助：

* 避免在 API 管理更新期間定期清除快取
* 更充分地掌控您的快取組態
* 快取超出您的 API 管理層所允許的資料
* 使用快取搭配 API 管理的耗用量層

如需快取的詳細資訊，請參閱 [API 管理快取原則](api-management-caching-policies.md)和[在 Azure API 管理中自訂快取](api-management-sample-cache-by-key.md)。

![在 APIM 中使用自備的快取](media/api-management-howto-cache-external/overview.png)

您將學到什麼：

> [!div class="checklist"]
> * 在 API 管理中新增外部快取

## <a name="prerequisites"></a>先決條件

若要完成本教學課程，您需要：

+ [建立 Azure API 管理執行個體](get-started-create-service-instance.md)
+ 了解[在 Azure API 管理中快取](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> 建立 Azure Redis 快取

本節說明如何在 Azure 中建立「Azure Redis 快取」。 如果您在 Azure 內部或外部已經有「Azure Redis 快取」，則可以<a href="#add-external-cache">跳到</a>下一節。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>新增外部快取

請依照下面的步驟，在「Azure API 管理」中新增外部「Azure Redis 快取」。

![在 APIM 中使用自備的快取](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> [**使用來源**] 設定可指定在進行 api 管理的多區域設定時，哪個 API 管理區域部署會與所設定的快取通訊。 指定為 [預設]**** 的快取會由具有區域值的快取所覆寫。
>
> 例如，如果 API 管理裝載於美國東部、東南亞和西歐區域，而而且已設定兩個快取，一個用於 [預設]****，另一個用於 [東南亞]****，則 [東南亞]**** 的 API 管理會使用自己的快取，而其他兩個區域則會使用 [預設]**** 快取項目。

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>從相同的訂用帳戶新增 Azure Redis 快取

1. 在 Azure 入口網站中瀏覽至您的 API 管理執行個體。
2. 從左側功能表中選取 [外部快取]**** 索引標籤。
3. 按一下 [+新增]**** 按鈕。
4. 在 [快取執行個體]**** 下拉式欄位中選取您的快取。
5. 選取 [**預設**]，或在 [**使用來源**] 下拉式欄位中指定所需的區域。
6. 按一下 [檔案]  。

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>新增裝載於目前 Azure 訂用帳戶或整體 Azure 外的 Azure Redis 快取

1. 在 Azure 入口網站中瀏覽至您的 API 管理執行個體。
2. 從左側功能表中選取 [外部快取]**** 索引標籤。
3. 按一下 [+新增]**** 按鈕。
4. 在 [快取執行個體]**** 下拉式欄位中選取 [自訂]****。
5. 選取 [**預設**]，或在 [**使用來源**] 下拉式欄位中指定所需的區域。
6. 在 [連接字串]**** 欄位中提供您的「Azure Redis 快取」連接字串。
7. 按一下 [檔案]  。

## <a name="use-the-external-cache"></a>使用外部快取

在 Azure API 管理中設定外部快取後，它即可用於所有快取原則。 如需詳細步驟，請參閱[新增快取以改善 Azure API 管理的效能](api-management-howto-cache.md)。

## <a name="next-steps"></a><a name="next-steps"> </a>後續步驟

* 如需快取原則的詳細資訊，請參閱 [API 管理原則參考文件][API Management policy reference]中的[快取原則][Caching policies]。
* 如需使用原則運算式依索引鍵快取項目的詳細資訊，請參閱 [在 Azure API 管理中自訂快取](api-management-sample-cache-by-key.md)。

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
