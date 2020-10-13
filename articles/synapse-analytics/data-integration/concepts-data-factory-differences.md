---
title: 與 Azure Data Factory 的差異
description: 瞭解 Azure Synapse Analytics 的資料整合功能與 Azure Data Factory 的不同之處
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341890"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics 與 Azure Data Factory 中的資料整合

在 Azure Synapse Analytics 中，資料整合功能（例如 Synapse 管線和資料流程）是以 Azure Data Factory 的方式為基礎。 如需詳細資訊，請參閱 [什麼是 Azure Data Factory](../../data-factory/introduction.md)。 幾乎所有的功能完全相同或類似，而且檔會在這兩個服務之間共用。 本文將重點放在 Azure Data Factory 與 Azure Synapse 之間的差異，並找出其目前的差異。

若要查看 Azure Data Factory 功能或文章是否適用于 Azure Synapse，請檢查文章頂端的 [標記]。

![適用于標記](../media/concepts-data-factory-differences/applies-to-moniker.png "適用于標記")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>未針對 Azure Synapse 規劃的 Azure Data Factory 功能

以下是 Azure Data Factory 中提供的功能，但不是針對 Azure Synapse 所規劃。

* 隨即轉移 SSIS 套件的能力。
* 雪花式做為複製活動中的接收，以及對應的資料流程。
* Azure 整合執行時間的對應資料流程存留時間設定。

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Data Factory 中不支援的 Azure Synapse 功能

下列功能可在 Azure Synapse 中使用，但未規劃 Azure Data Factory。

* 對應資料流程的 Spark 作業監視僅適用于 Synapse。 在 Synapse 中，Spark 引擎包含在使用者的訂用帳戶中，因此使用者可以查看詳細的 Spark 記錄。 在 Azure Data Factory 中，會在 Azure Data Factory 管理的 Spark 叢集上執行作業。 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory 在 Synapse 中以不同方式運作的功能

下列功能的行為不同，或目前不存在於 Azure Synapse 中。 

* 整頓資料流程
* 解決方案範本資源庫
* Git 整合和原生 CI/CD 解決方案
* 與 Azure 監視器整合
* 在發佈後重新命名資源
* Synapse 工作區中的混合式整合執行時間設定。 使用者不能同時有受管理的 VNet IR 和 Azure IR。
* Synapse 工作區之間的整合執行時間共用

## <a name="next-steps"></a>後續步驟

瞭解如何將 [資料內嵌至 Azure Data Lake Storage gen2 帳戶](data-integration-data-lake.md)，以開始在您的 Synapse 工作區中進行資料整合。
