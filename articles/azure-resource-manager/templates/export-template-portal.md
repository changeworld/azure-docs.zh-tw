---
title: Azure 門戶中的匯出範本
description: 使用 Azure 門戶從訂閱中的資源匯出 Azure 資源管理器範本。
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273731"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>單資源和多資源匯出到 Azure 門戶中的範本

為了説明創建 Azure 資源管理器範本，可以從現有資源匯出範本。 匯出的範本可説明您瞭解 JSON 語法和部署資源的屬性。 要自動執行將來的部署，請從匯出的範本開始，並針對您的方案對其進行修改。

資源管理器使您能夠選擇要匯出到範本的一個或多個資源。 您可以專注于範本中所需的資源。

本文演示如何通過門戶匯出範本。 您還可以使用 Azure [CLI、Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates)[電源外殼](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](/rest/api/resources/resourcegroups/exporttemplate)。

## <a name="choose-the-right-export-option"></a>選擇正確的匯出選項

有兩種方式可以匯出範本：

* **從資源組或資源匯出**。 此選項從現有資源生成新範本。 匯出的範本是資源組目前狀態的"快照"。 可以匯出整個資源組或該資源組中的特定資源。

* **在部署之前或從歷史記錄匯出**。 此選項檢索用於部署的範本的確切副本。

根據您選擇的選項，匯出的範本具有不同的品質。

| 從資源組或資源 | 部署之前或從歷史記錄 |
| --------------------- | ----------------- |
| 範本是資源目前狀態的快照。 它包括部署後所做的任何手動更改。 | 範本僅顯示部署時的資源狀態。 不包括部署後所做的任何手動更改。 |
| 您可以從資源組中選擇要匯出的資源。 | 包括特定部署的所有資源。 不能選擇這些資源的子集或添加在不同時間添加的資源。 |
| 範本包括資源的所有屬性，包括部署期間通常不會設置的某些屬性。 在重用範本之前，您可能需要刪除或清理這些屬性。 | 範本僅包括部署所需的屬性。 範本可供使用。 |
| 範本可能不包括需要重用的所有參數。 大多數屬性值都在範本中硬編碼。 要在其他環境中重新部署範本，您需要添加可提高配置資源能力的參數。  您可以取消選擇 **"包含"參數**，以便可以編寫自己的參數。 | 範本包含使在不同環境中輕鬆重新部署的參數。 |

從資源組或資源匯出範本時：

* 您需要捕獲對原始部署後所做的資源的更改。
* 您希望選擇要匯出的資源。

在部署之前或從歷史記錄中匯出範本，當：

* 您需要一個易於重用的範本。
* 您無需包括原始部署後所做的更改。

## <a name="limitations"></a>限制

從資源組或資源匯出時，將從每種資源類型的[已發佈架構](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas)生成匯出的範本。 有時，架構沒有資源類型的最新版本。 檢查匯出的範本，以確保它包含所需的屬性。 如有必要，編輯匯出的範本以使用所需的 API 版本。

匯出範本功能不支援匯出 Azure 資料工廠資源。 要瞭解如何匯出資料工廠資源，請參閱在 Azure[資料工廠中複製或克隆資料工廠](https://aka.ms/exportTemplateViaAdf)。

要匯出通過經典部署模型創建的資源，必須[將它們遷移到資源管理器部署模型](https://aka.ms/migrateclassicresourcetoarm)。

## <a name="export-template-from-a-resource-group"></a>從資源組匯出範本

要從資源組匯出一個或多個資源，可以：

1. 選擇包含要匯出的資源的資源組。

1. 通過選擇核取方塊選擇一個或多個資源。  要選擇全部，請選擇**名稱**左側的核取方塊。 "**匯出範本"** 功能表項目僅在選擇至少一個資源後才會啟用。

   ![匯出所有資源](./media/export-template-portal/select-all-resources.png)

    在螢幕截圖中，僅選擇存儲帳戶。
1. 選取 [匯出範本]****。

1. 將顯示匯出的範本，可供下載和部署。

   ![顯示範本](./media/export-template-portal/show-template.png)

   預設情況下選擇**包含參數**。  選中後，生成範本時將包含所有範本參數。 如果要編寫自己的參數，請選中此核取方塊以不包含它們。

## <a name="export-template-from-a-resource"></a>從資源匯出範本

要匯出一個資源：

1. 選擇包含要匯出的資源的資源組。

1. 選擇要匯出以打開資源的資源。

1. 對於該資源，選擇左側窗格中的 **"匯出"範本**。

   ![匯出資源](./media/export-template-portal/export-single-resource.png)

1. 將顯示匯出的範本，可供下載和部署。 範本僅包含單個資源。 預設情況下選擇**包含參數**。  選中後，生成範本時將包含所有範本參數。 如果要編寫自己的參數，請選中此核取方塊以不包含它們。

## <a name="export-template-before-deployment"></a>部署前匯出範本

1. 選擇要部署的 Azure 服務。

1. 填寫新服務的值。

1. 通過驗證後，但在開始部署之前，選擇 **"下載用於自動化的範本**"。

   ![下載範本](./media/export-template-portal/download-before-deployment.png)

1. 該範本顯示，可供下載和部署。


## <a name="export-template-after-deployment"></a>部署後匯出範本

您可以匯出用於部署現有資源的範本。 您得到的範本正是用於部署的範本。

1. 選擇要匯出的資源組。

1. 選擇"**部署**"下的連結。

   ![選擇部署歷史記錄](./media/export-template-portal/select-deployment-history.png)

1. 從部署歷史記錄中選擇一個部署。

   ![選取部署](./media/export-template-portal/select-details.png)

1. 選擇**範本**。 將顯示用於此部署的範本，可供下載。

   ![選取範本](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Azure CLI、Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](/rest/api/resources/resourcegroups/exporttemplate)匯出範本。
- 要瞭解資源管理器範本語法，請參閱瞭解[Azure 資源管理器範本的結構和語法](template-syntax.md)。
- 要瞭解如何開發範本，請參閱分[步教程](/azure/azure-resource-manager/)。
- 要查看 Azure 資源管理器範本架構，請參閱[範本引用](/azure/templates/)。
