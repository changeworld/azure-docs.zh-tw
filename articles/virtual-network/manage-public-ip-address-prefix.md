---
title: 建立、變更或刪除 Azure 公用 IP 位址首碼
titlesuffix: Azure Virtual Network
description: 瞭解公用 IP 位址首碼，以及如何建立、變更或刪除它們。 請參閱尋找其他資訊的位置。
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: c844b25394d865453e653bfc3ac6bff396d97a47
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035054"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>建立、變更或刪除公用 IP 位址首碼

了解公用 IP 位址首碼，以及如何建立、變更和刪除公用 IP 位址首碼。 公用 IP 位址首碼是根據您所指定之公用 IP 位址數目的連續位址範圍。 這些位址會指派給您的訂用帳戶。 當您建立公用 IP 位址資源時，您可以從前置詞指派靜態公用 IP 位址，並將該位址與虛擬機器、負載平衡器或其他資源建立關聯，以啟用網際網路連線能力。 如果您不熟悉公用 IP 位址首碼，請參閱[公用 IP 位址首碼概觀](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任一節的步驟之前，請先完成下列工作︰

- 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果使用入口網站，請開啟 https://portal.azure.com，並使用您的 Azure 帳戶來登入。
- 如果使用 PowerShell 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/powershell) \(英文\) 中執行命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 本教學課程需要 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。
- 如果使用命令列介面 (CLI) 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/bash) \(英文\) 中執行命令，或從您的電腦執行 CLI。 本教學課程需要 Azure CLI 2.0.41 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果您在本機執行 Azure CLI，則也需要執行 `az login` 以建立與 Azure 的連線。

您登入或連線到 Azure 的帳戶必須指派為[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，或為已指派 [[許可權](#permissions)] 中所列適當動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

公用 IP 位址首碼需要付費。 如需詳細資料，請參閱[定價](https://azure.microsoft.com/pricing/details/ip-addresses)。

## <a name="create-a-public-ip-address-prefix"></a>建立公用 IP 位址首碼

1. 在入口網站的左上角，選取 [+ 建立資源]****。
2. 在 [*搜尋 Marketplace* ] 方塊中輸入*公用 ip 首碼*。 當 [公用 IP 位址首碼]**** 出現於搜尋結果時，將其選取。
3. 在 [公用 IP 位址首碼]**** 下方，選取 [建立]****。
4. 在 [建立公用 IP 位址首碼]**** 下方，輸入或選取下列設定的值，然後選取 [建立]****：

   |設定|必要？|詳細資料|
   |---|---|---|
   |訂用帳戶|是|所在的[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)必須與您想要與公用 IP 位址建立關聯的資源相同。|
   |資源群組|是|所在的[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)可以與您想要與公用 IP 位址建立關聯的資源相同或不同。|
   |名稱|是|名稱必須是您選取的資源群組中唯一的名稱。|
   |區域|是|必須與您從範圍指派位址的公用 IP 位址存在於相同[區域](https://azure.microsoft.com/regions)。|
   |首碼大小|是| 您需要的首碼大小。 /28 或 16 個 IP 位址為預設值。

**命令**

|工具|Command|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[新增-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>從首碼建立靜態公用 IP 位址
建立首碼之後，您必須從首碼建立靜態 IP 位址。 若要這樣做，請遵循下面的步驟。

1. 在 Azure 入口網站頂端包含「搜尋資源」** 文字的方塊中，鍵入「公用 IP 位址首碼」**。 當 [公用 IP 位址首碼]**** 出現於搜尋結果時，將其選取。
2. 選取您要從中建立公用 IP 的首碼。
3. 當它出現在搜尋結果時，請選取它，然後按一下 [概觀] 區段中的 [+ 新增 IP 位址]****。
4. 在 [建立公用 IP 位址]**** 下方，輸入或選取下列設定的值。 由於首碼適用於標準 SKU、IPv4 和靜態，因此您只需要提供下列資訊：

   |設定|必要？|詳細資料|
   |---|---|---|
   |名稱|是|公用 IP 位址名稱在您選取的資源群組中必須是唯一。|
   |閒置逾時 (分鐘)|否|不需依賴用戶端傳送保持連線訊息，讓 TCP 或 HTTP 連線保持開啟的分鐘數。 |
   |DNS 名稱標籤|否|在您建立名稱的 Azure 區域 (跨越所有訂用帳戶和所有客戶) 中必須是唯一。 Azure 會在其 DNS 中自動登錄名稱和 IP 位址，以便您連線至具有此名稱的資源。 Azure 會將 *location.cloudapp.azure.com* (其中 location 是您選取的位置) 之類的預設子網路附加至您提供的名稱，以建立完整的 DNS 名稱。如需詳細資訊，請參閱[使用具有 Azure 公用 IP 位址的 Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)。|

或者，您可以使用下列 CLI 和 PS 命令搭配--public-ip 首碼 (CLI) 和-PublicIpPrefix (PS) 參數，以建立公用 IP 位址資源。 

|工具|Command|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>檢視或刪除首碼

1. 在 Azure 入口網站頂端包含「搜尋資源」** 文字的方塊中，鍵入「公用 IP 位址首碼」**。 當 [公用 IP 位址首碼]**** 出現於搜尋結果時，將其選取。
2. 選取您要檢視、變更設定，或從清單中刪除的公用 IP 位址首碼名稱。
3. 根據您要檢視、刪除或變更公用 IP 位址首碼，完成下列其中一個選項。
   - **檢視**：[概觀]**** 區段會顯示公用 IP 位址首碼的索引鍵設定，例如首碼。
   - **刪除**：若要刪除公用 IP 位址首碼，請在 [概觀]**** 區段中選取 [刪除]****。 如果首碼內的位址與公用 IP 位址資源相關聯，則您必須先刪除公用 IP 位址資源。 請參閱[刪除公用 IP 位址](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address)。

**命令**

|工具|Command|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) 可列出公用 IP 位址、[az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) 可顯示設定；[az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) 可進行更新；[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) 可進行刪除|
|PowerShell|[AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix)以取出公用 IP 位址物件，並查看其設定， [AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix)以更新設定;[移除-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix)以刪除|

## <a name="permissions"></a>權限

若要針對公用 IP 位址首碼執行工作，您的帳戶必須指派為[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，或為已指派下表所列適當動作的[自訂](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| 動作                                                            | 名稱                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | 讀取公用 IP 位址首碼                                |
| Microsoft.Network/publicIPPrefixes/write                          | 建立或更新公用 IP 位址首碼                    |
| Microsoft.Network/publicIPPrefixes/delete                         | 刪除公用 IP 位址首碼                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | 從首碼建立公用 IP 位址 |

## <a name="next-steps"></a>後續步驟

- 了解使用[公用 IP 首碼](public-ip-address-prefix.md)的案例和優點
