---
title: 管理您的帳戶 - Azure 批處理 |微軟文件
description: 瞭解 Azure 批次處理帳戶的項目
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
ms.date: 03/30/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecae47f6aa0ab3f179632467b7da7805f06162d6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397269"
---
# <a name="manage-your-batch-account"></a>管理您的批次帳戶

批次帳戶是批次服務內唯一識別的實體。 所有處理都與 Batch 帳戶相關聯。

您可以使用 [Azure 入口網站](batch-account-create-portal.md)或以程式設計的方式建立 Azure Batch 帳戶，例如使用 [Batch管理 .NET 程式庫](batch-management-dotnet.md)。 建立帳戶時，您可以將 Azure 儲存體帳戶產生關聯，以儲存作業相關的輸入和輸出資料或應用程式。

您可以在單一 Batch 帳戶中執行多個 Batch 工作負載，或在相同訂用帳戶 (但不同 Azure 區域) 的 Batch 帳戶之間散佈工作負載。

## <a name="components-of-the-batch-account"></a>批次處理帳戶的元件

Batch 帳戶使您能夠在 Azure 中高效地運行大規模並行和高性能計算 (HPC) 批處理作業。 在您管理的帳戶中:

- 執行的應用程式

- 池內池和節點的分配

- 工作的數量和類型 

- 數據的輸入和輸出。 您無需安裝其他軟體來管理任務。

- 建立 Batch 帳戶時,系統會要求您為其分配名稱。 此名稱是其 ID,分配後無法更改。

- 要更改帳戶的名稱,您需要將其刪除並創建新的 Batch 帳戶。

- 帳戶是在要使用的訂閱中創建的。

- 使用 該帳戶從訂閱中的任何 Batch 帳戶標識和檢索主帳戶密鑰和輔助帳戶金鑰。

- 該帳戶維護有關池分配和核心配額的資訊。  

- 該帳戶包含位置資訊。

- 該帳戶標識您的存儲帳戶。

## <a name="next-steps"></a>後續步驟

- 使用[Azure 門戶](batch-account-create-portal.md)創建批處理帳戶。
- 以程式設計方式建立批次處理帳戶,例如使用[批次處理管理 .NET 函式庫](batch-management-dotnet.md)。
- [設定或關閉 Azure 批次處理的處理的處理到 Azure 處理的處理的遠端存取](pool-endpoint-configuration.md)。
- [在 Batch 計算節點上執行作業準備和作業解除工作](batch-job-prep-release.md)

