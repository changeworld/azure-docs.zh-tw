---
title: 變更通知中樞命名空間的定價層 | Microsoft Docs
description: 瞭解如何更改 Azure 通知中心命名空間的定價層。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 51d0880b7f56d523a01fbc993993b3caf7328134
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261070"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>變更 Azure 通知中樞命名空間的定價層
通知中樞以三個層級提供：**免費**、**基本**和**標準**。 本文說明如何變更 Azure 通知中樞命名空間的定價層。 

## <a name="overview"></a>總覽
在 Azure 通知中樞，**中樞**是最小的資源/實體。 它通常會對應至一個應用程式，並且可為支援應用程式的每個平台通知系統保存一個憑證。 應用程式可以是混合式或原生和跨平台應用程式。

**命名空間**是通知中樞的集合。 每個命名空間通常由相關和特定用途的中樞所組成。 例如，您可能會有三個不同的命名空間，分別供開發、測試和生產環境使用。 

您可以在命名空間層級建立定價層的關聯。 通知中樞支援三個層級：**免費**、**基本**和**標準**。 您可以為命名空間使用符合個人需求的層級。 以下幾節將說明如何變更通知中樞命名空間的定價層。 

## <a name="use-azure-portal"></a>使用 Azure 入口網站 
使用 Azure 入口網站時，您可以在命名空間頁面或中樞頁面上變更命名空間的定價層。  在中樞頁面上變更定價層時，您實際上會在命名空間層級進行變更。 這會變更命名空間及其下所有中樞的定價層。 

### <a name="change-tier-on-the-namespace-page"></a>在命名空間頁面上變更層級
下列程序將提供在命名空間頁面上為命名空間變更定價層的步驟。 當您變更命名空間的層級時，其結果將套用至命名空間中的所有中樞。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選擇左側功能表上**的所有服務**。 
3. 選取 [物聯網]**** 區段中的 [通知中樞命名空間]****。 如果您選取文字旁的星號 (`*`)，則會將其新增至左側導覽列的 [我的最愛]**** 下方。 它可讓您在下次能更快速地存取命名空間頁面。 將其新增至 [我的最愛] 之後，請選取 [通知中樞命名空間]****。 

    ![所有服務 -> 通知中樞命名空間](./media/change-pricing-tier/all-services-nhub.png)
1. 在 [通知中樞命名空間]**** 頁面上，選取要變更定價層的命名空間。 
2. 在命名空間的 [通知中樞命名空間]**** 頁面上，您可以在 [基本資訊]**** 區段中檢視命名空間目前的定價層。 在下圖中，您可以看到命名空間的定價層為 [免費]****。 

    ![命名空間頁面上目前的定價層](./media/change-pricing-tier/pricing-tier-before.png)
1. 在命名空間的 [通知中樞命名空間]**** 頁面上，選取 [管理]**** 區段下方的 [定價層]****。 

    ![選取命名空間頁面上的定價層](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. 變更您的定價層，然後按一下 [選取]**** 按鈕。    
7. 您會在 [警示]**** 中看到層級變更動作的狀態。 
8. 切換至 [概觀]**** 頁面。 確認 [基本資訊]**** 區段中的 [定價層]**** 欄位顯示新的層級。     
1. 此為選用步驟。 選取命名空間中的任何中樞。 確認您看到與 [基本資訊]**** 區段中相同的定價層。 您應該會看到命名空間中所有的中樞都採用相同的定價層。 

### <a name="change-tier-on-the-hub-page"></a>在中樞頁面上變更層級
下列程序將提供在中樞頁面上為命名空間變更定價層的步驟。 即使您從中樞頁面執行下列步驟，您實際上將變更命名空間及其中所有中樞的定價層。 

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選擇左側功能表上**的所有服務**。
3. 選取 [物聯網]**** 區段中的 [通知中樞]****。 
4. 選取您的通知**中樞**。 
5. 選取左側功能表上的 [定價層]****。 
6. 變更定價層，然後按一下 [選取]**** 按鈕。 此動作會為包含中樞的命名空間變更定價層設定。 因此，您會在命名空間頁面和所有中樞頁面上看到新的定價層。 

## <a name="use-rest-api"></a>使用 REST API
您可以使用下列資源提供者 REST API 來取得目前的定價層，並加以更新。 

### <a name="get-current-pricing-tier-for-a-namespace"></a>取得命名空間目前的定價層
若要取得**目前的命名空間層**，請傳送 GET 命令，如下列範例所示： 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>更新命名空間的定價層
若要**更新命名空間層**，請傳送 PUT 命令，如下列範例所示： 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>後續步驟
如需這些層級和定價的詳細資訊，請參閱[通知中樞定價](https://azure.microsoft.com/pricing/details/notification-hubs/)。
