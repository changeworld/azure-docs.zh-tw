---
title: 如何建立支援票證
description: 如何在 Azure Synapse 分析中建立支援票證。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195658"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>如何建立 Azure Synapse 分析的支援票證
如果您有任何 Azure Synapse 分析的問題，請建立支援票證，讓工程支援小組可以協助您。

## <a name="create-a-support-ticket"></a>建立支援票證
1. 開啟 [Azure 入口網站](https://portal.azure.com/)。
1. 在 [首頁] 畫面上，按一下 [說明 + 支援] 索引標籤。
   
    ![說明 + 支援](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. 檢查您的[Azure 支援計畫](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。
   
   * **帳單、配額及訂用帳戶管理**支援適用於所有支援層級。
   * 協助**修正**支援是透過[開發人員](https://azure.microsoft.com/support/plans/developer/)、[標準](https://azure.microsoft.com/support/plans/standard/)、[專業指導](https://azure.microsoft.com/support/plans/prodirect/)或[頂級](https://azure.microsoft.com/support/plans/premier/)支援來提供。 客戶在使用 Azure 期間如果遇到可合理認為是 Microsoft 所造成的問題，這類問題即屬於可協助修正的問題。
   * **開發人員指導**與**諮詢服務**可在[專業指導](https://azure.microsoft.com/support/plans/prodirect/)和[頂級](https://azure.microsoft.com/support/plans/premier/)支援層級取得。 
     
     如果您有頂級支援方案，您也可以在[Microsoft Premier 線上入口網站](https://premier.microsoft.com/)回報 SQL 資料倉儲相關問題。 若要深入瞭解各種支援方案，包括範圍、回應時間、價格等，請參閱[Azure 支援計畫](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。 如需有關 Azure 支援的常見問題，請參閱[Azure 支援常見問題](https://azure.microsoft.com/support/faq/)。
1. 在 [說明 **+ 支援**] 頁面上，選取 [**新增支援要求**]。 從下拉式功能表中選取 [問題類型]。 然後繼續填寫 [**基本**] 索引標籤中的資訊。輸入問題的**摘要**，然後從功能表中選取**問題類型**，然後選取 [儲存]。

    ![說明 + 支援](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > 根據預設，每個 SQL Server (例如 myserver.database.windows.net) 的 [DTU 配額] 為 45,000。 此配額僅是安全限制。 您可以藉由建立支援票證，並選取 *配額* 做為要求類型來增加配額。 若要計算 DTU 需求，請將7.5 乘以所需的總[DWU](sql-data-warehouse-overview-what-is.md) 。 例如，如果您想要在一個 SQL Server 上裝載兩個 DW6000，則應該要求 90,000 的 DTU 配額。  您可以在入口網站的 [SQL Server] 刀鋒視窗中檢視目前的 DTU 耗用量。 已暫停和未暫停的資料庫都會計入 DTU 配額。 
   > 

1. 您可能會看到協助您解決問題的解決方案。 如果呈現的解決方案無法解決您的問題，請選取 **[下一步：詳細資料]** 。 提交問題的詳細資料和您的連絡人資訊。 選取 **[下一步]： [查看 + 建立
]** ![詳細資料](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. 請檢查您的資訊，然後選取表單底部的 [**建立**] 以提交支援要求。

## <a name="monitor-a-support-ticket"></a>監視支援票證
提交支援要求之後，Azure 支援小組會與您聯繫。 若要檢查您的要求狀態和詳細資料，請按一下儀表板上的 [所有支援要求]。

![檢查狀態](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>其他資源
您也可以透過[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)或[Azure SQL 資料倉儲 MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)，與 SQL 資料倉儲的社區進行連接。

 
