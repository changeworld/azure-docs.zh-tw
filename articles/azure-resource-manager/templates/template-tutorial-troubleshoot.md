---
title: 針對部署進行疑難排解
description: 了解如何對 Azure Resource Manager 範本部署進行監視和疑難排解。 顯示活動記錄和部署歷程記錄。
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0da6c614572e73a00db1087621eaca3bd790aad6
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891800"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>教學課程：對 ARM 範本部署進行疑難排解

了解如何對 Azure Resource Manager (ARM) 範本部署錯誤進行疑難排解。 在本教學課程中，您將在範本中設定兩個錯誤，並了解如何使用活動記錄和部署歷程記錄來解決問題。

範本部署有兩種類型的相關錯誤：

- **驗證錯誤** 來自可在部署之前判斷的情況。 其中包含您範本中的語法錯誤，或者嘗試部署會超出您訂用帳戶配額的資源。
- **部署錯誤** 來自在部署程序期間發生的狀況。 其中包括嘗試存取以平行方式部署的資源。

這兩種錯誤類型都會傳回錯誤碼，以供您針對部署進行疑難排解。 這兩種錯誤類型都會出現在活動記錄檔中。 不過，驗證錯誤不會出現在部署歷程記錄中，因為部署永遠不會啟動。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> - 建立有問題的範本
> - 對驗證錯誤進行疑難排解
> - 針對部署錯誤進行疑難排解
> - 清除資源

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

- Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](quickstart-create-templates-use-visual-studio-code.md)。

## <a name="create-a-problematic-template"></a>建立有問題的範本

從 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)開啟名為[建立標準儲存體帳戶](https://azure.microsoft.com/resources/templates/101-storage-account-create/)的範本，並設定兩個範本問題。

1. 在 Visual Studio Code 中，選取 [檔案]>[開啟檔案]。
2. 在 [檔案名稱] 中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. 選取 [開啟] 以開啟檔案。
4. 將 **apiVersion** 行變更為以下這一行：

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - **apiVersion1** 是無效的元素名稱。 這是驗證錯誤。
    - API 版本應為 "2018-07-01"。  這是部署錯誤。

5. 選取 [檔案]>[另存新檔]，在您的本機電腦上將檔案另存為 **azuredeploy.json** 。

## <a name="troubleshoot-the-validation-error"></a>對驗證錯誤進行疑難排解

請參閱[部署範本](template-tutorial-create-multiple-instances.md#deploy-the-template)一節，以部署範本。

殼層應該會出現如下的錯誤：

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

此錯誤訊息表示問題與 **apiVersion1** 有關。

請使用 Visual Studio Code 將 **apiVersion1** 變更為 **apiVersion** 以更正問題，然後儲存範本。

## <a name="troubleshoot-the-deployment-error"></a>對部署錯誤進行疑難排解

請參閱[部署範本](template-tutorial-create-multiple-instances.md#deploy-the-template)一節，以部署範本。

殼層應該會出現如下的錯誤：

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

您可以使用下列程序從 Azure 入口網站找出部署錯誤：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序選取 [資源群組] 和資源群組名稱，以開啟資源群組。 您應該會在 [部署] 下方看到 [1 失敗]。

    ![醒目提示失敗部署的螢幕擷取畫面。](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. 選取 [錯誤詳細資料]。

    ![醒目提示 [錯誤詳細資料] 連結的螢幕擷取畫面。](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    錯誤訊息與先前顯示的相同：

    ![顯示錯誤詳細資料的螢幕擷取畫面。](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

您也可以在活動記錄中找到錯誤：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [監視] > [活動記錄]。
3. 使用篩選條件尋找記錄。

    ![Resource Manager 教學課程疑難排解](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

請使用 Visual Studio Code 來更正問題，然後重新部署範本。

如需常見錯誤的清單，請參閱[對使用 Azure Resource Manager 時常見的 Azure 部署錯誤進行疑難排解](common-deployment-errors.md)。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何對 ARM 範本部署錯誤進行疑難排解。  如需詳細資訊，請參閱[對使用 Azure Resource Manager 時常見的 Azure 部署錯誤進行疑難排解](common-deployment-errors.md)。
