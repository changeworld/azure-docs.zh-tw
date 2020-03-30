---
title: 管理您的帳戶 - Azure 批次處理 |微軟文檔
description: 瞭解 Azure 批次處理帳戶的組成內容
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4df2ec0439e659fd8dc1448c6209c9718114791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479785"
---
# <a name="manage-your-batch-account"></a>管理您的批次帳戶

批次帳戶是批次服務內唯一識別的實體。 所有處理都與 Batch 帳戶相關聯。

您可以使用 [Azure 入口網站](batch-account-create-portal.md)或以程式設計的方式建立 Azure Batch 帳戶，例如使用 [Batch管理 .NET 程式庫](batch-management-dotnet.md)。 建立帳戶時，您可以將 Azure 儲存體帳戶產生關聯，以儲存作業相關的輸入和輸出資料或應用程式。

您可以在單一 Batch 帳戶中執行多個 Batch 工作負載，或在相同訂用帳戶 (但不同 Azure 區域) 的 Batch 帳戶之間散佈工作負載。

## <a name="components-of-the-batch-account"></a>批次處理帳戶的元件

Batch 帳戶使您能夠在 Azure 中高效地運行大規模並行和高性能計算 （HPC） 批次處理作業。 在您管理的帳戶中：

- 正在運行的應用程式

- 池內池和節點的分配

- 任務的數量和類型 

- 資料的輸入和輸出。 您無需安裝其他軟體來管理工作。

- 創建 Batch 帳戶時，系統會要求您為其分配名稱。 此名稱是其 ID，分配後無法更改。

- 要更改帳戶的名稱，您需要將其刪除並創建新的 Batch 帳戶。

- 帳戶是在要使用的訂閱中創建的。

- 使用 該帳戶從訂閱中的任何 Batch 帳戶標識和檢索主帳戶金鑰和輔助帳戶金鑰。

- 該帳戶維護有關池分配和核心配額的資訊。  

- 該帳戶包含位置資訊。

- 該帳戶標識您的存儲帳戶。

## <a name="next-steps"></a>後續步驟

- 使用[Azure 門戶](batch-account-create-portal.md)創建批次處理帳戶。
- 以程式設計方式創建批次處理帳戶，例如使用[批次處理管理 .NET 庫](batch-management-dotnet.md)。
- [配置或禁用對 Azure 批次處理池中的計算節點的遠端存取](pool-endpoint-configuration.md)。
- [在 Batch 計算節點上執行作業準備和作業解除工作](batch-job-prep-release.md)

