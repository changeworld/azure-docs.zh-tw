---
title: 調整作業-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器
description: 本文說明如何透過 Azure 入口網站在適用於 PostgreSQL 的 Azure 資料庫中執行調整作業。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934374"
---
# <a name="scale-operations-in-flexible-server"></a>彈性伺服器的調整規模作業

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

本文提供執行計算和儲存體調整作業的步驟。 您將能夠變更高載、一般用途和記憶體優化 Sku 之間的計算層，包括選擇適合用來執行應用程式的虛擬核心數目。 您也可以擴大儲存體。 預期的 IOPS 會根據計算層、虛擬核心和儲存體容量顯示。 成本預估也會根據您的選擇來顯示。

> [!IMPORTANT]
> 您無法縮減儲存體。

## <a name="pre-requisites"></a>必要條件

若要完成本操作說明指南，您需要：

-   您必須有適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。 相同的程式也適用于以區域冗余設定的彈性伺服器。
> [!IMPORTANT]
> 當設定為具有高可用性時，您無法選擇高載 SKU。 在調整作業期間，會先將待命調整為所需的大小、主伺服器已容錯移轉，並調整主要伺服器。 

## <a name="scaling-the-compute-tier-and-size"></a>調整計算層和大小

請遵循下列步驟來選擇計算層。
 
1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要從中還原備份的彈性伺服器。

2.  按一下 [ **計算 + 儲存體**]。

3.  隨即顯示具有目前設定的頁面。
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="計算 + 儲存體視圖":::

4.  您可以選擇高載、一般用途和記憶體優化層之間的計算類別。
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="列出計算層":::


5.  如果您適合使用預設的虛擬核心和記憶體大小，可以略過下一個步驟。

6.  如果您想要變更虛擬核心數目，您可以按一下 [ **計算大小** ] 下拉式清單，然後從清單中按一下所需的虛擬核心/記憶體數目。
    
    - 高載計算層： :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="高載計算":::

    - 一般目的計算層： :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="一般目的計算":::

    - 記憶體優化計算層： :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="記憶體優化計算":::

7.  按一下 [檔案]  。 
8.  您將會看見確認訊息。 如果您想要繼續，請按一下 **[確定]** 。 
9.  正在進行調整作業的通知。


## <a name="scaling-storage-size"></a>調整儲存體大小

請遵循下列步驟來增加儲存體大小。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要增加儲存體大小的彈性伺服器。
2.  按一下 [ **計算 + 儲存體**]。

3.  隨即顯示具有目前設定的頁面。
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="按一下 [計算 + 儲存體]":::
4.  具有滑動軸的 **GiB 欄位儲存大小** 會顯示為目前的大小。

5.  將橫條滑到您想要的大小。 顯示對應的 IOPS 數目。 IOPS 取決於計算層和大小。 此外也會顯示成本資訊。 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="儲存體擴大":::

6.  如果您適合儲存體大小，請按一下 [ **儲存**]。 
7.  您將會看見確認訊息。 如果您想要繼續，請按一下 **[確定]** 。 
8.  正在進行調整作業的通知。

## <a name="next-steps"></a>下一步

-   瞭解 [商務持續性](./concepts-business-continuity.md)
-   深入瞭解 [高可用性](./concepts-high-availability.md)
-   瞭解[備份和](./concepts-backup-restore.md)復原
