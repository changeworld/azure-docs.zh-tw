---
title: 應用程式閘道與服務端點整合-Azure App Service |Microsoft Docs
description: 描述應用程式閘道如何與服務端點安全的 Azure App Service 整合。
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: fdb502685259a5e12bf0182c36aa6c73ec7c1dd5
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958928"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>應用程式閘道與服務端點的整合
App Service 的三種變化，需要與 Azure 應用程式閘道的整合設定稍有不同。 這些變化包括定期 App Service-也稱為多租使用者、內部 Load Balancer (ILB) App Service 環境 (ASE) 和外部 ASE。 本文將逐步解說如何使用 App Service (多租使用者) 進行設定，以及討論有關 ILB 和外部 ASE 的考慮。

## <a name="integration-with-app-service-multi-tenant"></a>與 App Service (多租使用者) 整合
App Service (多租使用者) 具有公用網際網路面向端點。 您可以使用 [服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md) ，只允許來自 Azure 虛擬網路內特定子網的流量，並封鎖其他所有專案。 在下列案例中，我們將使用這項功能來確保 App Service 實例只能接收來自特定應用程式閘道實例的流量。

![應用程式閘道與 App Service 整合](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

除了建立 App Service 和應用程式閘道之外，此設定有兩個部分。 第一個部分是在部署應用程式閘道之虛擬網路的子網中啟用服務端點。 服務端點可確保將子網離開 App Service 的所有網路流量都會標記特定的子網識別碼。 第二個部分是設定特定 web 應用程式的存取限制，以確保只允許以這個特定子網識別碼標記的流量。 您可以根據喜好設定使用不同的工具來設定它。

## <a name="using-azure-portal"></a>使用 Azure 入口網站
使用 Azure 入口網站時，您可以遵循四個步驟來布建及設定安裝程式。 如果您有現有的資源，可以略過第一個步驟。
1. 使用 App Service 檔中的其中一個快速入門來建立 App Service，例如 [.Net Core 快速入門](../quickstart-dotnetcore.md)
2. 使用 [入口網站快速入門](../../application-gateway/quick-create-portal.md)來建立應用程式閘道，但略過 [新增後端目標] 區段。
3. 將 [App Service 設定為應用程式閘道中的後端](../../application-gateway/configure-web-app-portal.md)，但略過 [限制存取] 區段。
4. 最後， [使用服務端點建立存取限制](../../app-service/app-service-ip-restrictions.md#service-endpoints)。

您現在可以透過應用程式閘道存取 App Service，但如果您嘗試直接存取 App Service，您應該會收到 403 HTTP 錯誤，表示網站已停止。

![應用程式閘道與 App Service 整合](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
[Resource Manager 部署範本][template-app-gateway-app-service-complete]將會提供完整的案例。 此案例是由服務端點鎖定的 App Service 實例和存取限制所組成，只會接收來自應用程式閘道的流量。 此範本包含許多智慧型預設值，以及新增至資源名稱的唯一 postfixes，以簡化。 若要覆寫它們，您必須複製存放庫，或下載範本並加以編輯。 

若要套用範本，您可以使用範本描述中所找到的 [部署至 Azure] 按鈕，或者您可以使用適當的 PowerShell/CLI。

## <a name="using-azure-command-line-interface"></a>使用 Azure 命令列介面
[Azure CLI 範例](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md)會布建服務端點鎖定的 App Service，並使用存取限制只接收來自應用程式閘道的流量。 如果您只需要從現有的應用程式閘道將流量隔離到現有的 App Service，下列命令就已足夠。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

在預設設定中，此命令會確保子網中的服務端點設定和 App Service 中的存取限制都有設定。

## <a name="considerations-for-ilb-ase"></a>ILB ASE 的考慮
ILB ASE 不會公開至網際網路，而且實例與應用程式閘道之間的流量，因此已經隔離到虛擬網路。 下列操作 [指南](../environment/integrate-with-application-gateway.md) 會設定 ILB ASE，並使用 Azure 入口網站將其與應用程式閘道整合。 

如果您想要確保只有來自應用程式閘道子網的流量到達 ASE，您可以設定網路安全性群組 (NSG) ，而這會影響 ASE 中的所有 web 應用程式。 針對 NSG，您可以指定子網 IP 範圍，並選擇性地指定埠 (80/443) 。 請確定您未覆寫 ASE [所需的 NSG 規則](../environment/network-info.md#network-security-groups) ，才能正確運作。

若要將流量隔離到個別的 web 應用程式，您必須使用以 ip 為基礎的存取限制，因為服務端點不適用於 ASE。 IP 位址應為應用程式閘道實例的私人 IP。

## <a name="considerations-for-external-ase"></a>外部 ASE 的考慮
外部 ASE 具有公開的負載平衡器，例如多租使用者 App Service。 服務端點不適用於 ASE，這就是為什麼您必須使用應用程式閘道實例的公用 IP 來使用以 ip 為基礎的存取限制。 若要使用 Azure 入口網站建立外部 ASE，您可以遵循本 [快速入門](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "適用于完整案例的 Azure Resource Manager 範本"

## <a name="considerations-for-kuduscm-site"></a>Kudu/scm 網站的考慮
Scm 網站也稱為 kudu，是每個 web 應用程式都有的管理網站。 您無法反向 proxy scm 網站，而且您很可能會想要將其鎖定到個別的 IP 位址或特定的子網。

如果您想要使用與主要網站相同的存取限制，您可以使用下列命令來繼承設定。

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

如果您想要設定 scm 網站的個別存取限制，您可以使用--scm 網站旗標新增存取限制，如下所示。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>後續步驟
如需 App Service 環境的詳細資訊，請參閱 [App Service 環境檔](/azure/app-service/environment)。

若要進一步保護您的 web 應用程式，您可以在 [Azure Web 應用程式防火牆檔](../../web-application-firewall/ag/ag-overview.md)中找到應用程式閘道上的 web 應用程式防火牆的相關資訊。