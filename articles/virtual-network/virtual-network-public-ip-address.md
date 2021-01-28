---
title: 管理公用 IP 位址 |Microsoft Docs
titleSuffix: Azure Virtual Network
description: 管理公用 IP 位址。  此外，您也可以瞭解公用 IP 位址是具有自己可設定之設定的資源。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: d52430c87d99f8837c78fcff89d8b214e45350ff
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934941"
---
# <a name="manage-public-ip-addresses"></a>管理公用 IP 位址

了解公用 IP 位址，以及如何建立、變更和刪除公用 IP 位址。 公用 IP 位址是可加以設定的資源。 將公用 IP 位址指派給支援公用 IP 位址的 Azure 資源，可以：
- 啟用從網路網路到資源的輸入通訊；資源包括 Azure 虛擬機器 (VM)、Azure 應用程式閘道、Azure Load Balancer、Azure VPN 閘道等等。 即使未將公用 IP 位址指派給該虛擬機器，只要虛擬機器是負載平衡器後端集區的一部分，且已將公用 IP 位址指派給負載平衡器，就可以讓您與來自網際網路的虛擬機器等資源進行通訊。 若要判斷資源是否可以將公用 IP 位址指派給特定的 Azure 服務，或者是否可以透過不同 Azure 資源的公用 IP 位址與其進行通訊，請參閱服務的說明文件。
- 使用可預測 IP 位址對網際網路進行輸出連線。 例如，虛擬機器不需有指派的公用 IP 位址，即可對網際網路進行輸出通訊，但其位址是由 Azure 轉譯而成的網路位址 (依預設是無法預測的公用位址)。 指派公用 IP 位址給資源，可讓您知道輸出連線所使用的 IP 位址。 雖然可預測，但位址可能根據選擇的指派方法而有所變更。 如需詳細資訊，請參閱＜[建立公用 IP 位址](#create-a-public-ip-address)＞。 若要深入了解 Azure 資源的輸出連線，請參閱[了解輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任一節的步驟之前，請先完成下列工作︰

- 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果使用入口網站，請開啟 https://portal.azure.com，並使用您的 Azure 帳戶來登入。
- 如果使用 PowerShell 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/powershell) \(英文\) 中執行命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 本教學課程需要 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。
- 如果使用命令列介面 (CLI) 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/bash) \(英文\) 中執行命令，或從您的電腦執行 CLI。 本教學課程需要 Azure CLI 2.0.31 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您在本機執行 Azure CLI，則也需要執行 `az login` 以建立與 Azure 的連線。

