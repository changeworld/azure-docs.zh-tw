---
title: 快速入門 - 使用 PowerShell 建立 Azure APIM 執行個體 | Microsoft Docs
description: 使用 Azure PowerShell 建立新的 Azure APIM 執行個體。
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/14/2020
ms.author: apimpm
ms.openlocfilehash: eb2c42d26a85a07518a018ba5b8817f13d3cd17f
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707060"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>快速入門：使用 PowerShell 建立新的 Azure API 管理服務執行個體

「Azure API 管理 (APIM)」可協助組織將 API 發佈給外部、合作夥伴及內部開發人員，以發揮其資料與服務的潛力。 「API 管理」提供的核心專長認證，透過開發人員參與、商務洞察力、分析、安全性和保護，可確保 API 程式獲致成功。 APIM 可讓您為裝載於任何位置的現有後端服務，建立和管理新式 API 閘道。 如需詳細資訊，請參閱[概觀](api-management-key-concepts.md)。

此快速入門描述使用 Azure PowerShell 指令碼建立新 APIM 執行個體的步驟。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本教學課程會要求使用 Azure PowerShell 模組版本 1.0 或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列命令會在美國西部地區建立名為 *myResourceGroup* 的資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>建立 API 管理服務

現在您已有資源群組，您可以接著建立 APIM 服務執行個體。 使用 [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) 建立執行個體，並提供服務名稱和發行者詳細資料。 服務名稱必須是 Azure 中唯一的。

在下列範例中，服務名稱會使用 *myapim*。 將名稱更新為唯一值。 也請更新 API 發行者的組織名稱，以及用來接收通知的系統管理員電子郵件地址。

根據預設，此命令會在開發人員層中建立執行個體，以此經濟實惠的選項評估 Azure APIM。 此階層不適用於生產環境。 如需有關調整 API 管理層規模的詳細資訊，請參閱[升級和調整規模](upgrade-and-scale.md)。

> [!NOTE]
> 這是長時間執行的作業。 這可能需要 30 到 40 分鐘的時間，才能在此階層中建立及啟用 APIM 服務。

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

當命令傳回時，請執行 [Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement) 來檢視 Azure APIM 服務的屬性。 啟用之後，佈建狀態會是 [成功]，而服務執行個體會有數個相關聯的 URL。 例如：\

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

範例輸出︰

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

部署您的 APIM 服務執行個體之後，您就可以開始使用了。 從[匯入和發佈第一個 API](import-and-publish.md) 教學課程開始。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組和所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [匯入和發佈您的第一個 API](import-and-publish.md)
