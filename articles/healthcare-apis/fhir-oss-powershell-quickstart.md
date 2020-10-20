---
title: PowerShell：部署適用於 Azure 的 FHIR 伺服器 – Azure API for FHIR
description: 本快速入門說明如何使用 PowerShell 部署 Microsoft 開放原始碼 FHIR 伺服器。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c6c1a7e21f0a1554c67c7f1860a2bd3382c941f5
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817961"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>快速入門：使用 PowerShell 部署開放原始碼 FHIR 伺服器

在本快速入門中，了解如何使用 PowerShell 部署適用於 Azure 的 Microsoft Open Source FHIR 伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>建立資源群組

挑選將包含已佈建資源的資源群組名稱並加以建立：

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>部署 FHIR 伺服器範本

適用於 Azure [GitHub 存放庫](https://github.com/Microsoft/fhir-server)的 Microsoft FHIR Server 包含會部署所有必要資源的範本。 部署程序需要幾分鐘的時間，才會建立並設定必要的 Azure 資源。 部署方式：

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> 如果您尚未登入，請先執行下列命令。

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

若要使用現有的資源群組，請變更 $rg 變數定義行和 Azure ARM 範本部署命令列中的 $rg 值，如程式碼所示。

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>確認 FHIR 伺服器正在執行

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

這需要一分鐘左右的時間，伺服器才會第一次回應。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將適用於 Azure 的 Microsoft 開放原始碼 FHIR 伺服器部署至您的訂用帳戶。 若要了解如何使用 Postman 存取 FHIR API，請繼續進行 Postman 教學課程。
 
>[!div class="nextstepaction"]
>[使用 Postman 存取 FHIR API](access-fhir-postman-tutorial.md)
