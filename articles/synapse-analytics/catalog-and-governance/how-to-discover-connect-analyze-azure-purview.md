---
title: 使用 Azure 範疇探索、連接及探索 Synapse 中的資料
description: 如何探索資料、將其連線並在 Synapse 中流覽的指南
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a3ed7979584787627c97cfec3bb2e19c147f7152
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872652"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>使用 Azure 範疇探索、連接及探索 Synapse 中的資料 

在本檔中，您將瞭解向 Synapse 註冊 Azure 範疇帳戶時可執行檔互動類型。 

## <a name="prerequisites"></a>先決條件 

- [Azure Purview 帳戶](../../purview/create-catalog-portal.md) 
- [Synapse 工作區](../quickstart-create-workspace.md) 
- [將 Azure 範疇帳戶連線到 Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>在 Synapse 中使用 Azure 範疇 

在 Synapse 中使用 Azure 範疇需要您擁有該範疇帳戶的存取權。 Synapse 傳遞您的範疇許可權。 例如，如果您有編者許可權角色，您將能夠編輯 Azure 範疇掃描的中繼資料。 

### <a name="data-discovery-search-datasets"></a>資料探索：搜尋資料集 

若要探索 Azure 範疇註冊和掃描的資料，您可以使用 Synapse 工作區頂端中央的搜尋列。 請確定您選取 Azure 範疇來搜尋所有組織資料。 

[![搜尋 Azure 範疇資產](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure 範疇動作 

以下是 Synapse 中可用的 Azure 範疇功能清單： 
- 中繼資料的 **總覽** 
- 使用分類、詞彙、資料類型和描述來查看和編輯中繼資料的 **架構** 
- 查看歷程以瞭解相依性， **並進行影響** 分析。 如需的詳細資訊， [請參閱歷程](../../purview/catalog-lineage-user-guide.md)
- 查看及編輯 **連絡人** ，以瞭解誰是資料集的擁有者或專家 
- **與** 瞭解特定資料集的階層式相依性相關。 這項體驗有助於流覽資料階層。

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>您可以使用 Synapse 資源對資料集執行的動作 

### <a name="connect-data-to-synapse"></a>將資料連線至 Synapse 

- 您可以建立 **新的連結服務** 來進行 Synapse。 需要該動作才能將資料複製到 Synapse，或將資料放在您的資料中樞 (針對支援的資料來源（例如 ADLSg2）)  
- 針對檔案、資料夾或資料表之類的物件，您可以直接建立 **新的整合資料集** ，並利用現有的連結服務（若已建立） 

我們還無法推斷是否有現有的連結服務或整合資料集。 

###  <a name="develop-in-synapse"></a>在 Synapse 中開發 

您可以執行的動作有三個： **新的 SQL 腳本**、 **新的筆記本** 和 **新** 的資料流程。 

您可以使用 **新的 SQL 腳本**，視支援類型而定，您可以： 
- 查看前100個數據列，以瞭解資料的形狀。 
- 從 Synapse SQL database 建立外部資料表 
- 將資料載入 Synapse SQL 資料庫 
 
使用 **新的筆記本**，您可以： 
- 將資料載入 Spark 資料框架 
- 建立 Spark 資料表 (如果您對 Parquet 格式進行這種設定，它也會建立無伺服器的 SQL 集區資料表) 。 
 
使用 **新** 的資料流程，您可以建立可在資料流程管線中使用來源的整合資料集。 資料流程是無程式碼的開發人員功能，可執行資料轉換。 如需 [在 Synapse 中使用資料流程](../quickstart-data-flow.md)的詳細資訊。

##  <a name="nextsteps"></a>後續步驟 

- [在 Azure Purview 中註冊及掃描 Azure Synapse 資產](../../purview/register-scan-azure-synapse-analytics.md)
- [如何在 Azure 範疇資料目錄中搜尋資料](../../purview/how-to-search-catalog.md)
