---
title: 重設 Azure VPN 閘道以重新建立 IPsec 通道
description: 重設 Azure VPN 閘道，以在傳統和 Resource Manager 部署模型中重新建立 VPN 閘道的 IPsec 通道。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: e39884f6d62fc43943f892aed0dac650a01d6c40
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462909"
---
# <a name="reset-a-vpn-gateway"></a>重設 VPN 閘道

如果您遺失一或多個站對站 VPN 通道上的跨單位 VPN 連線，重設 Azure VPN 閘道會很有幫助。 在此情況下，您的所有內部部署 VPN 裝置都會運作正常，但無法使用 Azure VPN 閘道建立 IPsec 通道。 本文可協助您重設 VPN 閘道。

### <a name="what-happens-during-a-reset"></a>重設期間會發生什麼事？

VPN 閘道是由兩個在「作用中-待命」設定中執行的 VM 執行個體所組成。 重設閘道器時會重新啟動閘道，然後將跨單位設定重新套用至閘道器。 閘道會保留它永遠擁有的公用 IP 位址。 這表示您不需要利用 Azure VPN 閘道的新公用 IP 位址更新 VPN 路由器組態。

當您發出重設閘道的命令時，系統會立即重新啟動 Azure VPN 閘道目前作用中的執行個體。 從作用中執行個體 (正在重新啟動) 容錯移轉到待命執行個體期間，會有短暫的間隔。 此間隔應該不超過一分鐘。

如果連接未在第一次重新啟動之後儲存，請再次發出相同的命令來重新啟動第二個 VM 執行個體 (新的使用中閘道器)。 如果接連要求兩次重新啟動，兩個 VM 執行個體 (作用中和待命) 的重新啟動時間還要更久一點。 這會導致 VPN 連線的間隔較長，Vm 的最多30到45分鐘才能完成重新開機。

在兩次重新啟動之後，如果仍有跨單位連線問題，請從 Azure 入口網站開啟支援要求。

## <a name="before-you-begin"></a><a name="before"></a>開始之前

在您重設您的閘道器之前，請確認以下所列每個 IPsec 網站間 (S2S) VPN 通道的重要項目。 任何項目的不相符都會導致 S2S VPN 通道中斷連線。 驗證並更正內部部署和 Azure VPN 閘道的組態可為您避免不必要的重新開機，也避免閘道器上的其他作用中連線中斷。

重設您的閘道器之前請確認下列項目：

* Azure VPN 閘道與在內部部署 VPN 閘道的網際網路 IP 位址 (VIP) 已利用 Azure 和內部部署 VPN 原則正確設定。
* 預先共用的金鑰在 Azure 和內部部署 VPN 閘道上必須是相同的。
* 如果您套用特定的 IPsec/IKE 組態，例如加密、雜湊演算法和 PFS (完整轉寄密碼)，確定 Azure 和內部部署 VPN 閘道都具有相同組態。

## <a name="azure-portal"></a><a name="portal"></a>Azure 入口網站

您可以使用 Azure 入口網站重設 Resource Manager VPN 閘道。 如果您想要重設傳統閘道，請參閱 [傳統部署模型](#resetclassic)的 PowerShell 步驟。

### <a name="resource-manager-deployment-model"></a>資源管理員部署模型。

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>資源管理員部署模型。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Reset-AzVirtualNetworkGateway** 是可用來重設閘道的 Cmdlet。 執行重設之前，請確定您有最新版的 [PowerShell Az Cmdlet](https://docs.microsoft.com/powershell/module/az.network)。 下列範例會重設 TestRG1 資源群組中名為 VNet1GW 的虛擬網路閘道：

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

結果：

當您收到傳回的結果時，可能會認為閘道已成功重設。 不過，傳回結果中並沒有明確指出已成功重設。 如果您想要進一步查看歷程記錄，以確實掌握閘道重設發生的時間，您可以在 [Azure 入口網站](https://portal.azure.com)中檢視該項資訊。 在入口網站中，瀏覽至 'GatewayName' -> [資源健康狀態]****。

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>傳統部署模型

**Reset-AzureVNetGateway** 是可用來重設閘道的 Cmdlet。 適用于服務管理的 Azure PowerShell Cmdlet 必須安裝在您的桌面本機。 您無法使用 Azure Cloud Shell。 執行重設之前，請確認您已安裝最新版的 [Service Management (SM) PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets)。 使用此命令時，請確定您使用的是虛擬網路的完整名稱。 使用入口網站建立的傳統 Vnet 具有 PowerShell 所需的完整名稱。 您可以使用 ' >set-azurevnetconfig-ExportToFile C:\Myfoldername\NetworkConfig.xml ' 來查看完整名稱。

下列範例會重設名為 "Group TestRG1 TestVNet1" 之虛擬網路的閘道， (在入口網站中顯示為「TestVNet1」) ：

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

結果：

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="azure-cli"></a><a name="cli"></a>Azure CLI

若要重設閘道，請使用 [az network vnet-gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway) 命令。 下列範例會重設 TestRG5 資源群組中名為 VNet5GW 的虛擬網路閘道：

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

結果：

當您收到傳回的結果時，可能會認為閘道已成功重設。 不過，傳回結果中並沒有明確指出已成功重設。 如果您想要進一步查看歷程記錄，以確實掌握閘道重設發生的時間，您可以在 [Azure 入口網站](https://portal.azure.com)中檢視該項資訊。 在入口網站中，瀏覽至 'GatewayName' -> [資源健康狀態]****。
