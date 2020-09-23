---
title: 重新開機-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器
description: 本文說明如何透過 Azure 入口網站在適用於 PostgreSQL 的 Azure 資料庫中執行重新開機作業。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933839"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>重新開機適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

本文提供逐步執行的程式，可讓您重新開機彈性的伺服器。 這項作業適用于套用需要重新開機資料庫伺服器的任何靜態參數變更。 此程式與使用區域冗余高可用性設定的伺服器相同。 

> [!IMPORTANT]
> 當設定為具有高可用性時，會同時重新開機主要和待命伺服器。

## <a name="pre-requisites"></a>必要條件

若要完成本操作說明指南，您需要：

-   您必須擁有有彈性的伺服器。

## <a name="restart-your-flexible-server"></a>重新開機您的彈性伺服器

請遵循下列步驟來重新開機您的彈性伺服器。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要重新開機的彈性伺服器。

2.  從左面板中按一下 **[總覽** ]，然後按一下 [ **重新開機**]。
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="重新開機選取專案":::

3.  彈出確認訊息隨即出現。

4.  如果您想要繼續，請按一下 **[是]** 。
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="重新開機確認":::
 
6.  將會顯示已起始重新開機作業的通知。

## <a name="next-steps"></a>下一步

-   瞭解 [商務持續性](./concepts-business-continuity.md)
-   深入瞭解 [區域冗余高可用性](./concepts-high-availability.md)
