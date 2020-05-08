---
title: 使用 GitHub 動作部署 Resource Manager 範本
description: 說明如何使用 GitHub 動作部署 Azure Resource Manager 範本。
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 5fe147a9c42e83d5e644b0c08dfa67de88ec05c0
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875190"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>使用 GitHub 動作部署 Azure Resource Manager 範本

[Github 動作](https://help.github.com/en/actions)可讓您直接在您的 AZURE RESOURCE MANAGER （ARM）範本儲存所在的 github 存放庫中建立自訂軟體發展生命週期工作流程。 [工作流程](https://help.github.com/actions/reference/workflow-syntax-for-github-actions)是由 YAML 檔所定義。 工作流程有一或多個作業，其中每項工作都包含一組執行個別工作的步驟。 步驟可以執行命令或使用動作。 您可以建立自己的動作，或使用[GitHub 社區](https://github.com/marketplace?type=actions)所共用的動作，並視需要進行自訂。 本文說明如何使用[Azure CLI 動作](https://github.com/marketplace/actions/azure-cli-action)來部署 Resource Manager 範本。

Azure CLI 動作有兩個相依動作：

- **[簽](https://github.com/marketplace/actions/checkout)** 出您的存放庫，讓工作流程可以存取任何指定的 Resource Manager 範本。
- **[Azure 登](https://github.com/marketplace/actions/azure-login)** 入：使用您的 Azure 認證登入

部署 Resource Manager 範本的基本工作流程可以有三個步驟：

1. 簽出範本檔案。
2. 登入 Azure。
3. 部署 Resource Manager 範本

## <a name="prerequisites"></a>Prerequisites

您需要 GitHub 存放庫來儲存您的 Resource Manager 範本和工作流程檔案。 若要建立一個，請參閱[建立新的存放庫](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)。

## <a name="configure-deployment-credentials"></a>設定部署認證

Azure 登入動作會使用服務主體來對 Azure 進行驗證。 CI/CD 工作流程的主體通常需要內建參與者許可權，才能部署 Azure 資源。

下列 Azure CLI 腳本會示範如何在 Azure 資源群組上產生具有參與者許可權的 Azure 服務主體。 此資源群組是工作流程部署 Resource Manager 範本中所定義資源的位置。

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

自訂腳本中 **$projectName**和 **$location**的值。 資源群組名稱是附加 **rg** 的專案名稱。 您必須在工作流程中指定資源組名。

腳本會輸出類似下面的 JSON 物件：

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

複製 JSON 輸出，並將它儲存為 github 存放庫中的 GitHub 密碼。 如果您還沒有存放庫[，請參閱](#prerequisites)必要條件。

1. 從您的 GitHub 存放庫中，選取 [**設定**] 索引標籤。
1. 從左側功能表中選取 [**秘密**]。
1. 輸入下列值：

    - **名稱**： AZURE_CREDENTIALS
    - **值**：（貼上 JSON 輸出）
1. 選取 [**新增密碼**]。

您必須在工作流程中指定秘密名稱。

## <a name="add-resource-manager-template"></a>新增 Resource Manager 範本

將 Resource Manager 範本新增至 GitHub 存放庫。 如果您沒有帳戶，您可以使用下列範本。 此範本會建立儲存體帳戶。

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

您可以將檔案放在存放庫中的任何位置。 下一節中的工作流程範例假設範本檔案名為**azuredeploy.parameters.json」**，並儲存在存放庫根目錄中名為**templates**的資料夾中。

## <a name="create-workflow"></a>建立工作流程

工作流程檔案必須儲存在您存放庫根目錄的**github/workflow**資料夾中。 工作流程副檔名可以是 **. yml**或 **. yaml**。

您可以建立工作流程檔案，然後將檔案推送/上傳至存放庫，或使用下列程式：

1. 從您的 GitHub 存放庫中，選取頂端功能表中的 [**動作**]。
1. 選取 [**新增工作流程**]。
1. 選取 [**自行設定工作流程**]。
1. 如果您偏好**yml**以外的其他名稱，請重新命名工作流程檔案。 例如： **deployStorageAccount. yml**。
1. 以下列內容取代 yml 檔案的內容：

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    工作流程檔案有三個區段：

    - **名稱**：工作流程的名稱。
    - **on**：用來觸發工作流程的 GitHub 事件名稱。 當主要分支上有推送事件時，工作流程就會觸發，這會修改至少指定的兩個檔案中的其中一個。 這兩個檔案是工作流程檔案和範本檔案。

        > [!IMPORTANT]
        > 確認兩個檔案及其路徑是否符合您的檔案。
    - **作業**：工作流程執行是由一或多個工作所組成。 只有一個稱為「**部署-儲存體-帳戶-範本**」的作業。  此作業有三個步驟：

        - **簽出原始碼**。
        - **登入 Azure**。

            > [!IMPORTANT]
            > 確認密碼名稱符合您儲存至存放庫的專案。 請參閱[設定部署認證](#configure-deployment-credentials)。
        - **部署 ARM 範本**。 取代**resourceGroupName**的值。  如果您在[設定部署認證](#configure-deployment-credentials)中使用了 Azure CLI 腳本，所產生的資源組名就是已附加**rg**的專案名稱。 確認**templateLocation**的值。

1. 選取 [**開始認可**]。
1. 選取 **[直接認可至主要分支**]。
1. 選取 [**認可新**檔案] （或 [**認可變更**]）。

由於工作流程是設定為要由工作流程檔案或正在更新的範本檔案來觸發，因此工作流程會在您認可變更後立即啟動。

## <a name="check-workflow-status"></a>檢查工作流程狀態

1. 選取 [**動作**] 索引標籤。您應該會看到列出的**建立 deployStorageAccount yml**工作流程。 執行工作流程需要1-2 分鐘的時間。
1. 選取工作流程加以開啟。
1. 從左側功能表中，選取 [**部署-儲存體-帳戶範本**（作業名稱）]。 作業名稱是在工作流程中定義。
1. 選取 [**部署 ARM 範本**（步驟名稱）] 將它展開。 您可以看到 REST API 回應。

## <a name="next-steps"></a>後續步驟

如需逐步教學課程，引導您完成建立範本的程式，請參閱[教學課程：建立及部署您的第一個 ARM 範本](template-tutorial-create-first-template.md)。
