---
title: 使用 Azure CLI 和範本部署資源
description: 使用 Azure Resource Manager 和 Azure CLI 將資源部署到 Azure。 資源會定義在 Resource Manager 範本中。
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 7e8ae7e8c568f5f0ebb85f434e33f142b5fe94e8
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566155"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>使用 ARM 範本與 Azure CLI 來部署資源

本文說明如何搭配使用 Azure CLI 與 Azure Resource Manager 範本 (ARM 範本) 將您的資源部署到 Azure。 如果您不熟悉部署和管理 Azure 解決方案的概念，請參閱 [範本部署總覽](overview.md)。

Azure CLI 版本2.2.0 中的部署命令已變更。 本文中的範例需要 Azure CLI 2.2.0 版或更新版本。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果您沒有安裝 Azure CLI，可以使用 [Cloud Shell](#deploy-template-from-cloud-shell)。

## <a name="deployment-scope"></a>部署範圍

您可以將部署的目標設為資源群組、訂用帳戶、管理群組或租使用者。 在大部分情況下，您會將部署的目標設為資源群組。 若要在較大的範圍套用原則和角色指派，請使用訂用帳戶、管理群組或租使用者部署。 部署至訂用帳戶時，您可以建立資源群組，並將資源部署至該資源群組。

視部署的範圍而定，您可以使用不同的命令。

* 若要部署至 **資源群組**，請使用 [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create)：

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* 若要部署至 **訂**用帳戶，請使用 [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create)：

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  如需訂用帳戶層級部署的詳細資訊，請參閱[在訂用帳戶層級建立資源群組和資源](deploy-to-subscription.md)。

* 若要部署至 **管理群組**，請使用 [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create)：

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  如需管理群組層級部署的詳細資訊，請參閱[在管理群組層級建立資源](deploy-to-management-group.md)。

* 若要部署至 **租**使用者，請使用 [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create)：

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  如需租用戶層級部署的詳細資訊，請參閱[在租用戶層級建立資源](deploy-to-tenant.md)。

本文中的範例會使用資源群組部署。

## <a name="deploy-local-template"></a>部署本機範本

將資源部署至 Azure 時，您應該：

1. 登入您的 Azure 帳戶
2. 建立資源群組，作為已部署資源的容器。 資源群組的名稱只能包含英數字元、句點 (.)、底線、連字號及括弧。 最多可有 90 個字元。 不能以句點結束。
3. 部署至資源群組，此範本會定義要建立的資源。

範本可以包含讓您自訂部署的參數。 例如，您可以提供針對特定環境 (例如開發、測試和生產) 量身訂做的值。 範例範本會定義儲存體帳戶 SKU 的參數。

下列範例會建立資源群組，並從您的本機電腦部署範本：

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

部署需要幾分鐘的時間才能完成。 完成後，您會看到包含結果的訊息：

```output
"provisioningState": "Succeeded",
```

## <a name="deployment-name"></a>部署名稱

在上述範例中，您將部署命名為 `ExampleDeployment` 。 如果您未提供部署的名稱，則會使用範本檔案的名稱。 例如，如果您部署名為 `azuredeploy.json` 且未指定部署名稱的範本，則會將部署命名為 `azuredeploy` 。

每次執行部署時，會將專案新增至資源群組的部署歷程記錄，並提供部署名稱。 如果您執行另一個部署並指定相同名稱，則會將先前的專案取代為目前的部署。 如果您想要在部署歷程記錄中維護唯一的專案，請為每個部署提供一個唯一的名稱。

若要建立唯一的名稱，您可以指派一個亂數字。

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

或者，加入日期值。

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

如果您對相同的部署名稱執行相同資源群組的並行部署，則只會完成最後一個部署。 具有相同名稱但未完成的任何部署都會由最後一個部署取代。 例如，如果您執行名為的部署， `newStorage` 並部署名為的儲存體帳戶 `storage1` ，同時執行另一個名為的部署，並部署 `newStorage` 名為的儲存體帳戶 `storage2` ，您就只會部署一個儲存體帳戶。 產生的儲存體帳戶名為 `storage2` 。

但是，如果您執行名為 `newStorage` 的部署，並部署名為的儲存體帳戶 `storage1` ，並在完成後立即執行另一個名為的部署，並部署 `newStorage` 名為的儲存體帳戶 `storage2` ，則您會有兩個儲存體帳戶。 其中一個名為 `storage1` ，另一個名為 `storage2` 。 但是，您在部署歷程記錄中只會有一個專案。

當您為每個部署指定唯一的名稱時，您可以同時執行它們，而不會發生衝突。 如果您執行名為的部署， `newStorage1` 並部署名為的儲存體帳戶 `storage1` ，同時執行另一個名為 `newStorage2` 的部署，並部署名為的儲存體帳戶 `storage2` ，則您在部署歷程記錄中會有兩個儲存體帳戶和兩個專案。

為了避免與並行部署發生衝突，並確保部署歷程記錄中的唯一專案，請為每個部署提供一個唯一的名稱。

## <a name="deploy-remote-template"></a>部署遠端範本

您可能會想要將 ARM 範本儲存在您的本機電腦上，而不是在本機電腦上儲存它們。 您可以將範本儲存在原始檔控制存放庫 (例如 GitHub) 中。 或者，您可以將它們儲存在 Azure 儲存體帳戶中，以在組織內共用存取。

若要部署外部範本，請使用 **template-uri** 參數。 在範例中使用 URI 以部署來自 GitHub 的範例範本。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

上述範例針對範本需要可公開存取 URI，這適用於大部分的案例，因為您的範本不應該包含機密資料。 如果您需要指定機密資料 (例如系統管理員密碼)，請將該值以安全參數傳遞。 不過，如果不希望將範本公開存取，您可以將它儲存在私人儲存體容器中加以保護。 如需部署需要共用存取簽章 (SAS) 權杖之範本的相關資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。

## <a name="deploy-template-spec"></a>部署範本規格

您可以建立 [範本規格](template-specs.md)，而不是部署本機或遠端範本。範本規格是您的 Azure 訂用帳戶中包含 ARM 範本的資源。 這可讓您輕鬆安全地與組織中的使用者共用範本。 您可以使用角色型存取控制 (RBAC) 來授與範本規格的存取權。這項功能目前為預覽狀態。

下列範例示範如何建立和部署範本規格。只有當您已 [註冊預覽版](https://aka.ms/templateSpecOnboarding)時，才能使用這些命令。

首先，請提供 ARM 範本來建立範本規格。

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

然後，您會取得範本規格的識別碼，並加以部署。

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

如需詳細資訊，請參閱 [Azure Resource Manager 範本規格 (Preview) ](template-specs.md)。

## <a name="preview-changes"></a>預覽變更

在部署範本之前，您可以預覽範本將對您的環境進行的變更。 使用「 [假設](template-deploy-what-if.md) 」作業來確認範本會進行您預期的變更。 假設也會驗證範本是否有錯誤。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

在 Cloud Shell 中使用下列命令︰

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>參數

若要傳遞參數值，您可以使用內嵌參數或參數檔案。

### <a name="inline-parameters"></a>內嵌參數

若要傳遞內嵌參數，請提供 `parameters` 中的值。 例如，若要透過 Bash 殼層將字串和陣列傳遞至範本，請使用：

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

如果您是使用 Azure CLI 搭配 Windows 命令提示字元 (CMD) 或 PowerShell，請使用下列格式來傳遞陣列： `exampleArray="['value1','value2']"` 。

您也可以取得檔案內容，並提供該內容作為內嵌參數。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

當您需要提供組態值時，從檔案取得參數值會很有幫助。 例如，您可以提供 [Linux 虛擬機器的 cloud-init 值](../../virtual-machines/linux/using-cloud-init.md)。

arrayContent.json 的格式為：

```json
[
    "value1",
    "value2"
]
```

若要傳入物件（例如，若要設定標記），請使用 JSON。 例如，您的範本可能包含如下所示的參數：

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

在此情況下，您可以傳入 JSON 字串來設定參數，如下列 Bash 腳本所示：

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

在您想要傳遞給物件的 JSON 周圍使用雙引號。

### <a name="parameter-files"></a>參數檔案

相對於在您的指令碼中將參數做為內嵌值傳遞，使用包含該參數值的 JSON 檔案可能較為容易。 參數檔案必須是本機檔案。 Azure CLI 不支援外部參數檔案。

如需參數檔案的詳細資訊，請參閱[建立 Resource Manager 參數檔案](parameter-files.md)。

若要傳遞本機參數檔案，請使用 `@` 來指定名為 storage.parameters.json 的本機檔案。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>處理擴充的 JSON 格式

若要使用2.3.0 版或更舊版本的 Azure CLI 來部署具有多行字串或批註的範本，您必須使用 `--handle-extended-json-format` 參數。  例如：

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>後續步驟

- 在您收到錯誤時，若要回復為成功的部署，請參閱[錯誤回復至成功部署](rollback-on-error.md)。
- 若要指定如何處理存在於資源群組中、但尚未定義於範本中的資源，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。
- 若要瞭解如何在您的範本中定義參數，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
- 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](common-deployment-errors.md)。
- 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。
