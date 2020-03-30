---
title: 核准 Just-In-Time 存取
description: 描述 Azure 託管應用程式的消費者如何批准對託管應用程式進行即時訪問的請求。
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651016"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>配置和批准 Azure 託管應用程式的及時訪問

作為託管應用程式的消費者，您可能不願意讓發行者永久訪問託管資源組。 為了更好地控制對託管資源的訪問，Azure 託管應用程式提供了一個稱為即時 （JIT） 存取權限的功能，該功能當前處於預覽狀態。 它使您能夠批准發行者何時和多長時間有權訪問資源組。 發行者可以在該期間進行必要的更新，但當該時間結束時，發行者的訪問將過期。

授予存取權限的工作流是：

1. 發行者向市場添加託管應用程式，並指定 JIT 存取權限可用。

1. 在部署期間，您可以為託管應用程式的實例啟用 JIT 存取權限。

1. 部署後，可以更改 JIT 訪問的設置。

1. 發行者發送訪問請求。

1. 您批准請求。

本文重點介紹消費者為啟用 JIT 訪問和批准請求而執行的操作。 要瞭解如何使用 JIT 訪問發佈託管應用程式，請參閱[在 Azure 託管應用程式中請求及時訪問](request-just-in-time-access.md)。

> [!NOTE]
> 要使用即時訪問，必須具有[Azure 活動目錄 P2 許可證](../../active-directory/privileged-identity-management/subscription-requirements.md)。

## <a name="enable-during-deployment"></a>部署期間啟用

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 查找啟用了 JIT 的託管應用程式的市場條目。 選取 [建立]****。

1. 在為新的託管應用程式提供值的同時 **，JIT 配置**步驟允許您為託管應用程式啟用或禁用 JIT 訪問。 選擇 **"是****"以啟用 JIT 訪問**。 預設情況下，此選項是針對在市場中啟用 JIT 定義的託管應用程式選擇的。

   ![配置訪問](./media/approve-just-in-time-access/configure-jit-access.png)

   只能在部署期間啟用 JIT 訪問。 如果選擇 **"否**"，則發行者將獲得對託管資源組的永久存取權限。 以後無法啟用 JIT 存取權限。

1. 要更改預設審批設置，請選擇 **"自訂 JIT 配置**"。

   ![自訂訪問](./media/approve-just-in-time-access/customize-jit-access.png)

   預設情況下，啟用了 JIT 的託管應用程式具有以下設置：

   * 批准模式 = 自動
   * 最長訪問持續時間 = 8 小時
   * 核准者 = 無

   當審批模式設置為**自動**時，核准者會收到每個請求的通知，但請求將自動獲得批准。 設置為**手動**時，核准者會收到每個請求的通知，其中一個必須批准它。

   啟動最長持續時間指定發行者可以請求訪問託管資源組的最大時間量。

   核准者清單是可以批准 JIT 訪問請求的 Azure 活動目錄使用者。 要添加審批人，請選擇 **"添加審批人"** 並搜索該使用者。

   更新設置後，選擇 **"保存**"。

## <a name="update-after-deployment"></a>部署後更新

您可以變更要求的審批方式的值。 但是，如果在部署期間未啟用 JIT 訪問，則以後無法啟用它。

要更改已部署的託管應用程式的設置，請執行以下操作：

1. 在門戶中，選擇管理應用程式。

1. 選擇**JIT 配置**並根據需要更改設置。

   ![更改訪問設置](./media/approve-just-in-time-access/change-settings.png)

1. 完成時，選取 [儲存]****。

## <a name="approve-requests"></a>核准要求

當發行者請求訪問時，您將收到請求通知。 可以直接通過託管應用程式批准 JIT 訪問請求，也可以通過 Azure AD 特權標識管理服務跨所有託管應用程式批准 JIT 訪問請求。 要使用即時訪問，必須具有[Azure 活動目錄 P2 許可證](../../active-directory/privileged-identity-management/subscription-requirements.md)。

要通過託管應用程式核准請求：

1. 為託管應用程式選擇**JIT 訪問**，然後選擇 **"批准請求**"。

   ![核准要求](./media/approve-just-in-time-access/approve-requests.png)
 
1. 選擇要批准的請求。

   ![選擇請求](./media/approve-just-in-time-access/select-request.png)

1. 在表單中，提供審批原因，然後選擇 **"批准**"。

要通過 Azure AD 特權標識管理核准請求，

1. 選擇**所有服務**並開始搜索**Azure AD 特權標識管理**。 從可用選項中選擇它。

   ![搜索服務](./media/approve-just-in-time-access/search.png)

1. 選擇 **"批准請求**"。

   ![選擇批准請求](./media/approve-just-in-time-access/select-approve-requests.png)

1. 選擇**Azure 託管應用程式**，然後選擇要審批的請求。

   ![選擇請求](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>後續步驟

要瞭解如何使用 JIT 訪問發佈託管應用程式，請參閱[在 Azure 託管應用程式中請求及時訪問](request-just-in-time-access.md)。
