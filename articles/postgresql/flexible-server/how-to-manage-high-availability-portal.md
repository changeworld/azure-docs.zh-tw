---
title: 管理區域冗余高可用性-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器
description: 本文說明如何透過 Azure 入口網站，在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中啟用或停用區域冗余高可用性。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933851"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>在彈性的伺服器中管理區域冗余高可用性

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

本文說明如何在彈性的伺服器中啟用或停用區域冗余高可用性設定。

高可用性功能布建在不同區域中實體分隔主要和待命複本。 如需詳細資訊，請參閱 [高可用性概念檔](./concepts-high-availability.md)。 您可以選擇在建立彈性的伺服器時或建立之後，啟用高可用性。 此頁面會提供如何啟用或停用高可用性的指導方針。 此操作不會變更您的其他設定，包括 VNET 設定、防火牆設定和備份保留。 同樣地，啟用和停用高可用性是一項線上作業，而且不會影響您的應用程式連線能力和操作。

## <a name="pre-requisites"></a>必要條件

區域冗余高可用性僅適用于支援多個區域的區域。 

## <a name="enable-high-availability-during-server-creation"></a>在伺服器建立期間啟用高可用性

本節特別針對 HA 相關的欄位提供詳細資料。 您可以遵循下列步驟來部署高可用性，同時建立彈性的伺服器。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇 [彈性伺服器]，然後按一下 [建立]。  如需有關如何填入詳細資料（例如 **訂**用帳戶、 **資源群組**、 **伺服器名稱**、 **區域**和其他欄位）的詳細資訊，請參閱伺服器建立的操作說明文件。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="查看訂用帳戶和區域":::

2.  選擇您的 **可用性區域**。 如果您想要在與資料庫相同的可用性區域中共置應用程式，以降低延遲，這會很有用。 如果您想要讓有彈性的伺服器部署到任何可用性區域，請選擇 [ **無偏好** 設定]。
    ![AZ selection ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="Availability zone 選取專案":::  

3.  在可用性選項中，按一下 [ **區域冗余高可用性** ] 的核取方塊。

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="高可用性核取方塊":::

4.  如果您想要變更預設的計算和儲存體，請按一下 [  **設定伺服器**]。
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="設定伺服器-計算 + 儲存體":::  

5.  如果選取 [高可用性] 選項，高載層將無法供選擇。 您可以選擇 **一般用途** 或 **記憶體優化** 計算層級。 然後，您可以從下拉式清單中選取您選擇的 **計算大小** 。

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="計算層選取專案":::  


6.  使用滑動軸在 GiB 中選取 [ **儲存體大小** ]，並選取7天到35天之間的 **備份保留期限** 。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="儲存體備份"::: 

7. 按一下 [檔案]  。 

## <a name="enable-high-availability-post-server-creation"></a>在伺服器建立後啟用高可用性

請遵循下列步驟，為您現有的彈性伺服器啟用高可用性。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的于 postgresql 彈性伺服器。

2.  在 [彈性伺服器] 頁面上，按一下左側面板中的 [ **高可用性** ] 以開啟 [高可用性] 頁面。
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="左面板選取範圍"::: 

3.  按一下 [**區域冗余高可用性**] 核取方塊來**啟用**此選項，然後按一下 [ **儲存**]   以儲存變更。

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="啟用高可用性"::: 

4.  確認對話方塊將會顯示，表示藉由啟用高可用性，您的成本將會因為額外的伺服器和存放裝置部署而增加。

5.  按一下 [ **啟用 HA** ] 按鈕以啟用高可用性。

6.  將會顯示通知，指出高可用性部署正在進行中。

## <a name="disable-high-availability"></a>停用高可用性

請遵循下列步驟來停用已設定區域冗余之彈性伺服器的高可用性。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。

2.  在 [彈性伺服器] 頁面上，按一下前端面板中的 [ **高可用性** ] 以開啟 [高可用性] 頁面。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="左面板選取範圍"::: 

3.  按一下 [ **區域冗余高可用性** ] 核取方塊以 **停** 用此選項。 然後按一下 [ **儲存**]   以儲存變更。

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="停用高可用性"::: 

4.  確認對話方塊將會顯示，您可以在其中確認停用高可用性。

5.  按一下 [ **停用 HA** ] 按鈕以停用高可用性。

6.  通知將會顯示高可用性部署的解除委任正在進行中。

## <a name="next-steps"></a>下一步

-   瞭解 [商務持續性](./concepts-business-continuity.md)
-   深入瞭解 [區域冗余高可用性](./concepts-high-availability.md)
