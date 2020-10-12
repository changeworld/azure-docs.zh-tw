---
title: 停用並重新啟用 Azure Kubernetes Service 叢集的應用程式閘道輸入控制器附加元件
description: 本文提供有關如何針對您的 AKS 叢集停用和重新啟用 AGIC 附加元件的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807946"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>停用並重新啟用 AKS 叢集的 AGIC 附加元件
應用程式閘道輸入控制器 (AGIC) 部署為 AKS 附加元件，可讓您在 Azure CLI 中以一行啟用和停用附加元件。 當您停用 AGIC 附加元件時，應用程式閘道的生命週期會有所不同，這取決於應用程式閘道是由 AGIC 附加元件所建立，還是與 AGIC 附加元件分開部署。 如果您停用 AGIC 附加元件，您可以執行相同的命令來重新啟用它，或是使用現有的 AKS 叢集和應用程式閘道來啟用 AGIC 附加元件。

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>使用相關聯的應用程式閘道停用 AGIC 附加元件 
如果 AGIC 附加元件在您第一次設定所有專案時，自動為您部署應用程式閘道，則停用 AGIC 附加元件預設會根據幾個準則來刪除應用程式閘道。 AGIC 附加元件會尋找兩個準則，以判斷在您停用時是否應該刪除相關聯的應用程式閘道：
- AGIC 附加元件是否與 MC_ * node 資源群組中部署的應用程式閘道相關聯？ 
- AGIC 附加元件相關聯的應用程式閘道是否有標記 "appgw"？ AGIC 會使用此標記來判斷附加元件是否已部署應用程式閘道。 

如果同時符合這兩個準則，則 AGIC 附加元件將會刪除已停用附加元件時所建立的應用程式閘道;不過，它不會刪除公用 IP 或應用程式閘道部署所在的子網。 如果不符合第一個準則，則不會影響應用程式閘道是否有「建立者：輸入-appgw」標記-停用附加元件不會刪除應用程式閘道。 同樣地，如果不符合第二個準則（亦即，應用程式閘道缺少該標記），則停用附加元件不會刪除 MC_ * node 資源群組中的應用程式閘道。 

> [!TIP] 
> 如果您不想要在停用附加元件時刪除應用程式閘道，但它同時符合這兩個準則，則請移除 "appgw" 標籤，以防止附加元件刪除您的應用程式閘道。 

若要停用 AGIC 附加元件，請執行下列命令： 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>在現有的應用程式閘道和 AKS 叢集上啟用 AGIC 附加元件
如果您要停用 AGIC 附加元件，且需要重新啟用附加元件，或想要使用現有的應用程式閘道和 AKS 叢集來啟用附加元件，請執行下列命令：

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>接下來的步驟
如需如何使用現有的應用程式閘道和 AKS 叢集來啟用 AGIC 附加元件的詳細資訊，請參閱 [AGIC 附加元件棕色地帶部署](tutorial-ingress-controller-add-on-existing.md)。