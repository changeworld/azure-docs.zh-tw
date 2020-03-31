---
title: 在 Azure 宇宙資料庫帳戶中啟用筆記本（預覽版）
description: Azure Cosmos DB 的內置筆記本使您能夠從門戶中分析和視覺化資料。 本文介紹如何為 Cosmos 帳戶啟用此功能。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768027"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>為 Azure 宇宙資料庫帳戶啟用筆記本（預覽版）

> [!IMPORTANT]
> Azure Cosmos DB 的內置筆記本目前在以下 Azure 區域可用：澳大利亞東部、美國東部、美國東部 2、北歐、美國中南部、東南亞、英國南部、西歐和美國西部 2。 要使用筆記本，[請使用筆記本創建新帳戶](#enable-notebooks-in-a-new-cosmos-account)，或在其中一個區域的現有[帳戶上啟用筆記本](#enable-notebooks-in-an-existing-cosmos-account)。

Azure Cosmos DB 中的內置 Jupyter 筆記本使您能夠從 Azure 門戶分析和視覺化資料。 本文說明如何為 Azure Cosmos DB 帳戶啟用此功能。

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>在新的 Cosmos 帳戶中啟用筆記本
1. 登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選擇 **"創建資源** > **資料庫** > **Azure 宇宙資料庫**"。
1. 在 **"創建 Azure 宇宙資料庫帳戶"** 頁上，選擇**筆記本**。 
 
    ![在 Azure 宇宙 DB 創建邊欄選項卡中選擇筆記本選項](media/enable-notebooks/create-new-account-with-notebooks.png)
1. 選擇 **"審閱" = 創建**。 您可以跳過 **"網路和****標記"** 選項。 
1. 檢閱帳戶設定，然後選取 [建立]****。 建立帳戶需要幾分鐘的時間。 等候入口網站頁面顯示 [您的部署已完成]**** 訊息。 

    ![Azure 入口網站的 [通知] 窗格](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. 選取 [移至資源]**** 以移至 Azure Cosmos DB 帳戶頁面。 

    ![Azure Cosmos DB 帳戶頁面](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. 導航到**資料資源管理器**窗格。 現在，您應該會看到筆記本工作區。

    ![新的 Azure 宇宙資料庫筆記本工作區](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>在現有 Cosmos 帳戶中啟用筆記本
您還可以在現有帳戶上啟用筆記本。 此步驟只需每個帳戶執行一次。

1. 導航到 Cosmos 帳戶中**的資料資源管理器**窗格。
1. 選擇**啟用筆記本**。

    ![在資料資源管理器中創建新的筆記本工作區](media/enable-notebooks/enable-notebooks-workspace.png)
1. 這將提示您創建新的筆記本工作區。 選擇 **"完成設置"。**
1. 您的帳戶現在已啟用使用筆記本！

## <a name="create-and-run-your-first-notebook"></a>創建並運行第一個筆記本

要驗證是否可以使用筆記本，請在"示例筆記本"下選擇其中一個筆記本。 這將保存筆記本的副本到工作區並打開它。

在此示例中，我們將使用**入門.ipynb**。 

![查看入門.ipynb 筆記本](media/enable-notebooks/select-getting-started-notebook.png)

要運行筆記本：
1. 選擇包含 Python 代碼的第一個代碼單元。 
1. 選擇 **"運行"** 以運行儲存格。 您還可以使用 Shift **+ Enter**來運行儲存格。
1. 刷新資源窗格以查看已創建的資料庫和容器。

    ![運行入門筆記本](media/enable-notebooks/run-first-notebook-cell.png)

您還可以選擇 **"新筆記本"** 以創建新筆記本或上傳現有筆記本 （.ipynb） 檔，從"**我的筆記本"功能表中選擇****"上傳檔**"。 

![創建或上傳新筆記本](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>後續步驟

- 瞭解[Azure 宇宙 DB 聚居器筆記本的優勢](cosmosdb-jupyter-notebooks.md)
