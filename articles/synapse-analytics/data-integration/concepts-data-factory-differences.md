---
title: 與 Azure Data Factory 的差異
description: 瞭解 Azure Synapse Analytics 的資料整合功能與 Azure Data Factory 的不同之處
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 10f5336dd4c8a02acf623b1b14226ca676006953
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357644"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics 與 Azure Data Factory 中的資料整合

在 Azure Synapse Analytics 中，資料整合功能（例如 Synapse 管線和資料流程）是以 Azure Data Factory 的方式為基礎。 如需詳細資訊，請參閱 [什麼是 Azure Data Factory](../../data-factory/introduction.md)。 幾乎所有的功能完全相同或類似，而且檔會在這兩個服務之間共用。 本文將重點放在 Azure Data Factory 與 Azure Synapse 之間的差異，並找出其目前的差異。

若要查看 Azure Data Factory 功能或文章是否適用于 Azure Synapse，請檢查文章頂端的 [標記]。

![適用于標記](../media/concepts-data-factory-differences/applies-to-moniker.png "適用于標記")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>未針對 Azure Synapse 規劃的 Azure Data Factory 功能

以下是 Azure Data Factory 中提供的功能，但不是針對 Azure Synapse 所規劃。

* **Life 和 SHIFT SSIS 套件：** 在 Azure Data Factory 中，您可以使用 SSIS 整合執行時間來隨即轉移 SSIS 套件。 SSIS 整合執行時間和執行 SSIS 套件活動都無法在 Synapse 工作區中使用。 
* **存留時間：** 「存留時間」是 Azure integration runtime 中的一項設定，可讓 spark 叢集在對應資料流程中的時間點，在資料流程完成後隨時保持 *暖* 。 Synapse 工作區中無法使用此功能。

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Data Factory 中不支援的 Azure Synapse 功能

下列功能可在 Azure Synapse 中使用，但未規劃 Azure Data Factory。

* **Spark 作業監視對應的資料流程：** 在 Synapse 中，Spark 引擎包含在使用者的訂用帳戶中，因此使用者可以查看詳細的 Spark 記錄。 在 Azure Data Factory 中，會在 Azure Data Factory 管理的 Spark 叢集上執行作業，而且無法使用這項資訊。 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory 在 Synapse 中以不同方式運作的功能

下列功能的行為不同，或目前不存在於 Azure Synapse 中。 

* **整頓資料流程：** 整頓資料流程活動目前僅適用于 Azure Data Factory。
* **解決方案範本資源庫：** 在 Azure Data Factory 中，使用者可以在 [方案範本資源庫] 中找到管線範本。 在 Synapse 工作區中，知識中心包含一組不同的範本，以及其他資料集和 SQL 腳本。 
* **Git 整合和原生 CI/CD 解決方案：** 目前，Synapse 工作區無法連線到 Git 存放庫，也不會遵循與 Azure Data Factory 相同的持續整合和傳遞程式。
* **與 Azure 監視器整合：** 如同 Azure Data Factory，Synapse 工作區不會與 Azure 監視器整合。
* **混合式整合運行** 時間設定：在 Synapse 工作區中，使用者不能同時有受管理的 VNet IR 和 Azure IR。 Azure Data Factory 支援這項功能。
* **整合執行時間共用：** 在 Synapse 工作區之間無法共用自我裝載整合執行時間。 Azure Data Factory 支援這項功能。
* **資料流程的跨區域整合執行時間：** 資料流程無法在不同區域（Synapse 工作區）的整合執行時間上執行。 Azure Data Factory 支援這項功能。

## <a name="next-steps"></a>後續步驟

瞭解如何將 [資料內嵌至 Azure Data Lake Storage gen2 帳戶](data-integration-data-lake.md)，以開始在您的 Synapse 工作區中進行資料整合。
