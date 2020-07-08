---
title: 要求增加配額及取得支援
description: 如何在適用於 Azure Synapse Analytics 的 Azure 入口網站中建立支援要求。 要求增加配額或取得問題解決方式支援。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: de45e338b0b863dc2364af399a6991f56658b0e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212270"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>要求增加配額及取得 Azure Synapse Analytics 的支援

本文說明如何在適用於 Azure Synapse Analytics 的 Azure 入口網站中提交支援票證。 此程序可讓您要求增加配額或提交技術支援要求給工程支援小組。

## <a name="create-a-support-ticket"></a>建立支援票證

使用下列步驟，從適用於 Azure Synapse Analytics 的 Azure 入口網站建立新的支援要求。

1. 在 [Azure 入口網站](https://portal.azure.com)功能表上，選取 [説明 + 支援]。

   ![[説明 + 支援] 連結](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. 在 [說明 + 支援] 中，選取 [新增支援要求]。

    ![建立新的支援要求](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. 檢閱您的 [Azure 支援計劃](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。

   * **帳單、配額及訂用帳戶管理**支援適用於所有支援層級。
   * **協助修正**支援則透過[開發人員](https://azure.microsoft.com/support/plans/developer/)、[標準](https://azure.microsoft.com/support/plans/standard/)、[專業直接](https://azure.microsoft.com/support/plans/prodirect/)支援或[頂級](https://azure.microsoft.com/support/plans/premier/)支援來提供。 客戶在使用 Azure 期間如果遇到可合理認為是 Microsoft 所造成的問題，這類問題即屬於可協助修正的問題。
   * [專業直接支援](https://azure.microsoft.com/support/plans/prodirect/)和[頂級](https://azure.microsoft.com/support/plans/premier/)支援層級可提供**開發顧問**和**諮詢服務**。

   如果您有頂級支援計劃，您也可以在 [Microsoft Premier 線上入口網站](https://premier.microsoft.com/)回報 Azure Synapse Analytics 的相關問題。 請參閱 [Azure 支援計劃](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)，進一步了解包括範圍、回應時間、價格等各種 Azure 支援計劃。如需有關 Azure 支援的常見問題集，請參閱 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

1. 針對**問題類型**，請選取適當的問題類型。 針對協助修正問題，請選取 [技術]。 針對增加配額要求，選取 [服務與訂用帳戶限制 (配額)]。

   ![選取問題類型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > 本文的其餘部分著重於增加配額要求。 但是您也可以在這裡選取 [技術] 以提出解決問題支援要求。 如果您選取 [技術]，系統會要求您提供摘要，然後選取 [選取問題類型] 來識別問題類型。 您可能會看到可協助您解決問題的解決方案。 如果呈現的解決方案無法解決您的問題，請選取 [下一步：詳細資料] 並填寫表單以提交支援票證。

1. 針對增加配額要求，對 [配額類型] 選取 [Azure Synapse Analytics]。 然後，選取 **[下一步：解決方案 >>]** 。

   ![選取配額類型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. 在**詳細資料**視窗中，選取 [**輸入詳細資料**] 以輸入其他資訊。

   ![「提供詳細資料」連結](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>配額要求類型

選取 [**輸入詳細資料**] 會顯示 [**配額詳細資料**] 視窗，可讓您新增額外的資訊。 下列各節說明適用於 Azure Synapse Analytics 的不同配額要求。

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>每個伺服器的 Synapse SQL 集區資料倉儲單位（Dwu）

使用下列步驟來要求每個伺服器增加 DWU。

1. 選取每個伺服器配額類型的 [ **SYNAPSE SQL 集區 dwu** ]。

1. 使用下拉式清單，選取您想要套用配額增加的**資源**。

1. 在 [**要求配額**] 區段中輸入新的配額。

1. 選取 [儲存並繼續]  。

   ![DWU 配額詳細資料](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>每個訂用帳戶的伺服器

若要要求每個訂用帳戶的伺服器數目增加，您必須完成下列步驟：

1. 選取 [**每個訂**用帳戶的 SQL server] 作為配額類型。

1. 在 [位置] 清單中，選取要使用的 Azure 區域。 配額是各個區域中的每個訂用帳戶。

1. 在 [**要求配額**] 欄位中，輸入您的要求，以取得該區域中的伺服器數目上限。

   ![伺服器配額詳細資料](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. 選取 [儲存並繼續]  。

某些供應專案類型無法在每個區域中使用。 您可能會看到下列錯誤：

![區域存取錯誤](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>啟用區域的訂用帳戶存取

若要啟用訂用帳戶的區域存取，您必須完成下列步驟：  

1. 選取 [ **SYNAPSE SQL 集區（資料倉儲）] 區域存取**配額類型。

1. 從下拉式清單中選擇 [**位置**]，以選取 [區域]。

1. 在 [**需要 DWU** ] 區段中指出您的 DWU 效能需求。

1. 輸入您**的商務需求描述**。 

1. 選取 [儲存並繼續]  。

![區域存取](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>針對其他配額要求

從其他配額要求類型的 [配額類型] 下拉式功能表中，選取 [**其他配額要求**]：

![其他配額詳細資料](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>提交您的要求

最後一個步驟是填入 SQL Database 支援要求的剩餘詳細資料。 然後選取 **[下一步：審查 + 建立>>] **。

![審查建立詳細資料](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

查看要求詳細資料之後，請選取 [**建立**] 以提交要求。

![建立票證](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>監視支援票證

在您提交支援要求之後，Azure 支援小組會與您連絡。 若要檢查您的要求狀態和詳細資料，請選取儀表板上的 [**所有支援要求**]。

![檢查狀態](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>其他資源

您也可以在[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse)上，或透過[azure Synapse Analytics 的 Microsoft Q&問題頁面](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)，連接到 azure Synapse 分析的論壇。

