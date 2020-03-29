---
title: 使用 Azure PowerShell 的 Azure 流量管理器子網覆蓋 |微軟文檔
description: 本文將説明您瞭解如何使用流量管理器子網覆蓋來覆蓋流量管理器設定檔的路由方法，以便通過預定義的 IP 範圍將流量定向到基於最終使用者 IP 位址的終結點，方法是使用 Azure 向終結點映射電源外殼。
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938426"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>使用 Azure 電源外殼進行流量管理器子網覆蓋

流量管理器子網覆蓋允許您更改設定檔的路由方法。  添加覆蓋將基於最終使用者的 IP 位址（具有預定義的 IP 範圍）將流量定向到終結點映射。 

## <a name="how-subnet-override-works"></a>子網覆蓋的工作原理

將子網覆蓋添加到流量管理器設定檔時，流量管理器將首先檢查最終使用者的 IP 位址是否有子網覆蓋。 如果找到一個，使用者的 DNS 查詢將定向到相應的終結點。  如果未找到映射，流量管理器將回退到設定檔的原始路由方法。 

IP 位址範圍可以指定為 CIDR 範圍（例如，1.2.3.0/24）或位址範圍（例如 1.2.3.4-5.6.7.8）。 與每個終結點關聯的 IP 範圍必須是唯一的。 不同終結點之間的 IP 範圍的任何重疊將導致流量管理器拒絕設定檔。

有兩種類型的路由設定檔支援子網覆蓋：

* **地理位置**- 如果流量管理器發現 DNS 查詢的 IP 位址的子網覆蓋，它將路由查詢到終結點，無論終結點的運行狀況是什麼。
* **性能**- 如果流量管理器發現 DNS 查詢的 IP 位址的子網覆蓋，則僅當流量正常時，它才會將流量路由到終結點。  如果子網重寫終結點不正常，流量管理器將回落到性能路由啟發式。

## <a name="create-a-traffic-manager-subnet-override"></a>創建流量管理器子網覆蓋

要創建流量管理器子網覆蓋，可以使用 Azure PowerShell 將覆蓋的子網添加到流量管理器終結點。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以執行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中採用的命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式殼層。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 如果您從電腦執行 PowerShell，就需要 Azure PowerShell 模組 1.0.0 或更新版本。 您可以執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，也需要執行 `Login-AzAccount` 來登入 Azure。


1. **檢索流量管理器終結點：**

    若要啟用子網覆蓋，檢索要向其添加覆蓋的終結點，並使用[Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)將其存儲在變數中。

    將名稱、設定檔名稱和資源組名稱替換為要更改的終結點的值。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **將 IP 位址範圍添加到終結點：**
    
    要將 IP 位址範圍添加到終結點，請使用[Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0)添加該範圍。

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    添加範圍後，使用[集-AzTrafficManager終結點](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0)更新終結點。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
刪除 IP 位址範圍可以通過使用[刪除-AztrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0)完成。

1.  **檢索流量管理器終結點：**

    若要啟用子網覆蓋，檢索要向其添加覆蓋的終結點，並使用[Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)將其存儲在變數中。

    將名稱、設定檔名稱和資源組名稱替換為要更改的終結點的值。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **從終結點中刪除 IP 位址範圍：**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     刪除範圍後，請使用[集-AzTrafficManager終結點](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0)更新終結點。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>後續步驟
深入了解「流量管理員」的 [流量路由方法](traffic-manager-routing-methods.md)。

瞭解[子網流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
