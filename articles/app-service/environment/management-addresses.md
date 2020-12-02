---
title: 管理位址
description: 找出用於控制 App Service 環境的管理位址。 在路由表中設定這些位址，以避免非對稱式路由問題。
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 11/20/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 80d71293467582de5d62176075619e78d9fd5b5f
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511826"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment 管理位址

App Service 環境 (ASE) 是在 Azure 虛擬網路 (VNet) 內執行的 Azure App Service 部署。  當 ASE 確實在 VNet 中執行時，必須仍然可從 Azure App Service 用以管理服務的一些專用私人 IP 位址來存取 ASE。  使用 ASE 時，管理流量會周遊使用者控制的網路。 如果封鎖或誤傳此流量，ASE 會暫止。 如需 ASE 網路服務相依性的詳細資料，請閱讀[網路考量和 App Service 環境][networking]。 如需 ASE 的一般資訊，您可從 [App Service 環境簡介][intro]開始。

所有 ASE 都有可讓管理流量進入的公用 VIP。 這些位址的連入管理流量會進入 ASE 公用 VIP 上的連接埠 454 和 455。 本文件列出流向 ASE 之管理流量的 App Service 來源位址。 這些位址位也會列在名為 AppServiceManagement 的 IP 服務標籤中。

您可在路由表中設定以下標記的位址，以避免管理流量發生非對稱式路由問題。 路由會作用於 IP 層級的流量，但不會察覺流量方向，或流量是否為 TCP 回復訊息的一部分。 如果回復 TCP 要求的位址不同於送往位址，即表示發生了非對稱式路由問題。 為避免 ASE 管理流量發生非對稱式路由問題，則必須確保回復是從送往的相同位址送回。 如需深入了解如何設定 ASE，以便在由內部部署軟體傳送輸出流量的環境中運作，請參閱[為 ASE 設定強制通道][forcedtunnel]

## <a name="list-of-management-addresses"></a>管理位址的清單 ##

| 區域 | 位址 |
|--------|-----------|
| 所有公用區域 | 13.66.140.0、13.67.8.128、13.69.64.128、13.69.227.128、13.70.73.128、13.71.170.64、13.71.194.129、13.75.34.192、13.75.127.117、13.77.50.128、13.78.109.0、13.89.171.0、13.94.141.115、13.94.143.126、13.94.149.179、20.36.106.128、20.36.114.64、23.102.135.246、23.102.188.65、40.69.106.128、40.70.146.128、40.71.13.64、40.74.100.64、40.78.194.128、40.79.130.64、40.79.178.128、40.83.120.64、40.83.121.56、40.83.125.161、40.112.242.192、51.140.146.64、51.140.210.128、52.151.25.45、52.162.106.192、52.165.152.214、52.165.153.122、52.165.154.193、52.165.158.140、52.174.22.21、52.178.177.147、52.178.184.149、52.178.190.65、52.178.195.197、52.187.56.50、52.187.59.251、52.187.63.19、52.187.63.37、52.224.105.172、52.225.177.153、52.231.18.64、52.231.146.128、65.52.172.237、70.37.57.58、104.44.129.141、104.44.129.243、104.44.129.255、104.44.134.255104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.203.64, 191.236.154.88, 52.181.183.11 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |

## <a name="configuring-a-network-security-group"></a>設定網路安全性群組

使用網路安全性群組，您就不必擔心個別地址或維護自己的設定。 名為 AppServiceManagement 的 IP 服務標籤，隨時掌握所有位址的最新狀態。 若要在 NSG 中使用 IP 服務標籤，請移至入口網站，開啟網路安全性群組 UI，並選取輸入安全性規則。 如果具有輸入管理流量的預先存在規則，請加以編輯。 如果未使用 ASE 建立此 NSG，或者該 NSG 是全新的，則選取 [新增]。 在來源下拉式清單中，選取 [服務標籤]。  在 [來源] 服務標籤下，選取 [AppServiceManagement]。 將來源連接埠範圍設為 \*、目的地設為 **Any**、目的地連接埠範圍設為 **454-455**、通訊協定設為 **TCP**，以及將動作設為 **Allow**。 如果您正在制定規則，則必須設定優先順序。 

![使用服務標籤建立 NSG][1]

## <a name="configuring-a-route-table"></a>設定路由表

管理位址可以放在具有網際網路下一個躍點的路由表中，以確保所有輸入管理流量可以通過相同的路徑返回。 設定強制通道時，會需要這些路由。 若要建立路由表，您可以使用入口網站、PowerShell 或 Azure CLI。  從 PowerShell 提示中使用 Azure CLI 建立路由表的命令如下。 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.203.64", "191.236.154.88", "52.181.183.11"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

建立路由表之後，您需要在 ASE 子網路上進行設定。  

## <a name="get-your-management-addresses-from-api"></a>從 API 取得管理位址 ##

您可以使用下列 API 呼叫來列出符合您 ASE 的管理位址。

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

此 API 會傳回一份 JSON 文件，其中包含適用於 ASE 的所有連入位址。 位址清單會包含管理位址、您 ASE 所使用的 VIP，以及 ASE 子網路位址範圍本身。  

若要使用 [armclient](https://github.com/projectkudu/ARMClient) 呼叫 API，請使用下列命令，但必須以您的值取代訂用帳戶識別碼、資源群組與 ASE 名稱。  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
