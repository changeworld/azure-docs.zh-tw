---
title: 從 Azure Blob 儲存體來回移動資料 - Team Data Science Process
description: 使用 Azure 儲存體總管、AzCopy、Python 和 SSIS，從 Azure Blob 儲存體來回移動資料。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76717582"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>從 Azure Blob 儲存體來回移動資料

Team Data Science Process 要求將資料內嵌或載入至各種不同的儲存體環境，以便在程序的每個階段中以最適當的方式處理或分析。

## <a name="different-technologies-for-moving-data"></a>資料移動的各種技術

下列文章說明如何使用不同的技術，從 Azure Blob 儲存體來回移動資料。

* [Azure 儲存體總管](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

哪一種方法最適合會取決於您的案例。 [在 Azure 機器學習中的進階分析案例](plan-sample-scenarios.md) 文章可協助您判斷進階分析程序中各種資料科學工作流程所需的資源。

> [!NOTE]
> 如需 Azure Blob 儲存體的完整介紹，請參閱 [Azure Blob 基本概念](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。
> 
> 

## <a name="using-azure-data-factory"></a>使用 Azure Data Factory

或者，您可以使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 來︰ 

* 建立和排程管線，以從 Azure Blob 儲存體下載資料， 
* 將其傳遞至已發佈的 Azure Machine Learning Web 服務， 
* 接收預測性分析結果，並 
* 將結果上傳至儲存體。 

如需詳細資訊，請參閱 [使用 Azure Data Factory 和 Azure Machine Learning 來建立預測管線](../../data-factory/transform-data-using-machine-learning.md)。

## <a name="prerequisites"></a>必要條件
本文章假設您擁有 Azure 訂用帳戶、儲存體帳戶和該帳戶的對應儲存體金鑰。 上傳/下載資料之前，您必須知道 Azure 儲存體帳戶名稱和帳戶金鑰。

* 若要設定 Azure 訂用帳戶，請參閱[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。
* 如需有關建立儲存體帳戶以及取得帳戶和金鑰資訊的指示，請參閱[關於 Azure 儲存體帳戶](../../storage/common/storage-create-storage-account.md)。

