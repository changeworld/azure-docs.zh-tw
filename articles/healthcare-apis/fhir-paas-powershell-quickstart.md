---
title: 快速入門：使用 PowerShell 部署適用於 FHIR 的 Azure API
description: 在本快速入門中，您將了解如何使用 PowerShell 部署 Azure API for FHIR。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: mihansen
ms.openlocfilehash: d7156543a66cdf50d7cfddec27e685429324f9e6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "84819994"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>快速入門：使用 PowerShell 部署適用於 FHIR 的 Azure API

在本快速入門中，您將了解如何使用 PowerShell 部署 Azure API for FHIR。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>註冊 Azure API for FHIR 資源提供者

如果 `Microsoft.HealthcareApis` 資源提供者尚未針對您的訂用帳戶註冊誤，您可加以註冊：

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>建立 Azure 資源群組

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>部署 Azure API for FHIR

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> 視您已安裝的 `Az` PowerShell 模組版本而定，已佈建的 FHIR 伺服器可能會設定為使用[本機 RBAC](configure-local-rbac.md)，且在已部署 FHIR 服務的允許身分識別物件識別碼清單中設定目前已登入的 PowerShell 使用者。 接下來，建議您[使用 Azure RBAC](configure-azure-rbac.md) 來指派資料平面角色，而您可能需要在部署後刪除此使用者物件識別碼，以啟用 Azure RBAC 模式。


## <a name="fetch-capability-statement"></a>Fetch 功能陳述式

藉由擷取 FHIR 功能陳述式，就能夠驗證 Azure API for FHIR 帳戶是否正在執行：

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>後續步驟

在本快速入門手冊中，您已將 Azure API for FHIR 部署至您的訂用帳戶。 若要在 Azure API for FHIR 中設定其他設定，請繼續進行其他設定操作指南。

>[!div class="nextstepaction"]
>[Azure API for FHIR 中的其他設定](azure-api-for-fhir-additional-settings.md)
