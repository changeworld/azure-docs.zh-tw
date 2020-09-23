---
title: 管理區域冗余高可用性-Azure 入口網站適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 本文說明如何透過 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫彈性的伺服器中啟用或停用區域冗余高可用性。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 09cd7428519cbf84c785efa16b61b9507a3c0b94
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936658"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>在適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽版中管理區域冗余高可用性) 

本文說明如何在彈性的伺服器中啟用或停用區域冗余高可用性設定。

高可用性功能布建在不同區域中實體分隔主要和待命複本。 如需詳細資訊，請參閱 [高可用性概念檔](./concepts/../concepts-high-availability.md)。 

> [!IMPORTANT]
> 在彈性的伺服器建立期間，您只能啟用區域冗余高可用性。

此頁面會提供如何啟用或停用高可用性的指導方針。 此操作不會變更您的其他設定，包括 VNET 設定、防火牆設定和備份保留。 同樣地，停用高可用性是一項線上作業，而且不會影響您的應用程式連線能力和操作。

> [!IMPORTANT]
> 區域冗余高可用性適用于一組有限的區域：東南亞、WestUS 2、西歐和美國東部。  

## <a name="enable-high-availability-during-server-creation"></a>在伺服器建立期間啟用高可用性

本節特別針對 HA 相關的欄位提供詳細資料。 您可以遵循下列步驟來部署高可用性，同時建立彈性的伺服器。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇 [彈性伺服器]，然後按一下 [ **建立**]。  如需有關如何填入詳細資料（例如 **訂**用帳戶、 **資源群組**、 **伺服器名稱**、 **區域**和其他欄位）的詳細資訊，請參閱伺服器建立的操作說明文件。

2.  在可用性選項中，按一下 [ **區域冗余高可用性** ] 的核取方塊。

3.  如果您想要變更預設的計算和儲存體，請按一下 [  **設定伺服器**]。

4.  如果選取 [高可用性] 選項，高載層將無法供選擇。 您可以選擇 **一般用途** 或 **記憶體優化** 計算層級。

    > [!IMPORTANT]
    > 針對 ***一般用途*** 和 ***記憶體優化*** 定價層，我們僅支援區域冗余高可用性。

5.  從下拉式清單中選取您選擇的 **計算大小** 。

6.  使用滑動軸在 GiB 中選取 [ **儲存體大小** ]，並選取7天到35天之間的 **備份保留期限** 。   

## <a name="disable-high-availability"></a>停用高可用性

請遵循下列步驟來停用已設定區域冗余之彈性伺服器的高可用性。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MySQL 的 Azure 資料庫有彈性的伺服器。

2.  在 [彈性伺服器] 頁面上，按一下前端面板中的 [ **高可用性** ] 以開啟 [高可用性] 頁面。

3.  按一下 [**區域冗余高可用性**] 核取方塊以停用此選項，然後按一下 [ **儲存**]   以儲存變更。

4.  確認對話方塊將會顯示，您可以在其中確認停用 HA。

5.  按一下 [ **停用 HA** ] 按鈕以停用高可用性。

6.  通知將會顯示高可用性部署的解除委任正在進行中。

## <a name="next-steps"></a>下一步

-   瞭解 [商務持續性](./concepts-business-continuity.md)
-   深入瞭解 [區域冗余高可用性](./concepts-high-availability.md)
