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
ms.date: 04/02/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 855a050afa14144f8963f24398c6b7b3939ef562
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656475"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>變更 Azure 通知中樞命名空間的定價層

通知中樞以三個層級提供：**免費**、**基本**和**標準**。 本文說明如何變更 Azure 通知中樞命名空間的定價層。

## <a name="overview"></a>概觀

在 Azure 通知中樞，中樞是最小的資源/實體。 它通常映射到一個應用程式,並且可以為我們支援的應用程式的每個平臺通知系統 (PNS) 保留一個證書。 該應用程式可以是混合應用程式,也可以是本機和跨平台應用程式。

**命名空間**是通知中樞的集合。 每個命名空間通常由相關和特定用途的中樞所組成。 例如,您可以分別有三個不同的命名空間來用於開發、測試和生產目的。

您可以將命名空間與**可用**、**基本**或**標準**定價層相關聯。 您可以為命名空間使用符合個人需求的層級。 以下幾節將說明如何變更通知中樞命名空間的定價層。

## <a name="use-azure-portal"></a>使用 Azure 入口網站

使用 Azure 門戶時,可以更改命名空間頁上或中心頁上的命名空間的定價層。 在中樞頁面上變更定價層時，您實際上會在命名空間層級進行變更。 這會變更命名空間及其下所有中樞的定價層。

### <a name="change-tier-on-the-namespace-page"></a>在命名空間頁面上變更層級

以下過程演示如何更改命名空間頁上的命名空間的定價層。 更改命名空間的層時,它將應用於該命名空間中的所有中心。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選擇左方選單上**的服務**。
3. 選取 [物聯網]**** 區段中的 [通知中樞命名空間]****。 如果選擇文本旁邊的星號 (),`*`則它將添加到**FAVORITES**下的左側導航欄中。 它可以説明您更快地訪問命名空間頁面。 將您新增到我的最愛後, 選擇**通知中心命名空間**。

    ![所有服務 -> 通知中樞命名空間](./media/change-pricing-tier/all-services-nhub.png)

4. 在 [通知中樞命名空間]**** 頁面上，選取要變更定價層的命名空間。
5. 在命名空間的 [通知中樞命名空間]**** 頁面上，您可以在 [基本資訊]**** 區段中檢視命名空間目前的定價層。 在下圖中，您可以看到命名空間的定價層為 [免費]****。

    ![命名空間頁面上目前的定價層](./media/change-pricing-tier/pricing-tier-before.png)

6. 在命名空間的 [通知中樞命名空間]**** 頁面上，選取 [管理]**** 區段下方的 [定價層]****。

    ![選取命名空間頁面上的定價層](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. 更改定價層,然後單擊 **「選擇**」按鈕。
8. 您可以在**警報**中查看層更改操作的狀態。
9. 切換至 [概觀]**** 頁面。 確認 [基本資訊]**** 區段中的 [定價層]**** 欄位顯示新的層級。
10. 此為選用步驟。 選取命名空間中的任何中樞。 確認您看到與 [基本資訊]**** 區段中相同的定價層。 您應該看到命名空間中所有中心的相同定價層。

### <a name="change-tier-on-the-hub-page"></a>在中樞頁面上變更層級

以下過程演示如何更改中心頁上的命名空間的定價層。 即使您從中樞頁面執行下列步驟，您實際上將變更命名空間及其中所有中樞的定價層。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選擇左方選單上**的服務**。
3. 選取 [物聯網]**** 區段中的 [通知中樞]****。
4. 選取您的通知**中樞**。
5. 選取左側功能表上的 [定價層]****。
6. 變更定價層，然後按一下 [選取]**** 按鈕。 此動作會為包含中樞的命名空間變更定價層設定。 因此，您會在命名空間頁面和所有中樞頁面上看到新的定價層。

> [!NOTE]
> 所有定價層更改立即生效。

## <a name="use-rest-api"></a>使用 REST API

您可以使用下列資源提供者 REST API 來取得目前的定價層，並加以更新。

### <a name="get-current-pricing-tier-for-a-namespace"></a>取得命名空間目前的定價層

要取得目前命名空間層,請送出 GET 命令,如以下範例所示:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>更新命名空間的定價層

要更新命名空間層,請寄送 PUT 命令,如以下範例所示:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>後續步驟

如需這些層級和定價的詳細資訊，請參閱[通知中樞定價](https://azure.microsoft.com/pricing/details/notification-hubs/)。
