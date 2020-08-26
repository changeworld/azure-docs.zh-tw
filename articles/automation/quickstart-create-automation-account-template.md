---
title: 快速入門：建立自動化帳戶 - Azure 範本
titleSuffix: Azure Automation
description: 此快速入門顯示如何使用 Azure Resource Manager 範本建立自動化帳戶。
services: automation
documentationcenter: na
author: mgoedtel
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.service: automation
ms.devlang: na
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 07/23/2020
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 37a619fe3279d1cb03763b14c3dfc9e315d850b9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685624"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>快速入門：使用 ARM 範本建立自動化帳戶

Azure 自動化會提供以雲端為基礎的自動化和設定服務，在您的 Azure 和非 Azure 環境之間支援一致的管理； 此快速入門顯示如何部署可建立自動化帳戶的 Azure Resource Manager 範本 (ARM 範本)。 相較於其他部署方法，使用 ARM 範本所需的步驟比較少。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

這個範例範本會執行下列作業：

* 自動化建立 Azure 監視器 Log Analytics 工作區。
* 自動化建立 Azure 自動化帳戶。
* 將自動化帳戶連結至 Log Analytics 工作區。
* 將範例自動化 Runbook 新增至帳戶。

>[!NOTE]
>當您使用 ARM 範本時，不支援建立自動化執行身分帳戶。 若要從入口網站或使用 PowerShell 手動建立執行身分帳戶，請參閱[管理執行身分帳戶](manage-runas-account.md)。

完成這些步驟之後，您需要為您的自動化帳戶[設定診斷設定](automation-manage-send-joblogs-log-analytics.md)，以將 Runbook 作業狀態和作業串流傳送至連結的 Log Analytics 工作區。

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-automation/)。

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API 版本

下表列出此範例中使用的資源 API 版本。

| 資源 | 資源類型 | API 版本 |
|:---|:---|:---|
| [工作區](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [自動化帳戶](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [工作區連結服務](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | worksapces | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>使用範本之前

系統已經為您設定 JSON 參數範本，以指定：

* 工作區的名稱。
* 要在其中建立工作區的區域。
* 自動化帳戶的名稱。
* 要在其中建立自動化帳戶的區域。

系統會使用 Log Analytics 工作區的預設值來設定範本中的下列參數：

* SKU 預設為在 2018 年 4 月定價模型中發行的每 GB 定價層。
* dataRetention 預設為 30 天。

>[!WARNING]
>如果您想要在選擇加入 2018 年 4 月定價模型的訂用帳戶中建立或設定 Log Analytics 工作區，則唯一有效的 Log Analytics 定價層是 PerGB2018。
>

JSON 範本會針對您的環境中可能用於標準設定的其他參數，指定預設值。 您可以將範本儲存在 Azure 儲存體帳戶中，以在組織內共用存取。 如需有關使用範本的詳細資訊，請參閱[使用 ARM 範本和 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)。

如果您是 Azure 自動化和 Azure 監視器的新手，請務必了解下列設定詳細資料。 當您嘗試建立、設定和使用連結至新自動化帳戶的 Log Analytics 工作區時，這些詳細資料可以協助您避免錯誤。

* 請檢閱[其他詳細資料](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)，以全面了解工作區設定選項，例如，存取控制模式、定價層、保留和容量保留層級。

* 請參閱[工作區對應](how-to/region-mappings.md)，以指定內嵌或參數檔案中支援的區域。 只有特定區域支援連結 Log Analytics 工作區以及訂用帳戶中的自動化帳戶。

* 如果您是 Azure 監視器記錄的新手，而且尚未部署工作區，您應該參閱[工作區設計指引](../azure-monitor/platform/design-logs-deployment.md)。 此指引將協助您了解存取控制，並了解我們針對貴組織建議的設計實作策略。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 該範本會建立一個 Azure 自動化帳戶、一個 Log Analytics 工作區，並將自動化帳戶連結至工作區。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. 輸入這些值。

3. 部署需要幾分鐘的時間才能完成。 完成時，輸出如下：

    ![部署完成時的範例結果](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Azure 入口網站中，開啟您剛建立的自動化帳戶。 

3. 從左窗格中，選取 [Runbook]。 在 [Runbook] 頁面上，列出三個使用自動化帳戶建立的教學課程 Runbook。

    ![使用自動化帳戶建立的教學課程 Runbook](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. 從左窗格中，選取 [連結的工作區]。 在 [連結的工作區] 頁面上，其會顯示您稍早指定連結至自動化帳戶的 Log Analytics 工作區。

    ![連結至 Log Analytics 工作區的自動化帳戶](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>清除資源

當您不再需要資源時，請將自動化帳戶從 Log Analytics 工作區取消連結，然後刪除自動化帳戶和工作區。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已經建立自動化帳戶、Log Analytics 工作區，並將兩者連結在一起。

若要深入了解，請繼續進行 Azure 自動化的教學課程。

> [!div class="nextstepaction"]
> [Azure 自動化教學課程](learn/automation-tutorial-runbook-graphical.md)