您登入或連線到 Azure 的帳戶必須指派給「[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」角色，或指派給已獲指派 [[許可權](#permissions)] 中所列適當動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

公用 IP 位址需要少許費用。 若要檢視價格，請閱讀 [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)頁面。

## <a name="create-a-public-ip-address"></a>建立公用 IP 位址

如需如何使用入口網站、PowerShell 或 CLI 建立公用 IP 位址的指示，請參閱下列頁面：

 * [建立公用 IP addresses - 入口網站](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [建立公用 IP addresses - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [建立公用 IP addresses - Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>雖然入口網站提供可讓您建立兩個公用 IP 位址資源 (一個 IPv4 和一個 IPv6) 的選項，但 PowerShell 和 CLI 命令會建立一個資源，其中包含一個 IP 版本或另一個 IP 版本的位址。 如果您想要兩個公用 IP 位址資源（每個 IP 版本各一個），您必須執行命令兩次，指定公用 IP 位址資源的不同名稱和 IP 版本。

如需建立時公用 IP 位址之特定屬性的詳細資訊，請參閱下表。

   |設定|必要？|詳細資料|
   |---|---|---|
   |IP 版本|是| 選取 [IPv4] 或 [IPv6] 或 [兩者]。 選取兩者都會導致2個公用 IP 位址建立-1 個 IPv4 位址和1個 IPv6 位址。 深入瞭解 [Azure vnet 中的 IPv6](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。|
   |SKU|是|在 Sku 推出之前建立的所有公用 IP 位址都是 **基本** SKU 公用 ip 位址。 建立公用 IP 位址之後，即無法變更 SKU。 獨立虛擬機器、可用性設定組內的虛擬機器，或虛擬機器擴展集，可以使用基本或標準 SKU。 不允許在可用性設定組或擴展集或獨立 Vm 內的虛擬機器之間混合使用 Sku。 **基本** SKU：如果您要在支援可用性區域的區域中建立公用 IP 位址，**可用性區域** 設定依預設會設為「無」。 基本公用 Ip 不支援可用性區域。 **標準** SKU：標準 SKU 公用 IP 可與虛擬機器或負載平衡器前端建立關聯。 如果您要在支援可用性區域的區域中建立公用 IP 位址，**可用性區域** 設定依預設會設為「區域備援」。 如需可用性區域的詳細資訊，請參閱 **可用性區域** 設定。 如果您要將位址與標準負載平衡器建立關聯，則需要標準 SKU。 若要深入了解標準負載平衡器，請參閱 [Azure 負載平衡器標準 SKU](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 當您將標準 SKU 的公用 IP 位址指派給虛擬機器的網路介面時，必須使用[網路安全性群組](./network-security-groups-overview.md#network-security-groups)明確地允許預定的流量。 在建立和關聯網路安全性群組並明確地允許所要流量前，與資源進行的通訊都會失敗。|
   |服務層級|是|指出 IP 位址是否與區域 (**區域**) ，或從多個區域的「任意傳播」 (**全域**) 。 *請注意，「全域層」 IP 是標準 ip 的預覽功能，目前僅供跨區域 Load Balancer 使用*。|
   |名稱|是|名稱必須是您選取的資源群組中唯一的名稱。|
   |IP 位址指派|是|**動態︰** 只有在公用 IP 位址與 Azure 資源相關聯，且第一次啟動資源之後，才會指派動態位址。 動態位址指派給資源時可以變更 (例如指派給虛擬機器，而虛擬機器停止 (解除配置)，然後再重新開機)。 如果虛擬機器已重新開機或停止 (但未解除配置)，則位址維持不變。 當公用 IP 位址資源與其所關聯的資源中斷關聯時，便會釋放動態位址。 **靜態︰** 建立公用 IP 位址時會指派靜態位址。 刪除公用 IP 位址資源之前，不會釋出靜態位址。 如果位址與資源沒有關聯，您可以在位址建立後變更指派方法。 如果位址與資源相關聯，您可能無法變更指派方法。 如果您針對 **IP 版本** 選取 *IPv6* ，則基本 SKU 的指派方法必須是 *動態* 的。  適用于 IPv4 和 IPv6 的標準 SKU 位址是 *靜態* 的。 |
   |閒置逾時 (分鐘)|否|不需依賴用戶端傳送保持連線訊息，讓 TCP 或 HTTP 連線保持開啟的分鐘數。 如果您選取 IPv6 作為 **IP 版本**，則無法變更此值。 |
   |DNS 名稱標籤|否|在您建立名稱的 Azure 位置 (跨越所有訂用帳戶和所有位置) 中必須是唯一的。 Azure 會在其 DNS 中自動登錄名稱和 IP 位址，以便您連線至具有此名稱的資源。 Azure 會將 *location.cloudapp.azure.com* (其中 location 是您選取的位置) 之類的預設子網路附加至您提供的名稱 ，以建立完整的 DNS 名稱。 如果您選擇兩個位址版本都建立，則會指派相同的 DNS 名稱給 IPv4 和 IPv6 位址。 Azure 預設 DNS 包含 IPv4 A 和 IPv6 AAAA 名稱記錄，並且會在查詢 DNS 名稱時回應這兩個記錄。 用戶端選擇要與哪一個位址 (IPv4 或 IPv6) 通訊。 可改為 (或同時) 使用具有預設尾碼的 DNS 名稱標籤，您可以使用 Azure DNS 服務來設定 DNS 名稱，其具有解析為公用 IP 位址的自訂尾碼。 如需詳細資訊，請參閱[使用具有 Azure 公用 IP 位址的 Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)。|
   |只有當您選取 **兩個**) 的 IP 版本時，才會顯示 [名稱] (|是，如果您選取 **兩者** 的 IP 版本|該名稱必須與此清單中的第一個 **名稱** 不同。 如果您選擇同時建立 IPv4 和 IPv6 位址，則入口網站會建立兩個個別的公用 IP 位址資源，並各指派一個 IP 位址版本。|
   |IP 位址指派 (只有在您選取 **兩個**) 的 ip 版本時才會顯示|是，如果您選取 **兩者** 的 IP 版本|與上述 IP 位址指派相同的限制|
   |訂用帳戶|是|必須存在於與您將與公用 IP 相關聯之資源的相同 [訂](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 用帳戶中。|
   |資源群組|是|可以存在於與公用 IP 建立關聯的資源相同或不同的 [資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) 中。|
   |位置|是|必須存在於相同的 [位置](https://azure.microsoft.com/regions)（也稱為區域），作為您將與公用 IP 建立關聯的資源。|
   |可用性區域| 否 | 只有在您選取受支援的位置時，才會出現此設定。 如需受支援位置的清單，請參閱[可用性區域概觀](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 如果您選取 **基本** SKU，則會自動為您選取「無」。 如果您想要保證特定區域，可選取特定區域。 或選擇非區域備援。 如果您選取 **標準** SKU：會自動為您選取區域備援，並針對區域失敗進行資料路徑復原。 如果您希望保證對區域失敗無法復原的特定區域，則可選取特定區域。

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>查看、修改公用 IP 位址的設定，或刪除公用 IP 位址

   - **視圖/清單**：若要檢查公用 IP 的設定，包括 SKU、位址、任何適用的關聯 (例如虛擬機器 NIC、Load Balancer 前端) 。
   - **修改**：使用 [建立公用 IP 位址](#create-a-public-ip-address)的步驟4中的資訊來修改設定，例如閒置 timeout、DNS 名稱標籤或指派方法。   (如需將公用 IP SKU 從基本升級至標準的完整程式，請參閱 [升級 Azure 公用 ip 位址](./virtual-network-public-ip-address-upgrade.md)。 ) 
   >[!WARNING]
   >若要將公用 IP 位址的指派從靜態變更為動態，您必須先中斷任何適用 IP 設定的位址關聯 (請參閱 **刪除** 區段) 。  另請注意，當您將指派方法從靜態變更為動態時，會遺失指派給公用 IP 位址的 IP 位址。 雖然 Azure 公用 DNS 伺服器會維護靜態或動態位址與任何 DNS 名稱標籤 (如果您定義一個位置) 之間的對應，但是動態 IP 位址可能會在虛擬機器處於停止 (已解除配置) 狀態後啟動時變更。 若要防止位址變更，請指派靜態 IP 位址。
   
|作業|Azure 入口網站|Azure PowerShell|Azure CLI|
|---|---|---|---|
|檢視 | 在公用 IP 的 [ **總覽** ] 區段中 |[>get-azpublicipaddress](/powershell/module/az.network/get-azpublicipaddress) 以取得公用 IP 位址物件並查看其設定| [az 網路公用 ip 顯示](/cli/azure/network/public-ip#az-network-public-ip-show) 顯示設定|
|List | 在 [ **公用 IP 位址** ] 類別下 |[>get-azpublicipaddress](/powershell/module/az.network/get-azpublicipaddress) 以取得一或多個公用 IP 位址物件並查看其設定|[az 網路公用 ip 清單](/cli/azure/network/public-ip#az-network-public-ip-list) 可列出公用 ip 位址|
|修改 | 若為中斷關聯的 IP，請 **選取 [** 設定] 以修改閒置的超時時間、DNS 名稱標籤，或將基本 IP 的變更指派從靜態變更為動態  |[>get-azpublicipaddress](/powershell/module/az.network/set-azpublicipaddress) 以更新設定 |[az 網路公用 ip 更新](/cli/azure/network/public-ip#az-network-public-ip-update) 以進行更新 |

   - **刪除**：公用 ip 的刪除要求公用 ip 物件不會與任何 IP 設定或虛擬機器 NIC 相關聯。 如需詳細資訊，請參閱下表。

|資源|Azure 入口網站|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[虛擬機器](./remove-public-ip-address-vm.md)|選取 [中斷關聯] 以中斷 IP 位址與 NIC 設定的關聯， **然後選取 [** **刪除**]。|[>get-azpublicipaddress](/powershell/module/az.network/set-azpublicipaddress) 會中斷 IP 位址與 NIC 設定的關聯; [移除->get-azpublicipaddress](/powershell/module/az.network/remove-azpublicipaddress) 以刪除|[az network public-ip update--移除](/cli/azure/network/public-ip#az-network-public-ip-update)以中斷 ip 位址與 NIC 設定的關聯;az 要刪除的[網路公用 ip 刪除](/cli/azure/network/public-ip#az-network-public-ip-delete) |
|Load Balancer 前端 | 流覽至未使用的公用 IP 位址，然後選取 [ **關聯** ] 並挑選具有相關前端 IP 設定的 Load Balancer 來取代它 (然後可以使用與 VM 相同的方法來刪除舊 IP)   | [>new-azloadbalancerfrontendipconfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) 會將新的前端 IP 設定與公用 Load Balancer 建立關聯; [移除->get-azpublicipaddress](/powershell/module/az.network/remove-azpublicipaddress) 以刪除;如果有一個以上的 IP 設定，也可以使用 [>new-azloadbalancerfrontendipconfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) 來移除前端 IP 設定 |[az network lb 前端-ip 更新](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) ，以將新的前端 ip 設定與公用 Load Balancer 建立關聯; [移除->get-azpublicipaddress](/powershell/module/az.network/remove-azpublicipaddress) 以刪除;也可以使用 [az network lb 前端-ip 刪除](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) 來移除前端 ip 設定（如果有一個以上）|
|防火牆|N/A| [解除配置 ( # B1 ](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) 以解除配置防火牆並移除所有 IP 設定 | [az network firewall ip-config delete](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) 以移除 ip (但必須使用 PowerShell 來解除配置第一個) |

## <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

使用具有公用 Ip 的虛擬機器擴展集時，不會有不同的公用 IP 物件與個別的虛擬機器實例相關聯。 不過，您可以使用公用 IP 前置詞物件 [來產生實例 ip](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)。

若要列出虛擬機器擴展集上的公用 Ip，您可以使用 PowerShell ([>get-azpublicipaddress-VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) 或 CLI ([az vmss list-instance-Public-ip](/cli/azure/vmss#az_vmss_list_instance_public_ips)) 。

如需詳細資訊，請參閱 [Azure 虛擬機器擴展集的網路功能](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)。

## <a name="assign-a-public-ip-address"></a>指派公用 IP 位址

了解如何將公用 IP 位址指派給下列資源：

- [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或[Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)虛擬機器 (建立) 或[現有的虛擬機器](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [公用 Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [應用程式閘道](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 VPN 閘道的站對站連線](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [虛擬機器擴展集](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>權限

若要針對公用 IP 位址執行工作，您的帳戶必須指派為[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，或為已指派下表所列適當動作的[自訂](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| 動作                                                             | 名稱                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | 讀取公用 IP 位址                                          |
| Microsoft.Network/publicIPAddresses/write                          | 建立或更新公用 IP 位址                           |
| Microsoft.Network/publicIPAddresses/delete                         | 刪除公用 IP 位址                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | 將公用 IP 位址與資源建立關聯                    |

## <a name="next-steps"></a>後續步驟

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 範例指令碼，或使用 Azure [Resource Manager 範本](template-samples.md)建立公用 IP 位址
- 建立並指派公用 IP 位址的[Azure 原則定義](./policy-reference.md)
