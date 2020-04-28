---
title: 使用儲存體總管來觀看知識存放區（預覽）
titleSuffix: Azure Cognitive Search
description: 使用 Azure 入口網站的儲存體總管來檢視和分析 Azure 認知搜尋的知識存放區。 知識存放區目前為公開預覽狀態。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75754077"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>使用儲存體總管來檢視知識存放區

> [!IMPORTANT] 
> 知識存放區目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

在本文中，您將瞭解如何使用 Azure 入口網站中的儲存體總管連接到知識存放區，並加以探索。

## <a name="prerequisites"></a>Prerequisites

+ 遵循[在 Azure 入口網站中建立知識存放區](knowledge-store-create-portal.md)中的步驟，建立本逐步解說中所使用的知識存放區範例。

+ 您也需要用來建立知識存放區的 Azure 儲存體帳戶名稱，以及其來自 Azure 入口網站的存取金鑰。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>在儲存體總管中，查看、編輯和查詢知識存放區

1. 在 [Azure 入口網站中，開啟您用來建立知識存放區的[儲存體帳戶](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. 在儲存體帳戶的左側瀏覽窗格中，按一下 [儲存體總管]****。

1. 展開 [資料表]**** 清單，以顯示當您對飯店檢閱資料範例執行 [匯入資料]**** 精靈時所建立的 Azure 資料表預測清單。

選取任何資料表來查看擴充的資料，包括主要片語和情感分數。

   ![在儲存體總管中檢視資料表](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "在儲存體總管中檢視資料表")

若要變更任何資料表值的資料類型，或變更資料表中的個別值，請按一下 [編輯]****。 當您變更某個資料表資料列中任何資料行的資料類型時，系統變會將該類型套用至所有資料列。

   ![儲存體總管中編輯資料表](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "儲存體總管中編輯資料表")

若要執行查詢，請在命令列上按一下 [查詢]****，然後輸入您的條件。  

   ![在儲存體總管中查詢資料表](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "在儲存體總管中查詢資料表")

## <a name="clean-up"></a>清除

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源]  或 [資源群組]  連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。

## <a name="next-steps"></a>後續步驟

將此知識存放區連接到 Power BI 以進行更深入的分析，或使用 REST API 和 Postman 來建立不同的知識存放區，以進行程式碼的繼續。

> [!div class="nextstepaction"]
> [連接 Power BI](knowledge-store-connect-power-bi.md)
> [在 REST 中建立知識存放區](knowledge-store-create-rest.md)
