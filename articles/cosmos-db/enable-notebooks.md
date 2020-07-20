---
title: 在 Azure Cosmos DB 帳戶中啟用筆記本（預覽）
description: Azure Cosmos DB 的內建筆記本可讓您從入口網站中分析資料並加以視覺化。 本文說明如何啟用 Cosmos 帳戶的這項功能。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 4c485bf6b9eb34e68e399c24e51286428f47586f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261897"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>啟用 Azure Cosmos DB 帳戶的筆記本（預覽）

> [!IMPORTANT]
> 下列 Azure 區域目前提供適用于 Azure Cosmos DB 的內建筆記本：澳大利亞東部、美國東部、美國東部2、北歐、美國中南部、東南亞、英國南部、西歐和美國西部2。 若要使用筆記本，請[建立具有筆記本的新帳戶](#enable-notebooks-in-a-new-cosmos-account)，或在其中一個區域中的[現有帳戶上啟用筆記本](#enable-notebooks-in-an-existing-cosmos-account)。

Azure Cosmos DB 中的內建 Jupyter 筆記本可供從 Azure 入口網站分析資料並將其視覺化。 本文說明如何為 Azure Cosmos DB 帳戶啟用此功能。

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>在新的 Cosmos 帳戶中啟用筆記本

1. 登入[Azure 入口網站](https://portal.azure.com/)。
1. 選取 [建立資源] > [資料庫] > [Azure Cosmos DB]。
1. 在 [**建立 Azure Cosmos DB 帳戶**] 頁面上，選取 [**筆記本**]。 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="在 Azure Cosmos DB 建立] 分頁中選取 [筆記本] 選項":::

1. 選取 [檢閱 + 建立]。 您可以略過 [**網路**和**標記**] 選項。 
1. 檢閱帳戶設定，然後選取 [建立]。 建立帳戶需要幾分鐘的時間。 等候入口網站頁面顯示 [您的部署已完成] 訊息。 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Azure 入口網站的 [通知] 窗格":::

1. 選取 [移至資源] 以移至 Azure Cosmos DB 帳戶頁面。

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Azure Cosmos DB 帳戶頁面":::

1. 流覽至 [**資料總管**] 窗格。 您現在應該會看到 [筆記本] 工作區。

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="新增 Azure Cosmos DB 筆記本工作區":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>在現有的 Cosmos 帳戶中啟用筆記本

您也可以在現有的帳戶上啟用筆記本。 每個帳戶只需執行一次此步驟。

1. 流覽至 Cosmos 帳戶中的 [**資料總管**] 窗格。
1. 選取 [**啟用筆記本**]。

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="在資料總管中建立新的 [筆記本] 工作區":::

1. 這會提示您建立新的 [筆記本] 工作區。 選取 [**完成設定]。**
1. 您的帳戶現在已啟用使用筆記本！

## <a name="create-and-run-your-first-notebook"></a>建立並執行您的第一個筆記本

若要確認您可以使用筆記本，請選取 [範例筆記本] 底下的其中一個筆記本。 這會將筆記本的複本儲存至您的工作區並加以開啟。

在此範例中，我們將使用**GettingStarted. ipynb**。 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="View GettingStarted. ipynb 筆記本":::

若要執行筆記本：
1. 選取包含 Python 程式碼的第一個程式碼儲存格。 
1. 選取 [**執行**] 以執行儲存格。 您也可以使用**Shift + Enter**來執行資料格。
1. 重新整理資源窗格，以查看已建立的資料庫和容器。

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="執行開始使用筆記本":::

您也可以選取 [**新增筆記本**] 來建立新的筆記本，或從 [**我的筆記本**] 功能表中選取 [**上傳**檔案] 來上傳現有的筆記本（. ipynb）檔案。 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="建立或上傳新的筆記本":::

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Cosmos DB Jupyter 筆記本](cosmosdb-jupyter-notebooks.md)的優點
