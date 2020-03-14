---
title: Azure 入口網站中匯出範本
description: 使用 Azure 入口網站從您的訂用帳戶中的資源匯出 Azure Resource Manager 範本。
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273731"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>單一和多重資源匯出至 Azure 入口網站中的範本

若要協助建立 Azure Resource Manager 範本，您可以從現有的資源匯出範本。 匯出的範本可協助您瞭解用來部署資源的 JSON 語法和屬性。 若要將未來的部署自動化，請從匯出的範本開始，並針對您的案例加以修改。

Resource Manager 可讓您挑選一或多個要匯出至範本的資源。 您可以將焦點放在範本中所需的確切資源上。

本文說明如何透過入口網站匯出範本。 您也可以使用[Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、 [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](/rest/api/resources/resourcegroups/exporttemplate)。

## <a name="choose-the-right-export-option"></a>選擇正確的匯出選項

有兩種方式可以匯出範本：

* **從資源群組或資源匯出**。 此選項會從現有的資源產生新的範本。 匯出的範本是資源群組目前狀態的「快照集」。 您可以匯出整個資源群組或該資源群組中的特定資源。

* **在部署之前或從歷程記錄匯出**。 此選項會抓取用於部署之範本的完整複本。

根據您選擇的選項而定，匯出的範本會有不同的品質。

| 從資源群組或資源 | 部署之前或從歷程記錄 |
| --------------------- | ----------------- |
| 範本是資源「目前」狀態的快照集。 其中包含您在部署後所做的任何手動變更。 | 範本只會顯示部署時的資源狀態。 您在部署後所做的任何手動變更都不會包含在內。 |
| 您可以從資源群組中選取要匯出的資源。 | 包含特定部署的所有資源。 您無法挑選這些資源的子集，或新增在不同時間新增的資源。 |
| 範本包含資源的所有屬性，包括您在部署期間通常不會設定的一些屬性。 在重複使用範本之前，您可能會想要移除或清除這些屬性。 | 範本僅包含部署所需的屬性。 範本已備妥可供使用。 |
| 範本可能未包含重複使用所需的所有參數。 大部分的屬性值都是在範本中硬式編碼。 若要在其他環境中重新部署範本，您需要新增參數來增加設定資源的能力。  您可以取消選取 [**包含參數**]，讓您可以撰寫自己的參數。 | 範本包括可讓您輕鬆地在不同環境中重新部署的參數。 |

從資源群組或資源匯出範本的時機：

* 您必須針對原始部署之後所做的資源，捕捉變更。
* 您想要選取要匯出的資源。

在部署之前或從歷程記錄匯出範本，時機：

* 您想要一個容易重複使用的範本。
* 您不需要包含在原始部署之後所做的變更。

## <a name="limitations"></a>限制

從資源群組或資源匯出時，會從[已發佈的架構](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas)為每個資源類型產生匯出的範本。 有時候，架構沒有資源類型的最新版本。 請檢查您匯出的範本，以確定它包含您所需的屬性。 如有需要，請編輯匯出的範本，以使用您所需的 API 版本。

「匯出範本」功能不支援匯出 Azure Data Factory 資源。 若要瞭解如何匯出 Data Factory 資源，請參閱[在 Azure Data Factory 中複製或複製資料](https://aka.ms/exportTemplateViaAdf)處理站。

若要匯出透過傳統部署模型建立的資源，您必須將[它們遷移至 Resource Manager 部署模型](https://aka.ms/migrateclassicresourcetoarm)。

## <a name="export-template-from-a-resource-group"></a>從資源群組匯出範本

若要從資源群組匯出一或多個資源：

1. 選取包含您要匯出之資源的資源群組。

1. 選取核取方塊來選取一或多個資源。  若要選取 [全部]，請選取 [**名稱**] 左邊的核取方塊。 只有在您選取至少一個資源之後，[**匯出範本**] 功能表項目才會變成啟用狀態。

   ![匯出所有資源](./media/export-template-portal/select-all-resources.png)

    在螢幕擷取畫面上，只會選取儲存體帳戶。
1. 選取 [匯出範本]。

1. 匯出的範本隨即顯示，並可供下載和部署。

   ![顯示範本](./media/export-template-portal/show-template.png)

   預設會選取 [**包含參數**]。  選取此選項時，將會在產生範本時包含所有範本參數。 如果您想要撰寫自己的參數，請切換此核取方塊以不包含它們。

## <a name="export-template-from-a-resource"></a>從資源匯出範本

若要匯出一項資源：

1. 選取包含您要匯出之資源的資源群組。

1. 選取您想要匯出的資源以開啟資源。

1. 針對該資源，請在左窗格中選取 [**匯出範本**]。

   ![匯出資源](./media/export-template-portal/export-single-resource.png)

1. 匯出的範本隨即顯示，並可供下載和部署。 範本只包含單一資源。 預設會選取 [**包含參數**]。  選取此選項時，將會在產生範本時包含所有範本參數。 如果您想要撰寫自己的參數，請切換此核取方塊以不包含它們。

## <a name="export-template-before-deployment"></a>部署前匯出範本

1. 選取您想要部署的 Azure 服務。

1. 填入新服務的值。

1. 通過驗證之後，在開始部署之前，請選取 [**下載自動化的範本**]。

   ![下載範本](./media/export-template-portal/download-before-deployment.png)

1. 範本隨即顯示，並可供下載和部署。


## <a name="export-template-after-deployment"></a>部署後匯出範本

您可以匯出用來部署現有資源的範本。 您取得的範本完全是用於部署的範本。

1. 選取您想要匯出的資源群組。

1. 選取 [**部署**] 底下的連結。

   ![選取部署歷程記錄](./media/export-template-portal/select-deployment-history.png)

1. 從部署歷程記錄中選取其中一個部署。

   ![選取部署](./media/export-template-portal/select-details.png)

1. 選取 [**範本**]。 這會顯示用於此部署的範本，並可供下載。

   ![選取範本](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、 [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](/rest/api/resources/resourcegroups/exporttemplate)匯出範本。
- 若要瞭解 Resource Manager 範本語法，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
- 若要瞭解如何開發範本，請參閱[逐步教學](/azure/azure-resource-manager/)課程。
- 若要查看 Azure Resource Manager 範本架構，請參閱[範本參考](/azure/templates/)。
