---
title: 使用儲存體總管來檢視知識存放區
titleSuffix: Azure Cognitive Search
description: 使用 Azure 入口網站的儲存體總管來檢視和分析 Azure 認知搜尋的知識存放區。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85566019"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>使用儲存體總管來檢視知識存放區

在本文中，您將瞭解如何使用 Azure 入口網站中的儲存體總管來連接及探索知識存放區。

## <a name="prerequisites"></a>Prerequisites

+ 遵循[在 Azure 入口網站中建立知識存放區](knowledge-store-create-portal.md)中的步驟，建立本逐步解說中所使用的知識存放區範例。

+ 您也需要用來建立知識存放區的 Azure 儲存體帳戶名稱，以及其來自 Azure 入口網站的存取金鑰。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>在儲存體總管中查看、編輯和查詢知識存放區

1. 在 Azure 入口網站中，開啟您用來建立知識存放區的 [儲存體帳戶](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 。

1. 在儲存體帳戶的左側瀏覽窗格中，按一下 [儲存體總管]****。

1. 展開 [資料表]**** 清單，以顯示當您對飯店檢閱資料範例執行 [匯入資料]**** 精靈時所建立的 Azure 資料表預測清單。

選取任何資料表以查看擴充的資料，包括主要片語和情感分數。

   ![在儲存體總管中檢視資料表](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "在儲存體總管中檢視資料表")

若要變更任何資料表值的資料類型，或變更資料表中的個別值，請按一下 [編輯]****。 當您變更某個資料表資料列中任何資料行的資料類型時，系統變會將該類型套用至所有資料列。

   ![儲存體總管中編輯資料表](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "儲存體總管中編輯資料表")

若要執行查詢，請在命令列上按一下 [查詢]****，然後輸入您的條件。  

   ![在儲存體總管中查詢資料表](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "在儲存體總管中查詢資料表")

## <a name="clean-up"></a>清除

如果您是在自己的訂用帳戶中進行，建議您在專案結束時判斷自己是否仍需要先前所建立的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。

## <a name="next-steps"></a>後續步驟

將此知識存放區連接到 Power BI 以進行更深入的分析，或使用 REST API 和 Postman 來建立不同的知識存放區，以繼續使用程式碼。

> [!div class="nextstepaction"]
> [使用 Power BI 連接](knowledge-store-connect-power-bi.md) 
> [建立 REST 中的知識存放區](knowledge-store-create-rest.md)
