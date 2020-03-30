---
title: 使用 SAS 權杖安全地部署範本
description: 使用受 SAS 權杖保護的 Azure 資源管理器範本將資源部署到 Azure。 顯示 Azure 電源外殼和 Azure CLI。
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156390"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>使用 SAS 權杖部署專用 ARM 範本

當 Azure 資源管理器 （ARM） 範本位於存儲帳戶中時，可以限制對範本的訪問，以避免公開公開範本。 通過為範本創建共用訪問簽名 （SAS） 權杖並在部署期間提供該權杖來訪問安全範本。 本文介紹如何使用 Azure PowerShell 或 Azure CLI 部署具有 SAS 權杖的範本。

## <a name="create-storage-account-with-secured-container"></a>使用安全容器創建存儲帳戶

以下腳本創建已關閉公共訪問的存儲帳戶和容器。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>將範本上載到存儲帳戶

現在，您已準備好將範本上載到存儲帳戶。 提供要使用的範本的路徑。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>在部署期間提供 SAS Token

若要在儲存體帳戶中部署私人範本，請產生 SAS Token 並將它包含在範本的 URI 中。 設定到期時間，以允許足夠的時間來完成部署。

> [!IMPORTANT]
> 包含範本的 Blob 只能由帳戶擁有者訪問。 不過，當您建立 Blob 的 SAS Token 時，具備該 URI 的任何人都可以存取該 Blob。 如果另一位使用者攔截了 URI，該使用者也能存取範本。 SAS 權杖是限制對範本的訪問的好方法，但不應直接在範本中包含密碼等敏感性資料。
>

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

如需使用包含已連結範本的 SAS Token 範例，請參閱 [透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。


## <a name="next-steps"></a>後續步驟
* 有關部署範本的簡介，請參閱[使用 ARM 範本和 Azure PowerShell 部署資源](deploy-powershell.md)。
* 若要在範本中定義參數，請參閱 [編寫範本](template-syntax.md#parameters)。
