---
title: 將 Data Factory 連線至 Azure Purview
description: 瞭解如何將 Data Factory 連線到 Azure 範疇
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: d1a07a3733bddc1516a4d2e19f3cac31a7702954
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944943"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>將 Data Factory 連線到 Azure 範疇 (Preview) 
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文將說明如何將 data factory 連線至 Azure 範疇，以及如何報告 ADF 活動的資料歷程複製資料、資料流程和執行 SSIS 套件。

## <a name="connect-data-factory-to-azure-purview"></a>將 data factory 連線至 Azure 範疇
Azure 範疇是新的雲端服務，可供資料使用者集中管理橫跨雲端和內部內部部署環境之資料資產的資料管理。 您可以將資料處理站連線至 Azure 範疇，且此連線可讓您利用 Azure 範疇來捕獲複製、資料流程和執行 SSIS 套件的歷程資料。 如需如何在 Azure 範疇中註冊 data factory 的詳細資料，請參閱 [如何連接 Azure Data Factory 和 Azure 範疇](../purview/how-to-link-azure-data-factory.md)。 

## <a name="report-lineage-data-to-azure-purview"></a>將歷程資料回報給 Azure 範疇
當客戶在 Azure Data Factory 中執行 [複製]、[資料流程] 或 [執行 SSIS 封裝] 活動時，客戶可以取得相依性關聯性，並在資料來源與目的地之間擁有整個工作流程處理的高階總覽。
如需如何從 Azure Data Factory 收集歷程的詳細資料，請參閱 [Data Factory](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)歷程。

## <a name="next-steps"></a>後續步驟
[目錄歷程使用者指南](../purview/catalog-lineage-user-guide.md)

[教學課程：將 Data Factory 歷程資料推送至 Azure 範疇](turorial-push-lineage-to-purview.md)