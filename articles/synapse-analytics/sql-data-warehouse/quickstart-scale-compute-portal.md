---
title: 調整 Synapse SQL 集區的計算 (Azure 入口網站)
description: 您可以使用 Azure 入口網站來調整 Synapse SQL 集區 (資料倉儲) 的計算。
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/28/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d944ff86cdc11c30142792c6fd15d03e3cfd4fa9
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212933"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>快速入門：使用 Azure 入口網站調整 Synapse SQL 集區的計算

您可以使用 Azure 入口網站來調整 Synapse SQL 集區 (資料倉儲) 的計算。 [擴增計算](sql-data-warehouse-manage-compute-overview.md)以提升效能，或將計算調整回來以節省成本。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="before-you-begin"></a>開始之前

您可以調整您已有的 SQL 集區，也可以使用[快速入門：建立與連線 - 入口網站](create-data-warehouse-portal.md)建立名為 **mySampleDataWarehouse** 的 SQL 集區。 本快速入門會調整 **mySampleDataWarehouse**。

>[!IMPORTANT] 
>您的 SQL 集區必須在線上才能進行調整。 

## <a name="scale-compute"></a>調整計算

您可以藉由增加或減少資料倉儲單位來調整 SQL 集區計算資源。 [快速入門：建立與連線 - 入口網站](create-data-warehouse-portal.md)已建立 **mySampleDataWarehouse**，並以 400 個 DWU 加以初始化。 下列步驟會調整 **mySampleDataWarehouse** 的 DWU。

若要變更資料倉儲單位：

1. 按一下 Azure 入口網站左側頁面中的 [Azure Synapse Analytics (先前為 SQL DW)]。
2. 從 [Azure Synapse Analytics (先前為 SQL DW)] 頁面中選取 [mySampleDataWarehouse]。 SQL 集區隨即開啟。
3. 按一下 [調整] 。

    ![按一下 [調整]](./media/quickstart-scale-compute-portal/click-scale.png)

2. 在 [調整] 面板中，將滑桿向左或右移動來變更 DWU 設定。 然後，選取 [調整]。

    ![移動滑桿](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>後續步驟
若要深入了解 SQL 集區，請繼續[將資料載入 SQL 集區](load-data-from-azure-blob-storage-using-polybase.md)的教學課程。 
