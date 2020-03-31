---
title: 應用程式閘道與服務終結點集成 - Azure 應用服務 |微軟文檔
description: 描述應用程式閘道如何與 Azure 應用服務集成，保護與服務終結點一起保護。
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
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980058"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>應用程式閘道與服務終結點集成
應用服務有三種變體，它們需要與 Azure 應用程式閘道的集成配置略有不同。 這些變體包括常規應用服務 - 也稱為多租戶、內部負載等化器 （ILB） 應用服務環境 （ASE） 和外部 ASE。 本文將介紹如何使用應用服務（多租戶）配置它，並討論有關 ILB 和外部 ASE 的注意事項。

## <a name="integration-with-app-service-multi-tenant"></a>與應用服務（多租戶）集成
應用服務（多租戶）具有面向公共互聯網的終結點。 使用[服務終結點](../../virtual-network/virtual-network-service-endpoints-overview.md)，只能允許來自 Azure 虛擬網路中的特定子網的流量，並阻止其他所有內容。 在以下方案中，我們將使用此功能來確保應用服務實例只能接收來自特定應用程式閘道實例的流量。

![應用程式閘道與應用服務集成](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

除了創建應用服務和應用程式閘道之外，此配置還有兩個部分。 第一部分是在部署應用程式閘道的虛擬網路子網中啟用服務終結點。 服務終結點將確保將子網留給應用服務的所有網路流量都將使用特定的子網 ID 進行標記。 第二部分是設置特定 Web 應用的訪問限制，以確保僅允許使用此特定子網 ID 標記的流量。 您可以根據首選項使用不同的工具對其進行配置。

## <a name="using-azure-portal"></a>使用 Azure 入口網站
使用 Azure 門戶，請按照四個步驟來預配和配置設置。 如果您有現有資源，則可以跳過第一步。
1. 使用應用服務文件中的"快速入門"之一創建應用服務，例如[.Net Core 快速入門](../../app-service/app-service-web-get-started-dotnet.md)
2. 使用[門戶快速啟動](../../application-gateway/quick-create-portal.md)創建應用程式閘道，但跳過"添加後端目標"部分。
3. 將[應用服務配置為應用程式閘道中的後端](../../application-gateway/configure-web-app-portal.md)，但跳過"限制訪問"部分。
4. 最後[使用服務終結點創建訪問限制](../../app-service/app-service-ip-restrictions.md#service-endpoints)。

您現在可以通過應用程式閘道訪問應用服務，但如果您嘗試直接存取應用服務，應收到 403 HTTP 錯誤，指示網站已停止。

![應用程式閘道與應用服務集成](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
[資源管理器部署範本][template-app-gateway-app-service-complete]將預配完整的方案。 該方案包括一個應用服務實例，該實例與服務終結點一起鎖定，訪問限制僅接收來自應用程式閘道的流量。 該範本包括許多智慧預設值和添加到資源名稱中的唯一修復，使其簡單。 要覆蓋它們，您必須克隆回購或下載範本並對其進行編輯。 

要應用範本，可以使用範本說明中找到的"部署到 Azure"按鈕，也可以使用適當的 PowerShell/CLI。

## <a name="using-azure-command-line-interface"></a>使用 Azure 命令列介面
[Azure CLI 示例](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md)將預配使用服務終結點鎖定的應用服務，並限制訪問限制，以便僅接收來自應用程式閘道的流量。 如果只需要將現有應用服務的流量與現有應用程式閘道隔離開來，則以下命令就足夠了。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

在預設配置中，該命令將確保子網中的服務終結點配置設置和應用服務中的訪問限制。

## <a name="considerations-for-ilb-ase"></a>ILB ASE 的注意事項
ILB ASE 不會暴露給互聯網，因此實例和應用程式閘道之間的流量已隔離到虛擬網路。 以下[操作指南](../environment/integrate-with-application-gateway.md)配置 ILB ASE，並使用 Azure 門戶將其與應用程式閘道集成。 

如果要確保僅從應用程式閘道子網的流量到達 ASE，則可以配置影響 ASE 中的所有 Web 應用的網路安全性群組 （NSG）。 對於 NSG，您可以指定子網 IP 範圍，並選擇埠 （80/443）。 確保不要覆蓋 ASE 正常運行[所需的 NSG 規則](../environment/network-info.md#network-security-groups)。

要隔離單個 Web 應用的流量，您需要使用基於 ip 的訪問限制，因為服務終結點不適用於 ASE。 IP 位址應為應用程式閘道實例的專用 IP。

## <a name="considerations-for-external-ase"></a>外部 ASE 的注意事項
外部 ASE 具有面向公共的負載等化器，如多租戶應用服務。 服務終結點對 ASE 不起作用，因此您必須使用應用程式閘道實例的公共 IP 使用基於 ip 的訪問限制。 要使用 Azure 門戶創建外部 ASE，可以遵循此[快速入門](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "用於完整方案的 Azure 資源管理器範本"

## <a name="considerations-for-kuduscm-site"></a>庫杜/scm 網站的注意事項
scm 網站（也稱為 kudu）是一個管理網站，適用于每個 Web 應用。 無法反向代理 scm 網站，您很可能還希望將其鎖定到單個 IP 位址或特定子網。

如果要使用與主網站相同的訪問限制，可以使用以下命令繼承設置。

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

如果要為 scm 網站設置單獨的訪問限制，可以使用 --scm 網站標誌添加訪問限制，如下所示。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>後續步驟
有關應用服務環境的詳細資訊，請參閱[應用服務環境文檔](https://docs.microsoft.com/azure/app-service/environment)。

為了進一步保護 Web 應用，可以在[Azure Web 應用程式防火牆文檔中](../../web-application-firewall/ag/ag-overview.md)找到有關應用程式閘道上的 Web 應用程式防火牆的資訊。