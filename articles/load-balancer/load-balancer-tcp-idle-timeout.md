---
title: 設定負載平衡器 TCP 重設和閒置超時
titleSuffix: Azure Load Balancer
description: 在本文中，您將瞭解如何設定 Azure Load Balancer TCP 閒置超時和重設。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747228"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>設定 Azure Load Balancer 的 TCP 重設和閒置超時

Azure Load Balancer 具有下列閒置超時範圍：

* 輸出規則為4分鐘到100分鐘
* 針對 Load Balancer 規則和輸入 NAT 規則，4分鐘到30分鐘

依預設，它會設定為4分鐘。 如果閒置期間超過 timeout 值，就不保證會在用戶端與您的服務之間維護 TCP 或 HTTP 會話。 

下列各節說明如何變更負載平衡器資源的閒置 timeout 和 tcp 重設設定。

## <a name="set-tcp-reset-and-idle-timeout"></a>設定 tcp 重設和閒置超時
---
# <a name="portal"></a>[**入口網站**](#tab/tcp-reset-idle-portal)

若要設定負載平衡器的閒置 timeout 和 tcp 重設，請編輯負載平衡規則。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側功能表中，選取 [ **資源群組** ]。

3. 選取負載平衡器的資源群組。 在此範例中，資源群組的名稱是 **myResourceGroup** 。

4. 選取負載平衡器。 在此範例中，負載平衡器的名稱是 **myLoadBalancer** 。

5. 在 [ **設定** ] 中，選取 [ **負載平衡規則** ]。

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="編輯負載平衡器規則。" border="true":::

6. 選取您的負載平衡規則。 在此範例中，負載平衡規則的名稱是 **mylbrule 變更** 。

7. 在負載平衡規則中，將 **閒置 timeout (分鐘)** 的滑杆移至您的超時值。  

8. 在 [ **TCP 重設** ] 底下，選取 [ **已啟用** ]。

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="編輯負載平衡器規則。" border="true":::

9. 選取 [儲存]。

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

若要設定閒置 timeout 和 tcp 重設，請在下列負載平衡規則參數中使用 [>new-azloadbalancer](/powershell/module/az.network/set-azloadbalancer)設定值：

* **IdleTimeoutInMinutes**
* **Enabletcpreset 參數**

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

以您資源中的值取代下列範例：

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

若要設定閒置 timeout 和 tcp 重設，請針對 [az network lb rule update](/cli/azure/network/lb/rule?az_network_lb_rule_update)使用下列參數：

* **--閒置-超時**
* **--啟用-tcp-重設**

開始之前，請先驗證您的環境：

* 登入 Azure 入口網站，並執行 `az login` 檢查您的訂用帳戶是否有效。
* 執行 `az --version`，在終端機或命令視窗中檢查您的 Azure CLI 版本。 如需最新版本，請參閱[最新版本資訊](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。
  * 如果您沒有最新版本，請遵循[適用於您作業系統或平台的安裝指南](/cli/azure/install-azure-cli)，更新您的安裝。

以您資源中的值取代下列範例：

* **myResourceGroup**
* **myLoadBalancer**
* **Mylbrule 變更**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>後續步驟

如需 tcp 閒置超時和重設的詳細資訊，請參閱 [LOAD BALANCER Tcp 重設和閒置超時](load-balancer-tcp-reset.md)

如需設定負載平衡器分配模式的詳細資訊，請參閱 [設定負載平衡器分配模式](load-balancer-distribution-mode.md)。
