---
title: 縮放 Azure 信號R 服務的實例
description: 瞭解如何縮放 Azure SignalR 服務實例以通過 Azure 門戶或 Azure CLI 添加或減少容量。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659282"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>如何縮放 Azure SignalR 服務實例？
本文介紹如何縮放 Azure SignalR 服務的實例。 有兩種用於擴展、放大和橫向擴展的方案。

* [放大](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：獲取更多單位、連接、消息等。 您可以通過將定價層從免費更改為"標準"來擴展規模。
* [橫向擴展](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加信號器單位的數量。 您可以擴展到多達 100 個單位。

縮放設置需要幾分鐘才能應用。 在極少數情況下，可能需要大約 30 分鐘才能申請。 它們不要求您更改代碼或重新部署伺服器應用程式。

有關各個 SignalR 服務的定價和容量的資訊，請參閱[Azure SignalR 服務定價詳細資訊](https://azure.microsoft.com/pricing/details/signalr-service/)。  

> [!NOTE]
> 將 SignalR 服務從**免費**層更改為**標準**層，反之亦然，公共服務 IP 將更改，並且通常需要 30-60 分鐘才能在整個 Internet 上向 DNS 伺服器傳播更改。 在更新 DNS 之前，服務可能無法到達。 通常，不建議過於頻繁地更改定價層。


## <a name="scale-on-azure-portal"></a>在 Azure 門戶上縮放

1. 在瀏覽器中，打開[Azure 門戶](https://portal.azure.com)。

2. 在 SignalR 服務頁面中，從左側功能表中選擇 **"縮放**"。
   
3. 選擇定價層，然後按一下 **"選擇**"。 設置**標準**層的單位元數目。
   
    ![在門戶上擴展](./media/signalr-howto-scale/signalr-howto-scale.png)

4. 按一下 [儲存]****。

## <a name="scale-using-azure-cli"></a>使用 Azure CLI 進行調整

此腳本創建**免費**層的新 SignalR 服務資源和一個新的資源組，並將其擴展到**標準**層。 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

記下新資源群組所產生的實際名稱。 當您想要刪除所有群組資源時，就會用到該資源群組名稱。

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>比較定價層

有關詳細資訊（如每個定價層包含的消息和連接），請參閱[SignalR 服務定價詳細資訊](https://azure.microsoft.com/pricing/details/signalr-service/)。

有關每個層中的服務限制、配額和約束表，請參閱[SignalR 服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)。

## <a name="next-steps"></a>後續步驟

在本指南中，您瞭解了如何縮放單個 SignalR 服務實例。

縮放、分片和跨區域方案還支援多個終結點。

> [!div class="nextstepaction"]
> [具有多個實例的縮放信號R服務](./signalr-howto-scale-multi-instances.md)
