---
title: 設定 Azure Load Balancer 分配模式
titleSuffix: Azure Load Balancer
description: 在本文中，開始設定 Azure Load Balancer 的分配模式，以支援來源 IP 親和性。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: aa2d2cda59f23ef0b961ccac60b119996423688e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682310"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>設定 Azure Load Balancer 的分配模式

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>雜湊型分配模式

Azure Load Balancer 的預設分散模式是 5 Tuple 雜湊。 

元組是由下列各項所組成：
* **來源 IP**
* **來源連接埠**
* **目的地 IP**
* **目的地連接埠**
* **通訊協定類型**

雜湊用來將流量對應至可用的伺服器。 演算法只會在傳輸會話內提供輸出。 相同會話中的封包會被導向至負載平衡端點後方的相同資料中心 IP。 當用戶端從相同的來源 IP 啟動新的會話時，來源埠會變更，並導致流量移至不同的資料中心端點。

![五個元組雜湊式分配模式](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>來源 IP 同質性模式

您也可以使用「來源 IP 親和性」分配模式來設定負載平衡器。 這個分配模式也稱為工作階段同質性或用戶端 IP 同質性。 此模式會使用兩個元組 (來源 IP 和目的地 IP) 或三個元組 (來源 IP、目的地 IP 及通訊協定類型) 雜湊，以將流量對應至可用的伺服器。 藉由使用來源 IP 親和性，從相同用戶端電腦啟動的連線會移至相同的資料中心端點。

下圖說明兩個元組的設定。 請注意兩個元組如何透過負載平衡器執行至虛擬機器 1 (VM1) 。 然後，VM2 和 VM3 會備份 VM1。

![雙元組會話親和性分配模式](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

來源 IP 同質性模式可解決 Azure Load Balancer 與「遠端桌面閘道」(RD 閘道) 之間的不相容性。 藉由使用此模式，您將可以在單一雲端服務中建置「RD 閘道」伺服器陣列。

另一個使用案例是媒體上傳。 這會透過 UDP 來上傳資料，但會透過 TCP 來存取控制層：

* 用戶端會對負載平衡的公用位址啟動 TCP 會話，並導向至特定 DIP。 通道會保持作用中來監視連線健康情況。
* 來自相同用戶端電腦的新 UDP 會話會啟動至相同的負載平衡公用端點。 該連線會被導向到與先前 TCP 連線相同的 DIP 端點。 媒體上傳既能以高輸送量的方式執行，同時又可透過 TCP.維護控制通道。

> [!NOTE]
> 當藉由移除或新增虛擬機器來變更負載平衡集時，系統會重新計算用戶端要求的分配。 您無法確定來自現有用戶端的新連線最後都會抵達相同的伺服器。 此外，使用來源 IP 同質性分配模式可能會導致流量的分配不相等。 在 Proxy 後方執行的用戶端可能會被視為一個獨特的用戶端應用程式。

## <a name="configure-source-ip-affinity-settings"></a>設定來源 IP 同質性設定

### <a name="azure-portal"></a>Azure 入口網站

您可以藉由在入口網站中修改負載平衡規則來變更分配模式的設定。

1. 登入 Azure 入口網站，並按一下 **資源群組**，找出包含您想要變更之負載平衡器的資源群組。
2. 在 [負載平衡器] 總覽畫面中，按一下 [**設定**] 下的 [**負載平衡規則**]。
3. 在 [負載平衡規則] 畫面中，按一下您想要變更分配模式的負載平衡規則。
4. 在此規則下，藉由變更 [ **會話持續** 性] 下拉式方塊來變更分佈模式。  可用選項如下：
    
    * **無 (雜湊式)** -指定來自相同用戶端的後續要求可以由任何虛擬機器處理。
    * **用戶端 IP (來源 ip 親和性 2-元組)** -指定來自相同用戶端 ip 位址的後續要求會由相同的虛擬機器處理。
    * **用戶端 IP 和通訊協定 (來源 ip 親和性 3-元組)** -指定來自相同用戶端 IP 位址和通訊協定組合的後續要求會由相同的虛擬機器處理。

5. 選擇分配模式，然後按一下 [ **儲存**]。

### <a name="azure-powershell"></a>Azure PowerShell

針對使用 Resource Manager 部署的虛擬機器，請使用 PowerShell 來變更現有負載平衡規則的負載平衡器發佈設定。 下列命令會更新分配模式： 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

針對傳統的虛擬機器，使用 Azure PowerShell 來變更分配設定。 將 Azure 端點新增到虛擬機器，然後設定負載平衡器分配模式：

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

`LoadBalancerDistribution`針對所需的負載平衡量設定元素的值。 針對兩個元組 (來源 IP 和目的地 IP) 負載平衡指定 sourceIP。 針對三個元組 (來源 IP、目的地 IP 及通訊協定類型指定 sourceIPProtocol，) 負載平衡。 針對五個元組負載平衡的預設行為指定 [無]。

使用下列設定來擷取端點負載平衡器分配模式組態：

```azurepowershell
PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
LBSetName : MyLoadBalancedSet
LocalPort : 80
Name : HTTP
Port : 80
Protocol : tcp
Vip : 65.52.xxx.xxx
ProbePath :
ProbePort : 80
ProbeProtocol : tcp
ProbeIntervalInSeconds : 15
ProbeTimeoutInSeconds : 31
EnableDirectServerReturn : False
Acl : {}
InternalLoadBalancerName :
IdleTimeoutInMinutes : 15
LoadBalancerDistribution : sourceIP
```

當 `LoadBalancerDistribution` 元素不存在時，Azure Load Balancer 會使用預設的五元組演算法。

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>在負載平衡端點集上設定分配模式

當端點是負載平衡端點集的一部分時，必須在負載平衡端點集上設定分配模式：

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>設定雲端服務端點的分配模式

使用「適用於 .NET 2.5 的 Azure SDK」來更新您的雲端服務。 「雲端服務」的端點設定是在 .csdef 檔案中進行的。 若要更新「雲端服務」部署的負載平衡器分配模式，必須進行部署升級。

適用於端點設定的 .csdef 變更範例如下：

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API 範例

下列範例說明如何針對某個部署中的指定負載平衡集，重新設定負載平衡器分配模式。 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>變更已部署之負載平衡集的分配模式

使用 Azure 傳統部署模型來變更現有的部署組態。 新增 `x-ms-version` 標頭，然後將值設定成 2014-09-01 版或更新版本。

#### <a name="request"></a>要求

```http
POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
Content-Type: application/xml
```

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
            <Port> port-assigned-to-probe </Port>
            <Protocol> probe-protocol </Protocol>
            <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
            <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

如先前所述，將 `LoadBalancerDistribution` sourceIP 兩個元組親和性的元素，設定為三元組親和性的 sourceIPProtocol，或無親和性 (五個元組親和性) 。

#### <a name="response"></a>回應

```http
HTTP/1.1 202 Accepted
Cache-Control: no-cache
Content-Length: 0
Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
x-ms-servedbyregion: ussouth2
x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
Date: Thu, 16 Oct 2014 22:49:21 GMT
```

## <a name="next-steps"></a>後續步驟

* [Azure 負載平衡器概觀](load-balancer-overview.md)
* [開始設定網際網路對應負載平衡器](quickstart-load-balancer-standard-public-powershell.md)
* [設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
