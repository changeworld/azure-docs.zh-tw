---
title: 創建 Azure 堡壘主機：門戶
description: 在本文中，瞭解如何使用門戶創建 Azure 堡壘主機
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366138"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>使用門戶創建 Azure 堡壘主機

本文介紹如何使用 Azure 門戶創建 Azure 堡壘主機。 在虛擬網路中預配 Azure Bastion 服務後，同一虛擬網路中的所有 VM 都可以使用無縫的 RDP/SSH 體驗。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

您可以通過手動指定所有設置或使用與現有 VM 對應的設置在門戶中創建新的堡壘主機資源。 要使用 VM 設置創建堡壘主機，請參閱[快速入門](quickstart-host-portal.md)文章。 或者，可以使用 Azure [PowerShell](bastion-create-host-powershell.md)創建 Azure 堡壘主機。

## <a name="before-you-begin"></a>開始之前

堡壘在以下 Azure 公共區域中可用：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

此部分可説明您從 Azure 門戶創建新的 Azure 堡壘資源。

1. 在[Azure 門戶](https://portal.azure.com)功能表或**主頁**上，選擇 **"創建資源**"。

1. 在 **"新建**"頁上，在 *"搜索市場"* 欄位中，鍵入 **"堡壘**"，然後按一下 **"輸入**"以獲取搜尋結果。

1. 從結果中，按一下 **"堡壘**"。 確定發行者為 *Microsoft*，且類別 [網路]**。

1. 在 **"堡壘**"頁上，按一下"**創建**"以打開"**創建堡壘**"頁。

1. 在"**創建堡壘**"頁上，配置新的堡壘資源。 指定堡壘資源的配置設置。

    ![創建堡壘](./media/bastion-create-host-portal/settings.png)

    * **訂閱**：要用於創建新堡壘資源的 Azure 訂閱。
    * **資源組**：將在其中創建新堡壘資源的 Azure 資源組。 如果沒有現有資源組，則可以創建新的資源組。
    * **名稱**： 新堡壘資源的名稱
    * **區域**：將在 其中創建資源的 Azure 公共區域。
    * **虛擬網路**：將在其中創建堡壘資源的虛擬網路。 在此過程中，您可以在門戶中創建新的虛擬網路，或使用現有的虛擬網路。 如果使用現有虛擬網路，請確保現有虛擬網路有足夠的可用位址空間來滿足堡壘子網要求。
    * **子網**：將部署新堡壘主機資源的虛擬網路中的子網。 您必須使用名稱值**AzureBastion Subnet**創建子網。 此值使 Azure 知道要將堡壘資源部署到哪個子網。 這與閘道子網不同。 您必須使用至少 /27 或更大的子網（/27、/26 等）。
    
       創建**AzureBastion Subnet，** 沒有任何路由表或授權。 如果在**AzureBastionSubnet**上使用網路安全性群組，請參閱[使用 NSG](bastion-nsg.md)的文章。
    * **公共 IP 位址**：將訪問 RDP/SSH 的堡壘資源的公共 IP（通過埠 443）。 創建新的公共 IP 或使用現有 IP。 公共 IP 位址必須與您正在創建的堡壘資源位於同一區域。
    * **公共 IP 位址名稱**：公共 IP 位址資源的名稱。
    * **公共 IP 位址 SKU**：此設置在預設情況下已預填充到**標準**。 Azure 堡壘僅使用/支援標準公共 IP SKU。
    * **賦值**：此設置在預設情況下預填充為 **"靜態**"。

1. 完成指定設置後，按一下"審閱 **+ 創建**"。 這將驗證值。 驗證完成後，可以開始創建過程。
1. 在"**創建堡壘**"頁上，按一下"**創建**"。
1. 您將看到一條消息，通知您部署正在進行。 創建資源時，狀態將顯示在此頁面上。 創建和部署堡壘資源大約需要 5 分鐘。

## <a name="next-steps"></a>後續步驟

* 有關詳細資訊，請閱讀[堡壘常見問題解答](bastion-faq.md)。

* 要將網路安全性群組與 Azure 堡壘子網一起使用，請參閱[使用 NSG。](bastion-nsg.md)