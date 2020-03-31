---
title: 使用範本部署 Azure 防火牆
description: 使用範本部署 Azure 防火牆。 創建的網路有一個 VNet，具有三個子網。 部署了兩個雙核 Windows 伺服器虛擬機器。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169204"
---
# <a name="deploy-azure-firewall-using-a-template"></a>使用範本部署 Azure 防火牆

[建立 AzureFirewall 沙箱設定範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)會建立具有防火牆的測試網路環境。 該網路有一個虛擬網路 （VNet） 與三個子網： *Azure 防火牆子網*，*伺服器子網*， 和*跳轉盒子網*. ServersSubnet** 和 JumpboxSubnet** 子網路各有一個雙核心的 Windows Server 虛擬機器。

防火牆位於 Azure*防火牆子網*子網中，並且具有一個應用程式規則集合，該集合具有允許訪問`www.microsoft.com`的單個規則。

使用者定義的路由會將來自 ServersSubnet** 子網路的網路流量指向通過已套用防火牆規則的防火牆。

如需 Azure 防火牆的詳細資訊，請參閱[使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>使用範本來部署 Azure 防火牆

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

**若要使用範本安裝並部署 Azure 防火牆：**

1. 訪問 中的[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)範本。
   
1. 閱讀簡介，當準備好部署時，請選取 [部署至 Azure]****。
   
1. 視需要登入 Azure 入口網站。 

1. 在入口網站的 [建立 AzureFirewall 的沙箱設定]**** 頁面上，輸入或選取下列值：
   
   - **資源組**：選擇 **"創建新**"，鍵入資源組的名稱，然後選擇 **"確定**"。 
   - **虛擬網路名稱**：鍵入新 VNet 的名稱。 
   - **管理員使用者名**：鍵入管理員使用者帳戶的使用者名。
   - **管理員密碼**：鍵入管理員密碼。 
   
1. 閱讀條款及條件，然後選擇**我同意上述條款及條件**。
   
1. 選取 [購買]****。
   
   需要幾分鐘的時間才能建立資源。 
   
1. 探索使用防火牆所建立的資源。 

若要了解範本中防火牆的 JSON 語法和屬性，請參閱 [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)。

## <a name="clean-up-resources"></a>清除資源

當您不再需要它們時，可以通過運行["刪除-AzResourceGroup PowerShell"](/powershell/module/az.resources/remove-azresourcegroup)命令刪除資源組、防火牆和所有相關資源。 若要移除名為 MyResourceGroup** 的資源群組，請執行： 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
如果您打算繼續進行防火牆監視教學課程，請勿移除資源群組和防火牆。 

## <a name="next-steps"></a>後續步驟

接下來，您可以監視 Azure 防火牆記錄：

> [!div class="nextstepaction"]
> [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
