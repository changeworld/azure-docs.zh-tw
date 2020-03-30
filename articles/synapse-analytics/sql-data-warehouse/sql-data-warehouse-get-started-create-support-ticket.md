---
title: 請求增加配額並獲得支援
description: 如何在 Azure 門戶中為 Azure 同步分析創建支援請求。 請求增加配額或獲得問題解決支援。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350887"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>請求增加配額並獲得對 Azure 突觸分析的支援

本文介紹如何在 Azure 門戶中提交 Azure 突觸分析的支援票證。 此過程使您能夠請求增加配額或為工程支援小組提交技術支援請求。

## <a name="create-a-support-ticket"></a>建立支援票證

使用以下步驟從 Azure 門戶為 Azure 突觸分析創建新的支援請求。

1. 在[Azure 門戶](https://portal.azure.com)功能表上，選擇 **"説明 + 支援**"。

   !["説明+支援"連結](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. 在 **"説明+支援"** 中，選擇 **"新建支援請求**"。

    ![創建新的支援請求](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. 查看[Azure 支援計畫](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。

   * **帳單、配額及訂用帳戶管理**支援適用於所有支援層級。
   * **中斷修復**支援通過[開發人員](https://azure.microsoft.com/support/plans/developer/)、[標準](https://azure.microsoft.com/support/plans/standard/)、[專業直接](https://azure.microsoft.com/support/plans/prodirect/)或[高級](https://azure.microsoft.com/support/plans/premier/)支援提供。 客戶在使用 Azure 期間如果遇到可合理認為是 Microsoft 所造成的問題，這類問題即屬於可協助修正的問題。
   * [專業指導](https://azure.microsoft.com/support/plans/prodirect/)和[頂級](https://azure.microsoft.com/support/plans/premier/)支援層級可提供**開發顧問**和**諮詢服務**。

   如果您有高級支援計畫，還可以在[Microsoft Premier 線上門戶](https://premier.microsoft.com/)上報告 Azure 同步分析問題。 請參閱[Azure 支援計畫](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)，瞭解有關各種支援計畫（包括範圍、回應時間、定價等）詳細資訊。 有關 Azure 支援的常見問題，請參閱[Azure 支援常見問題解答](https://azure.microsoft.com/support/faq/)。

1. 對於**問題類型**，請選擇適當的問題類型。 對於中斷修復問題，請選擇 **"技術**"。 對於增加配額的請求，請選擇 **"服務和訂閱限制"（配額）。**

   ![選擇問題類型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > 本文的其餘部分側重于增加配額的請求。 但是，您也可以在此處選擇 **"技術"** 以用於問題解決支援請求。 如果選擇 **"技術**"，系統將要求您提供摘要，然後通過選擇 **"選擇問題類型"來識別問題類型**。 您可能會看到解決方案來説明解決問題。 如果提供的解決方案無法解決您的問題，請選擇 **"下一步：詳細資訊**"並填寫表單以提交支援票證。

1. 對於增加配額的請求，選擇**配額類型的** **Azure 突觸分析**。 然後選擇 **"下一步：解決方案 >>。**

   ![選擇配額類型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. 在 **"詳細資訊"** 視窗中，選擇 **"提供詳細資訊**"以輸入其他資訊。

   !["提供詳細資訊"連結](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>配額請求類型

按一下 **"提供詳細資訊**"將顯示允許您添加其他資訊的**配額詳細資訊**視窗。 以下各節介紹可用於 Azure 同步分析的不同配額請求。

### <a name="data-warehouse-units-dwus-per-server"></a>每個伺服器的資料倉儲單位 （DWU）

使用以下步驟請求增加每個伺服器的 DWU。

1. 根據伺服器配額類型選擇**資料倉儲單位 （DTUs）。**

1. 在 **"資源"** 清單中，選擇要定位的資源。

1. 在 **"請求配額"** 欄位中，輸入您請求的新 DWU 限制。

   ![DWU 配額詳細資訊](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>每個訂閱的伺服器

使用以下步驟請求增加每個訂閱的伺服器數。

1. **選擇每個訂閱**配額類型的伺服器。

1. 在 **"位置**"清單中，選擇要使用的 Azure 區域。 配額是每個區域中每個訂閱的配額。

1. 在"**新建配額"** 欄位中，輸入對該區域中伺服器最大數量的請求。

   ![伺服器配額詳細資訊](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>啟用對區域的訂閱訪問

某些產品/服務類型並非在每個區域都可用。 您可能會看到如下錯誤：

`This location is not available for subscription`

如果您的訂閱需要在特定區域進行訪問，請使用 **"其他配額請求**"選項請求訪問。 在請求中，指定要為該區域啟用的產品/服務以及 SKU 詳細資訊。 要流覽產品/服務清單和 SKU 選項，請參閱[Azure 突觸分析定價](https://azure.microsoft.com/pricing/details/synapse-analytics/)。

![其他配額詳細資訊](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>提交您的要求

最後一步是填寫 SQL 資料庫支援請求的剩餘詳細資訊。 然後選擇 **"下一步：審閱 + 創建>>**"，在查看請求詳細資訊後，按一下"**創建"** 以提交請求。

## <a name="monitor-a-support-ticket"></a>監視支援票證

提交支援請求後，Azure 支援小組將與您聯繫。 若要檢查您的要求狀態和詳細資料，請按一下儀表板上的 [所有支援要求]****。

![檢查狀態](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>其他資源

您還可以在[堆疊溢位](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse)或通過[Azure SQL 資料倉儲 MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)與 Azure 同步分析社區連接。

