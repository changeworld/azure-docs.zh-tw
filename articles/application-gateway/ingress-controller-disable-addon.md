---
title: 停用並重新啟用 Azure Kubernetes Service 叢集的應用程式閘道輸入控制器附加元件
description: 本文提供如何為您的 AKS 叢集停用及重新啟用 AGIC 附加元件的相關資訊
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807946"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>停用並重新啟用 AKS 叢集的 AGIC 附加元件
部署為 AKS 附加元件應用程式閘道輸入控制器（AGIC），可讓您使用 Azure CLI 中的一行來啟用及停用附加元件。 當您停用 AGIC 附加元件時，應用程式閘道的生命週期會有所不同，視 AGIC 附加元件是否建立應用程式閘道而定，或與 AGIC 附加元件分開部署而定。 如果您停用 AGIC 附加元件，或是使用現有的 AKS 叢集和應用程式閘道啟用 AGIC 附加元件，則可以執行相同的命令來重新啟用它。

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>使用相關聯的應用程式閘道停用 AGIC 附加元件 
當您第一次設定所有專案時，如果 AGIC 附加元件自動為您部署應用程式閘道，則停用 AGIC 附加元件預設會根據幾個準則刪除該應用程式閘道。 AGIC 附加元件會尋找兩個準則，以判斷它是否應該在您停用時刪除相關聯的應用程式閘道：
- AGIC 附加元件是否與 MC_ * 節點資源群組中部署的應用程式閘道相關聯？ 
- AGIC 附加元件所關聯的應用程式閘道是否具有「建立者：輸入-為 appgw-rg」標記？ AGIC 會使用此標記來判斷附加元件是否已部署應用程式閘道。 

如果同時符合這兩個準則，AGIC 附加元件將會刪除其在停用附加元件時所建立的應用程式閘道;不過，它不會刪除使用/在其中部署應用程式閘道的公用 IP 或子網。 如果不符合第一個條件，則應用程式閘道是否有「建立者：輸入-為 appgw-rg」標記，停用附加元件將不會刪除應用程式閘道。 同樣地，如果不符合第二個準則（也就是應用程式閘道缺少該標記），則停用附加元件將不會刪除 MC_ * 節點資源群組中的應用程式閘道。 

> [!TIP] 
> 如果您不想要在停用附加元件時刪除應用程式閘道，但同時符合這兩個條件，則請移除「建立者：輸入-為 appgw-rg」標記，以防止附加元件刪除您的應用程式閘道。 

若要停用 AGIC 附加元件，請執行下列命令： 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>在現有的應用程式閘道和 AKS 叢集上啟用 AGIC 附加元件
如果您停用 AGIC 附加元件，而需要重新啟用附加元件，或想要使用現有的應用程式閘道和 AKS 叢集啟用附加元件，請執行下列命令：

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>後續步驟
如需如何使用現有的應用程式閘道和 AKS 叢集來啟用 AGIC 附加元件的詳細資訊，請參閱[AGIC 附加元件 brownfield 部署](tutorial-ingress-controller-add-on-existing.md)。