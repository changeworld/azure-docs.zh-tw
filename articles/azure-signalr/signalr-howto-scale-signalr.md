---
title: 調整 Azure SignalR Service 的實例
description: 瞭解如何透過 Azure 入口網站或 Azure CLI 來調整 Azure SignalR Service 實例的規模，以增加或減少容量。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75659282"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>如何調整 Azure SignalR Service 實例？
本文說明如何調整 Azure SignalR Service 的實例。 有兩種規模調整、相應增加和相應放大的案例。

* 相應[增加](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：取得更多單位、連線、訊息等等。 您可以將定價層從「免費」變更為「標準」，以相應增加規模。
* [相應](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)放大：增加 SignalR 單位的數目。 您可以相應放大為最多100個單位。

調整規模設定需要幾分鐘的時間才會套用。 在罕見的情況下，可能需要大約30分鐘的時間來套用。 它們不會要求您變更程式碼或重新部署您的伺服器應用程式。

如需個別 SignalR Service 的定價和容量相關資訊，請參閱[Azure SignalR Service 定價詳細資料](https://azure.microsoft.com/pricing/details/signalr-service/)。  

> [!NOTE]
> 將 SignalR Service 從**免費**層變更為**標準**層，或相反地，公用服務 IP 將會變更，而且通常需要30-60 分鐘的時間，才能將變更傳播到整個網際網路上的 DNS 伺服器。 您的服務可能會在 DNS 更新前無法連線。 通常不建議您經常變更定價層。


## <a name="scale-on-azure-portal"></a>相應縮小 Azure 入口網站

1. 在瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com)。

2. 在 [SignalR Service] 頁面上，從左側功能表中選取 [**調整**]。
   
3. 選擇您的定價層，然後按一下 [**選取**]。 設定**標準**層的單位元數目。
   
    ![在入口網站上調整](./media/signalr-howto-scale/signalr-howto-scale.png)

4. 按一下 [檔案] 。

## <a name="scale-using-azure-cli"></a>使用 Azure CLI 進行調整

此腳本會建立**免費**層的新 SignalR Service 資源和新的資源群組，並將其相應增加至**標準**層。 

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

如需詳細資訊（例如每個定價層的內含訊息和連線），請參閱[SignalR Service 定價詳細資料](https://azure.microsoft.com/pricing/details/signalr-service/)。

如需每個層級的服務限制、配額和條件約束表，請參閱[SignalR Service 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)。

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何調整單一 SignalR Service 實例。

縮放、分區化和跨區域案例也支援多個端點。

> [!div class="nextstepaction"]
> [具有多個實例的規模 SignalR Service](./signalr-howto-scale-multi-instances.md)
