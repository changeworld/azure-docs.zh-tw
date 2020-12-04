---
title: 將 Data Factory 歷程資料推送至 Azure 範疇
description: 瞭解如何將 Data Factory 歷程資料推送至 Azure 範疇
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 6b50c9440a958bc1398e79ddf3c5a0984816ec45
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603181"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>將 Data Factory 歷程資料推送至 Azure 範疇 (Preview) 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教學課程中，您將使用 Data Factory 使用者介面 (UI) 來建立執行活動的管線，並將歷程資料包告至 Azure 範疇帳戶。 然後，您可以在 Azure 範疇帳戶中查看所有歷程資訊。

## <a name="prerequisites"></a>必要條件
* **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* **Azure Data Factory**。 如果您沒有 Azure Data Factory，請參閱 [建立 Azure Data Factory](./quickstart-create-data-factory-portal.md)。
* **Azure 範疇帳戶**。 範疇帳戶會捕獲 data factory 所產生的所有歷程資料。 如果您沒有 Azure 範疇帳戶，請參閱 [建立 Azure 範疇](https://docs.microsoft.com/azure/purview/create-catalog-portal)。


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>執行 Data Factory 活動並將歷程資料推送至 Azure 範疇
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>步驟1：將 Data Factory 連接到您的範疇帳戶
在範疇入口網站中登入您的範疇帳戶，移至 [ **管理中心**]。 選擇 [**外部** 連線 **Data Factory** ]，然後按一下 [**新增**] 按鈕以建立新 Data Factory 的連接。 
[![在 Data Factory 與範疇帳戶 ](./media/data-factory-purview/connect-adf-to-purview.png) 之間建立連線的螢幕擷取畫面 ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

在快顯視窗中，您可以選擇要連接到這個範疇帳戶的 Data Factory。 
![新連接的螢幕擷取畫面](./media/data-factory-purview/new-adf-purview-connection.png)

您可以在建立連接後檢查狀態。 **Connected** 表示 Data Factory 與此範疇之間的連接已成功連線。 
> [!NOTE]
> 您必須在範疇帳戶中指派下列任何角色，Data Factory **參與者** 角色才能建立 Data Factory 與 Azure 範疇之間的連線。
> - 擁有者
> - 使用者存取系統管理員

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>步驟2：在 Data Factory 中執行複製和資料流程活動
您可以在 Data Factory 中建立管線、複製活動和資料流程活動。 歷程資料捕捉不需要任何額外的設定。 歷程資料會在活動執行期間自動進行捕捉。
![複製和資料流程活動的螢幕擷取畫面 ](./media/data-factory-purview/adf-activities-for-lineage.png) （如果您不知道如何建立複製和資料流程活動），請參閱 [使用 Azure Data Factory 將資料從 Azure Blob 儲存體複製到 Azure SQL Database 中的資料庫](./tutorial-copy-data-portal.md) ，並 [使用對應資料流程來轉換資料](./tutorial-data-flow.md)。

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>步驟3：在 Data Factory 中執行執行 SSIS 套件活動
您可以在 Data Factory 中建立管線、執行 SSIS 套件活動。 歷程資料捕捉不需要任何額外的設定。 歷程資料會在活動執行期間自動進行捕捉。
![執行 SSIS 套件活動的螢幕擷取畫面](./media/data-factory-purview/ssis-activities-for-lineage.png)

如果您不知道如何建立「執行 SSIS 套件」活動，請參閱 [在 Azure 中執行 Ssis 套件](./tutorial-deploy-ssis-packages-azure.md)。

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>步驟4：在您的範疇帳戶中查看歷程資訊
返回範疇帳戶。 在首頁上，選取 **[流覽資產]**。 選擇您想要的資產，然後按一下 [歷程] 索引標籤。您將會看到所有歷程資訊。
[![範疇帳戶 ](./media/data-factory-purview/view-dataset.png) 的螢幕擷取畫面 ](./media/data-factory-purview/view-dataset.png#lightbox)

您可以查看複製活動的歷程資料。
[![複製 ](./media/data-factory-purview/copy-lineage.png) 歷程的螢幕擷取畫面 ](./media/data-factory-purview/copy-lineage.png#lightbox)

您也可以查看資料流程活動的歷程資料。
[![資料流程 ](./media/data-factory-purview/dataflow-lineage.png) 歷程的螢幕擷取畫面 ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> 針對資料流程活動的歷程，我們只支援來源和接收。 尚不支援資料流程轉換的歷程。

您也可以查看「執行 SSIS 套件」活動的歷程資料。
[![SSIS ](./media/data-factory-purview/ssis-lineage.png) 歷程的螢幕擷取畫面 ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> 在「執行 SSIS 套件」活動的歷程中，我們只支援來源和目的地。 尚不支援轉換的歷程。

## <a name="next-steps"></a>後續步驟
[目錄歷程使用者指南](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[將 Data Factory 連線到 Azure 範疇](connect-data-factory-to-azure-purview.md)