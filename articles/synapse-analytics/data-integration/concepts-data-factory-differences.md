---
title: 與 Azure Data Factory 的差異
description: 瞭解 Azure Synapse Analytics 的資料整合功能與 Azure Data Factory 的不同之處
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 8818d4db489cef8203ae515c18c61e215d577033
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387610"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics 與 Azure Data Factory 中的資料整合

在 Azure Synapse Analytics 中，資料整合功能（例如 Synapse 管線和資料流程）是以 Azure Data Factory 的方式為基礎。 如需詳細資訊，請參閱 [什麼是 Azure Data Factory](../../data-factory/introduction.md)。


## <a name="available-features-in-adf--azure-synapse-analytics"></a>ADF & 中的可用功能 Azure Synapse Analytics

請參閱下表以取得功能可用性：

| 類別                 | 功能    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | 使用 SSIS 和 SSIS Integration Runtime | ✓ | ✗ |
|                          | 支援跨區域 Integration Runtime (的資料流程)  | ✓ | ✗ |
|                          | Integration Runtime 共用 | ✓<br><small>*可以跨不同的資料處理站共用* | ✗ |
|                          | 存留時間 | ✓ | ✗ |
| **管線活動** | SSIS 封裝活動 | ✓ | ✗ |
|                          | 支援 Power Query 活動 | ✓ | ✓ |
| **範本庫和知識中心** | 解決方案範本 | ✓<br><small>*Azure Data Factory 範本資源庫* | ✓<br><small>*Synapse 工作區知識中心* |
| **GIT 存放庫整合** | GIT 整合 | ✓ | ✓ |
| **監視**           | 監視資料流程的 Spark 作業 | ✗ | ✓<br><small>*利用 Synapse Spark 集區* |
|                          | 與 Azure 監視器整合 | ✓ | ✗ |

> [!Note]
> **存留時間** 是一種 Azure Integration Runtime 設定，可讓 Spark 叢集在執行資料流程之後隨時 *保持暖* 。
>


## <a name="next-steps"></a>後續步驟

瞭解如何將 [資料內嵌至 Azure Data Lake Storage gen2 帳戶](data-integration-data-lake.md)，以開始在您的 Synapse 工作區中進行資料整合。
