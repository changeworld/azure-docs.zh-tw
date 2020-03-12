---
title: 要求配額增加並取得支援
description: 如何在適用于 Azure Synapse 分析的 Azure 入口網站中建立支援要求。 要求配額增加或取得問題解決方式支援。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88cc737fddfdd896575227e036df087c13a4d0f6
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130199"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>要求配額增加並取得 Azure Synapse 分析的支援

本文說明如何在 Azure Synapse 分析的 Azure 入口網站中提交支援票證。 此流程可讓您要求增加配額或提交技術支援要求給工程支援小組。

## <a name="create-a-support-ticket"></a>建立支援票證

使用下列步驟，從 Azure Synapse 分析的 Azure 入口網站建立新的支援要求。

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 功能表上，選取 [說明 **+ 支援**]。

   ![[說明 + 支援] 連結](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. 在 [說明 **+ 支援**] 中，選取 [**新增支援要求**]。

    ![建立新的支援要求](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. 檢查您的[Azure 支援計畫](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。

   * **帳單、配額及訂用帳戶管理**支援適用於所有支援層級。
   * 協助**修正**支援是透過[開發人員](https://azure.microsoft.com/support/plans/developer/)、[標準](https://azure.microsoft.com/support/plans/standard/)、[專業指導](https://azure.microsoft.com/support/plans/prodirect/)或[頂級](https://azure.microsoft.com/support/plans/premier/)支援來提供。 客戶在使用 Azure 期間如果遇到可合理認為是 Microsoft 所造成的問題，這類問題即屬於可協助修正的問題。
   * **開發人員指導**與**諮詢服務**可在[專業指導](https://azure.microsoft.com/support/plans/prodirect/)和[頂級](https://azure.microsoft.com/support/plans/premier/)支援層級取得。

   如果您有頂級支援方案，您也可以在[Microsoft Premier 線上入口網站](https://premier.microsoft.com/)回報 Azure Synapse 分析問題。 若要深入瞭解各種支援方案，包括範圍、回應時間、價格等，請參閱[Azure 支援計畫](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。 如需有關 Azure 支援的常見問題，請參閱[Azure 支援常見問題](https://azure.microsoft.com/support/faq/)。

1. 針對 [**問題類型**]，選取適當的問題類型。 如需協助修正問題，請選取 [**技術**]。 針對 [增加配額要求]，選取 **[服務和訂用帳戶限制（配額）** ]。

   ![選取問題類型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > 這篇文章的其餘部分著重于增加配額要求。 但您也可以在這裡選取 [**技術**] 以取得問題解決支援要求。 如果您選取 [**技術**]，系統會要求您提供摘要，然後藉由選取 [**選取問題類型**] 來識別問題類型。 您可能會看到協助您解決問題的解決方案。 如果呈現的解決方案無法解決您的問題，請選取 **[下一步：詳細資料]** ，並填寫表單以提交支援票證。

1. 針對 [配額增加要求]，選取 [ **Azure Synapse 分析**] 作為 [**配額類型**]。 然後選取 **[下一步：方案 > >]** 。

   ![選取配額類型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. 在**詳細資料**視窗中，選取 [**提供詳細資料**] 以輸入其他資訊。

   ![[提供詳細資料] 連結](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>配額要求類型

按一下 [**提供詳細資料**] 會顯示 [**配額詳細資料**] 視窗，可讓您新增額外的資訊。 下列各節說明適用于 Azure Synapse 分析的不同配額要求。

### <a name="data-warehouse-units-dwus-per-server"></a>每一伺服器的資料倉儲單位（Dwu）

使用下列步驟來要求每一伺服器的 Dwu 增加。

1. 選取**每個伺服器配額類型的資料倉儲單位（dtu）** 。

1. 在**資源清單**中，選取要設為目標的資源。

1. 在 [**要求配額**] 欄位中，輸入您要要求的新 DWU 限制。

   ![DWU 配額詳細資料](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>每個訂用帳戶的伺服器

使用下列步驟來要求增加每個訂用帳戶的伺服器數目。

1. 選取 [**每個訂**用帳戶的伺服器] 配額類型。

1. 在 [**位置**] 清單中，選取要使用的 Azure 區域。 配額是每個區域中的每個訂用帳戶。

1. 在 [**新配額**] 欄位中，輸入您的要求，以取得該區域中的伺服器數目上限。

   ![伺服器配額詳細資料](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>啟用區域的訂用帳戶存取

某些供應專案類型無法在每個區域中使用。 您可能會看到如下的錯誤：

`This location is not available for subscription`

如果您的訂用帳戶需要特定區域的存取權，請使用 [**其他配額要求**] 選項來要求存取權。 在您的要求中，指定您想要為區域啟用的供應專案和 SKU 詳細資料。 若要探索供應專案和 SKU 選項，請參閱[Azure Synapse Analytics 定價](https://azure.microsoft.com/pricing/details/synapse-analytics/)。

![其他配額詳細資料](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>提交您的要求

最後一個步驟是填入 SQL Database 支援要求的剩餘詳細資料。 然後選取 **[下一步]： [檢查 + 建立 > >]** ，然後在查看要求詳細資料之後，按一下 [**建立**] 以提交要求。

## <a name="monitor-a-support-ticket"></a>監視支援票證

提交支援要求之後，Azure 支援小組會與您聯繫。 若要檢查您的要求狀態和詳細資料，請按一下儀表板上的 [所有支援要求]。

![檢查狀態](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>其他資源

您也可以在[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse)上，或透過[Azure SQL 資料倉儲的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)，與 Azure Synapse 分析的社區進行連接。

