---
title: 快速入門：透過 Azure 入口網站暫停和繼續專用 SQL 集區中的計算
description: 使用 Azure 入口網站暫停專用 SQL 集區的計算以節省成本。 在您準備好使用資料倉儲時繼續計算。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7d258243dae06461d21e9b5f0346e419f034eea9
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109647"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>快速入門：透過 Azure 入口網站暫停和繼續專用 SQL 集區中的計算

您可以使用 Azure 入口網站來暫停和繼續專用 SQL 集區計算資源。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="before-you-begin"></a>開始之前

使用 [建立與連線 - 入口網站](../quickstart-create-sql-pool-portal.md)建立名為 **mySampleDataWarehouse** 的專用 SQL 集區。 

## <a name="pause-compute"></a>暫停計算

為了降低成本，您可以隨選暫停和繼續計算資源。 例如，如果您在夜間和週末不會使用資料庫，可以在這段時間暫停，並且在白天時繼續。
 
>[!NOTE]
>資料庫暫停時，系統將不會向您收取計算資源的費用。 不過，您仍需持續支付儲存體費用。 

請依照下列步驟將專用 SQL 集區暫停：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 瀏覽至 [專用 SQL 集區] 頁面，以開啟 SQL 集區。 
3. 您會看到 [狀態] 為 [線上]。

    ![計算上線](././media/pause-and-resume-compute-portal/compute-online.png)

4. 若要暫停專用 SQL 集區，請按一下 [暫停] 按鈕。 
5. 螢幕會出現詢問您是否要繼續的確認問題。 按一下 [是]  。
6. 請稍候片刻，並留意到 [狀態]  將變成 [正在暫停]  。

    ![此螢幕擷取畫面顯示範例資料倉儲的 Azure 入口網站， [狀態] 值為 [暫停]。](./media/pause-and-resume-compute-portal/pausing.png)

7. 暫停作業完成後，狀態將會變成 [已暫停]  ，選項按鈕則會是 [繼續]  。
8. 專用 SQL 集區的計算資源現已離線。 在您繼續服務之前，將無需支付計算費用。

    ![計算離線](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>繼續計算

請依照下列步驟將專用 SQL 集區繼續。

1. 瀏覽至 [專用 SQL 集區] 頁面，以開啟 SQL 集區。
3. 在 [mySampleDataWarehouse]  頁面上，留意到 [狀態]  目前為 [已暫停]  。

    ![計算離線](././media/pause-and-resume-compute-portal/compute-offline.png)

1. 若要繼續 SQL 集區，請按一下 [繼續]  。 
1. 螢幕會出現詢問您是否要啟動的確認問題。 按一下 [是]  。
1. 留意到 [狀態]  將變成 [正在繼續]  。

    ![此螢幕擷取畫面顯示範例資料倉儲的 Azure 入口網站，其中已選取 [啟動] 按鈕，且 [狀態] 值為 [繼續中]。](./media/pause-and-resume-compute-portal/resuming.png)

1. SQL 集區再度上線時，狀態將會變成 [線上]  ，選項按鈕則會是 [暫停]  。
1. SQL 集區的計算資源現已上線，且您可以使用服務。 費用計算將繼續進行。

    ![計算上線](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>清除資源

您需為資料倉儲單位和儲存在專用 SQL 集區中的資料付費。 這些計算和儲存體資源會分開計費。 

- 如果您想保留儲存體中的資料，請暫停計算。
- 如果您想要移除未來的費用，可以將專用 SQL 集區刪除。 

遵循下列步驟，視需要清除資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取您的專用 SQL 集區。

    ![清除資源](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. 若要暫停計算，請按一下 [暫停]  按鈕。 

1. 若要移除專用 SQL 集區以免支付計算或儲存體的費用，請按一下 [刪除]。



## <a name="next-steps"></a>後續步驟

現在您已暫停和繼續專用 SQL 集區的計算。 請繼續閱讀下一篇文章以深入了解如何[將資料載入專用 SQL 集區](load-data-from-azure-blob-storage-using-polybase.md)。 如需管理計算功能的其他資訊，請參閱[管理計算概觀](sql-data-warehouse-manage-compute-overview.md) 一文。 

