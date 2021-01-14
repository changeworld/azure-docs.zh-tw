---
title: 使用 Azure PowerShell Azure 流量管理員子網覆寫 |Microsoft Docs
description: 本文將協助您瞭解如何使用流量管理員子網覆寫來覆寫流量管理員設定檔的路由方法，以使用 Azure PowerShell，透過預先定義的 IP 範圍，將流量導向端點。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 502533b69058eacd4ad18a3b29a33fbc4a3715a5
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183757"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>使用 Azure Powershell 的流量管理員子網覆寫

流量管理員子網覆寫可讓您變更設定檔的路由方法。  新增覆寫會根據終端使用者的 IP 位址將流量導向至端點對應的預先定義 IP 範圍。 

## <a name="how-subnet-override-works"></a>子網覆寫的運作方式

將子網覆寫新增至流量管理員設定檔時，流量管理員會先檢查終端使用者的 IP 位址是否有子網覆寫。 如果找到一個，就會將使用者的 DNS 查詢導向至對應的端點。  如果找不到對應，流量管理員會切換回設定檔的原始路由方法。 

您可以將 IP 位址範圍指定為 CIDR 範圍 (例如，1.2.3.0/24) 或位址範圍 (例如 1.2.3.4-5.6.7.8) 。 與每個端點相關聯的 IP 範圍對該端點而言必須是唯一的。 不同端點之間的任何 IP 範圍重迭，都會導致流量管理員拒絕設定檔。

有兩種類型的路由設定檔可支援子網覆寫：

* **地理** -如果流量管理員找到 DNS 查詢之 IP 位址的子網覆寫，它就會將查詢路由傳送至端點的任何健康情況。
* **效能** -如果流量管理員找到 DNS 查詢之 IP 位址的子網覆寫，它只會將流量路由至端點（如果其狀況良好）。  如果子網覆寫端點的狀況不良，流量管理員會切換回效能路由啟發學習法。

## <a name="create-a-traffic-manager-subnet-override"></a>建立流量管理員子網覆寫

若要建立流量管理員子網覆寫，您可以使用 Azure PowerShell 將覆寫的子網新增至流量管理員端點。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以執行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中採用的命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式殼層。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 如果您從電腦執行 PowerShell，就需要 Azure PowerShell 模組 1.0.0 或更新版本。 您可以執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，也需要執行 `Login-AzAccount` 來登入 Azure。


1. **取出流量管理員端點：**

    若要啟用子網覆寫，請取得您想要新增覆寫的端點，然後使用 [AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)將其儲存在變數中。

    以您要變更的端點值取代 Name、ProfileName 和 ResourceGroupName。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **將 IP 位址範圍新增至端點：**
    
    若要將 IP 位址範圍新增至端點，您將使用 [AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) 新增範圍。

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    一旦加入範圍之後，請使用 [AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) 來更新端點。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
您可以使用 [Remove-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0)來完成移除 IP 位址範圍。

1.  **取出流量管理員端點：**

    若要啟用子網覆寫，請取得您想要新增覆寫的端點，然後使用 [AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)將其儲存在變數中。

    以您要變更的端點值取代 Name、ProfileName 和 ResourceGroupName。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **從端點移除 IP 位址範圍：**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     移除範圍之後，請使用 [AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) 來更新端點。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>後續步驟
深入了解「流量管理員」的 [流量路由方法](traffic-manager-routing-methods.md)。

深入瞭解 [子網流量路由方法](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)