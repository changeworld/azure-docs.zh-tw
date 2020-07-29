---
title: 管理您的帳戶
description: 瞭解 Azure Batch 帳戶的組成項目
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722961"
---
# <a name="manage-your-batch-account"></a>管理您的 Batch 帳戶

批次帳戶是批次服務內唯一識別的實體。 所有處理都與 Batch 帳戶相關聯。

您可以使用 [Azure 入口網站](batch-account-create-portal.md)或以程式設計的方式建立 Azure Batch 帳戶，例如使用 [Batch管理 .NET 程式庫](batch-management-dotnet.md)。 建立帳戶時，您可以將 Azure 儲存體帳戶產生關聯，以儲存作業相關的輸入和輸出資料或應用程式。

您可以在單一 Batch 帳戶中執行多個 Batch 工作負載，或在相同訂用帳戶 (但不同 Azure 區域) 的 Batch 帳戶之間散佈工作負載。

## <a name="components-of-the-batch-account"></a>Batch 帳戶的元件

Batch 帳戶能夠在 Azure 中有效率地執行大規模的平行和高效能運算 (HPC) 批次作業。 在您管理的帳戶內：

- 您正在執行的應用程式

- 集區和集區中的節點配置

- 工作的數目和類型 

- 資料的輸入和輸出。 您都不需要安裝額外的軟體來管理工作。

- 在您建立 Batch 帳戶時，系統會要求您為其指派名稱。 這個名稱是其識別碼，一旦指派之後，就無法變更。

- 若要變更帳戶的名稱，您必須將其刪除，並建立新的 Batch 帳戶。

- 帳戶會在您要使用的訂用帳戶內建立。

- 使用帳戶識別和擷取訂用帳戶之中任何 Batch 帳戶的主要和次要帳戶金鑰。

- 帳戶會維護集區配置和核心配額的相關資訊。  

- 帳戶包含位置資訊。

- 帳戶會識別您的儲存體帳戶。

## <a name="next-steps"></a>後續步驟

- 使用 [Azure 入口網站](batch-account-create-portal.md)建立儲存體帳戶。
- 以程式設計的方式建立 Batch 帳戶，例如使用 [Batch 管理 .NET 程式庫](batch-management-dotnet.md)。
- [設定或停用 Azure Batch 集區內計算節點的遠端存取權](pool-endpoint-configuration.md)。
- [在 Batch 計算節點上執行作業準備和作業解除工作](batch-job-prep-release.md)

