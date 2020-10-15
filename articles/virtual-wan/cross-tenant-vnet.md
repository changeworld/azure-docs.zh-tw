---
title: 將跨租使用者 Vnet 連線至中樞： PowerShell
titleSuffix: Azure Virtual WAN
description: 本文可協助您使用 PowerShell 將跨租使用者 Vnet 連線至虛擬中樞。
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: 875fd40fea315269f7fe72032942c40551a6b144
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078964"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>將跨租使用者 Vnet 連接到虛擬 Wan 中樞

本文可協助您使用虛擬 WAN 將 VNet 連線至不同租使用者中的虛擬中樞。 如果您的用戶端工作負載必須連接到相同的網路，但位於不同的租使用者，則此架構會很有用。 例如，如下圖所示，您可以將非 Contoso VNet (遠端租使用者) 連接至 (父租使用者) 的 Contoso 虛擬中樞。

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="設定路由設定" :::

在本文中，您將學會如何：

* 將另一個租使用者新增為 Azure 訂用帳戶的參與者。
* 將跨租使用者 VNet 連線至虛擬中樞。

使用 Azure 入口網站和 PowerShell 的組合來執行此設定的步驟。 不過，此功能本身只適用于 PowerShell 和 CLI。

## <a name="before-you-begin"></a>開始之前

### <a name="prerequisites"></a>必要條件

若要使用本文中的步驟，您必須在環境中已設定下列設定：

* 您的父訂用帳戶中的虛擬 WAN 和虛擬中樞。
* 在不同 (遠端) 租使用者的訂用帳戶中設定的虛擬網路。
* 請確定遠端租使用者中的 VNet 位址空間不會與任何其他已連線至父虛擬中樞的 Vnet 中的任何其他位址空間重迭。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>指派權限

為了讓具有虛擬中樞的父訂用帳戶修改及存取遠端租使用者中的虛擬網路，您必須從遠端租使用者訂用帳戶將 **參與者** 許可權指派給您的父訂用帳戶。

1. 將 **參與者** 角色指派新增至父帳戶， (具有虛擬 WAN 中樞) 的帳戶。 您可以使用 PowerShell 或 Azure 入口網站指派此角色。 如需相關步驟，請參閱下列 **新增或移除角色指派** 文章：

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [入口網站](../role-based-access-control/role-assignments-portal.md)

1. 接下來，將遠端租使用者訂用帳戶和父租使用者訂用帳戶新增至目前的 PowerShell 會話。 執行下列命令。 如果您已登入父系，則只需要執行遠端租使用者的命令。

   ```azurepowershell-interactive
   Add-AzAccount "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. 使用父認證登入 Azure PowerShell，並執行下列命令，以確認角色指派是否成功：

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 如果許可權已成功傳播至父系，並已新增至會話，則遠端租使用者所擁有的訂用帳戶會顯示在命令的輸出中。

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>將 VNet 連線至中樞

在下列步驟中，您會在將虛擬網路連結至虛擬中樞時，于兩個訂用帳戶的內容之間切換。 取代範例值以反映您自己的環境。

1. 藉由執行下列命令，確定您是在遠端帳戶的內容中：

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. 建立本機變數，以儲存您要連線至中樞之虛擬網路的中繼資料。

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. 切換回父帳戶。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. 將 VNet 連線到中樞。

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. 您可以在 PowerShell 或 Azure 入口網站中查看新的連接。

   * **PowerShell：** 如果成功形成連接，則新格式的連接中的中繼資料會顯示在 PowerShell 主控台中。
   * **Azure 入口網站：** 流覽至虛擬中樞、連線 **> 的虛擬網路連接**。 您可以查看連接的指標。 若要查看實際的資源，您將需要適當的許可權。
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>疑難排解

* 確認之前 [區段](#connect) 中 $remote (的中繼資料) 符合 Azure 入口網站的資訊。
* 您可以使用遠端租使用者資源群組的 IAM 設定，或使用 Azure PowerShell 命令 (>select-azsubscription) 來確認許可權。
* 請確定資源群組的名稱或任何其他環境特定變數的名稱周圍都包含引號 (例如。 "VirtualHub1" 或 "VirtualNetwork1" ) 。

## <a name="next-steps"></a>後續步驟

如需有關虛擬 WAN 的詳細資訊，請參閱：

* 虛擬 WAN [常見問題](virtual-wan-faq.md)
