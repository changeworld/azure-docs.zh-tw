---
title: 從 Azure Blob 儲存體來回移動資料 - Team Data Science Process
description: 使用 Azure 儲存體總管、AzCopy、Python 和 SSIS 將資料移入和移出 Azure Blob 儲存體。
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
ms.openlocfilehash: 5148084fa22266b1352046c7d8737b9804c5f4d0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311846"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>從 Azure Blob 儲存體來回移動資料

Team Data Science Process 要求將資料內嵌或載入至各種不同的儲存體環境，以便在程序的每個階段中以最適當的方式處理或分析。

## <a name="different-technologies-for-moving-data"></a>資料移動的各種技術

下列文章說明如何使用不同的技術，從 Azure Blob 儲存體來回移動資料。

* [Azure 儲存體總管](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
* [Python](../../storage/blobs/storage-quickstart-blobs-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

哪一種方法最適合會取決於您的案例。 [在 Azure 機器學習中的進階分析案例](plan-sample-scenarios.md) 文章可協助您判斷進階分析程序中各種資料科學工作流程所需的資源。

> [!NOTE]
> 如需 Azure Blob 儲存體的完整介紹，請參閱 [Azure Blob 基本概念](../../storage/blobs/storage-quickstart-blobs-dotnet.md)和 [Azure Blob 服務](/rest/api/storageservices/Blob-Service-Concepts)。
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
本文章假設您擁有 Azure 訂用帳戶、儲存體帳戶和該帳戶的對應儲存體金鑰。 上傳/下載資料之前，您必須知道您的 Azure 儲存體帳戶名稱和帳戶金鑰。

* 若要設定 Azure 訂用帳戶，請參閱 [免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。
* 如需有關建立儲存體帳戶，以及取得帳戶和金鑰資訊的指示，請參閱 [關於 Azure 儲存體帳戶](../../storage/common/storage-account-create.md)。