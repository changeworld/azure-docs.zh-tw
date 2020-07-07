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
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: f8ca0caedd438c4ce707a044bc7fa7dd035e8983
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82203228"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>在 Azure API 管理中使用外部 Redis 相容快取

除了使用內建快取，Azure API 管理還允許在外部 Redis 相容的快取中快取回應，例如 Azure Cache for Redis。

使用外部快取可克服內建快取的一些限制：

* 避免在 API 管理更新期間定期清除快取
* 更充分地掌控您的快取組態
* 快取超出您的 API 管理層所允許的資料
* 使用快取搭配 API 管理的耗用量層
* 在[API 管理自我裝載閘道上](self-hosted-gateway-overview.md)啟用快取

如需快取的詳細資訊，請參閱 [API 管理快取原則](api-management-caching-policies.md)和[在 Azure API 管理中自訂快取](api-management-sample-cache-by-key.md)。

![在 APIM 中使用自備的快取](media/api-management-howto-cache-external/overview.png)

您將學到什麼：

> [!div class="checklist"]
> * 在 API 管理中新增外部快取

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

+ [建立 Azure API 管理執行個體](get-started-create-service-instance.md)
+ 了解[在 Azure API 管理中快取](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> 建立 Azure Redis 快取

本節說明如何在 Azure 中建立「Azure Redis 快取」。 如果您在 Azure 內部或外部已經有「Azure Redis 快取」，則可以<a href="#add-external-cache">跳到</a>下一節。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"> </a>將 Redis cache 部署到 Kubernetes

針對快取，自我裝載閘道僅依賴外部快取。 為了讓快取成為有效的自我裝載閘道，而且它們所依賴的快取必須彼此接近，以將查閱和存放延遲降到最低。 將 Redis 快取部署到相同的 Kubernetes 叢集中，或在附近的個別叢集中，是最佳選項。 請遵循此[連結](https://github.com/kubernetes/examples/tree/master/guestbook)以瞭解如何將 Redis 快取部署至 Kubernetes 叢集。

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>新增外部快取

請依照下面的步驟，在「Azure API 管理」中新增外部「Azure Redis 快取」。

![在 APIM 中使用自備的快取](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> [**使用來源**] 設定會指定將使用所設定快取的 Azure 區域或自我裝載閘道位置。 設定為**預設**值的快取會由具有特定符合區域或位置值的快取覆寫。
>
> 例如，如果 API 管理裝載於美國東部、東南亞和西歐區域，而而且已設定兩個快取，一個用於 [預設]****，另一個用於 [東南亞]****，則 [東南亞]**** 的 API 管理會使用自己的快取，而其他兩個區域則會使用 [預設]**** 快取項目。

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>從相同的訂用帳戶新增 Azure Redis 快取

1. 在 Azure 入口網站中瀏覽至您的 API 管理執行個體。
2. 從左側功能表中選取 [外部快取]**** 索引標籤。
3. 按一下 [+新增]**** 按鈕。
4. 在 [快取執行個體]**** 下拉式欄位中選取您的快取。
5. 選取 [**預設**]，或在 [**使用來源**] 下拉式欄位中指定所需的區域。
6. 按一下 [檔案] 。

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>新增裝載於目前 Azure 訂用帳戶或整體 Azure 外的 Azure Redis 快取

1. 在 Azure 入口網站中瀏覽至您的 API 管理執行個體。
2. 從左側功能表中選取 [外部快取]**** 索引標籤。
3. 按一下 [+新增]**** 按鈕。
4. 在 [快取執行個體]**** 下拉式欄位中選取 [自訂]****。
5. 選取 [**預設**]，或在 [**使用來源**] 下拉式欄位中指定所需的區域。
6. 在 [連接字串]**** 欄位中提供您的「Azure Redis 快取」連接字串。
7. 按一下 [檔案] 。

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>將 Redis 快取新增至自我裝載的閘道

1. 在 Azure 入口網站中瀏覽至您的 API 管理執行個體。
2. 從左側功能表中選取 [外部快取]**** 索引標籤。
3. 按一下 [+新增]**** 按鈕。
4. 在 [快取執行個體]**** 下拉式欄位中選取 [自訂]****。
5. 在 [**使用來源**] 下拉式欄位中指定所需的自我裝載閘道位置或**預設值**。
6. 在 [連接字串]**** 欄位中提供您的 Redis 快取連接字串。
7. 按一下 [檔案] 。

## <a name="use-the-external-cache"></a>使用外部快取

在 Azure API 管理中設定外部快取後，它即可用於所有快取原則。 如需詳細步驟，請參閱[新增快取以改善 Azure API 管理的效能](api-management-howto-cache.md)。

## <a name="next-steps"></a><a name="next-steps"> </a>後續步驟

* 如需快取原則的詳細資訊，請參閱 [API 管理原則參考文件][API Management policy reference]中的[快取原則][Caching policies]。
* 如需使用原則運算式依索引鍵快取項目的詳細資訊，請參閱 [在 Azure API 管理中自訂快取](api-management-sample-cache-by-key.md)。

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
