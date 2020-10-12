---
title: 使用傳統部署模型建立自訂探查-Azure 應用程式閘道
description: 了解如何在傳統部署模型中使用 PowerShell 建立應用程式閘道的自訂探查
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 1445d1418bde6d5d15e365c59ceb56e7661faccb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088065"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>使用 PowerShell 建立 Azure 應用程式閘道 (傳統) 的自訂探查

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)

在本文中，您會使用 PowerShell 將自訂探查新增到現有的應用程式閘道。 對於具有特定健康狀態檢查頁面的應用程式，或是在預設 Web 應用程式上不提供成功回應的應用程式，自訂探查非常實用。

> [!IMPORTANT]
> Azure 針對建立和使用資源方面，有二種不同的部署模型：[Resource Manager 和傳統](../azure-resource-manager/management/deployment-models.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用 Resource Manager 模式。  了解如何[使用 Resource Manager 模型執行這些步驟](application-gateway-create-probe-ps.md)。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>建立應用程式閘道

建立應用程式閘道：

1. 建立應用程式閘道資源。
2. 建立設定 XML 檔案或設定物件。
3. 認可新建立應用程式閘道資源的設定。

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>使用自訂探查建立應用程式閘道資源

若要建立閘道，請使用 `New-AzureApplicationGateway` Cmdlet，並以您自己的值來取代這些值。 此時還不會開始對閘道計費。 會在稍後的步驟中於成功啟動閘道之後開始計費。

下列範例會使用名為 "testvnet1" 的虛擬網路和名為 "subnet-1" 的子網路來建立應用程式閘道。

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

若要驗證已建立閘道，您可以使用 `Get-AzureApplicationGateway` Cmdlet。

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> *InstanceCount*的預設值為2，最大值為10。 *GatewaySize* 的預設值是 Medium。 您可以選擇 Small、Medium 和 Large。
> 
> 

*>virtualips* 和 *DnsName* 會顯示為空白，因為閘道尚未啟動。 閘道處於執行中狀態之後，就會建立這些值。

### <a name="configure-an-application-gateway-by-using-xml"></a>使用 XML 設定應用程式閘道

在下列範例中，您將使用 XML 檔案來設定所有應用程式閘道設定，並將它們認可到應用程式閘道資源。  

將下列文字複製到 [記事本]。

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

編輯設定項目括號間的值。 以 .xml 副檔名儲存檔案。

下列範例示範如何使用設定檔來設定應用程式閘道，使公用連接埠 80 上的 HTTP 流量達到負載平衡，並使用自訂探查將網路流量傳送到兩個 IP 位址之間的後端連接埠 80。

> [!IMPORTANT]
> 通訊協定項目 Http 或 Https 會區分大小寫。

新增了新的 \<Probe\> 設定專案來設定自訂探查。

組態參數如下：

|參數|說明|
|---|---|
|**名稱** |自訂探查的參考名稱。 |
| **通訊協定** | 使用的通訊協定 (可能的值是 HTTP 或 HTTPS)。|
| **Host** 和 **Path** | 應用程式閘道所叫用的完整 URL 路徑，可藉以判斷執行個體健康狀態。 例如，如果您有網站 HTTP： \/ /contoso.com/，則可以為 "HTTP：/contoso.com/path/custompath.htm" 設定自訂探查，以便 \/ 讓探查檢查有成功的 HTTP 回應。|
| **間隔** | 以秒為單位設定探查間隔檢查。|
| **逾時** | 定義 HTTP 回應檢查的探查逾時。|
| **UnhealthyThreshold** | 要將後端執行個體標記為「狀況不良」** 所需的失敗 HTTP 回應次數。|

\<BackendHttpSettings\> 組態中會參考探查名稱，以指派哪個後端集區會使用自訂探查設定。

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>將自訂探查新增至現有應用程式閘道

變更目前的應用程式閘道組態需要三個步驟：取得目前的 XML 組態檔、進行修改使其具有自訂探查，並以新的 XML 設定來設定應用程式閘道。

1. 使用 `Get-AzureApplicationGatewayConfig` 取得 XML 檔案。 此 cmdlet 會匯出要修改的組態 XML 以新增探查設定。

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. 在文字編輯器中開啟 XML 檔案。 在 `<frontendport>` 之後新增 `<probe>` 區段。

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   在 XML 的 backendHttpSettings 區段中，如下列範例所示，新增探查名稱：

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   儲存 XML 檔案。

1. 使用 `Set-AzureApplicationGatewayConfig` 以新的 XML 檔案更新應用程式閘道組態。 此 cmdlet 會以新組態更新您的應用程式閘道。

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>接下來的步驟

如果您想要設定傳輸層安全性 (TLS) ，之前稱為安全通訊端層 (SSL) 卸載，請參閱 [設定應用程式閘道以進行 tls](application-gateway-ssl.md)卸載。

如果您想要設定要與內部負載平衡器搭配使用的應用程式閘道，請參閱 [建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

