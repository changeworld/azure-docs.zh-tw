---
title: 設定 Azure Load Balancer 分配模式
titleSuffix: Azure Load Balancer
description: 在本文中，開始配置 Azure 負載等化器的分發模式以支援源 IP 關聯。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023526"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>設定 Azure Load Balancer 的分配模式

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>雜湊型分配模式

Azure Load Balancer 的預設分散模式是 5 Tuple 雜湊。 

元組由以下部分組成：
* **源 IP**
* **來源連接埠**
* **目的地 IP**
* **目的地連接埠**
* **通訊協定類型**

雜湊用於將流量映射到可用的伺服器。 該演算法僅在傳輸會話中提供粘性。 同一會話中的資料包被定向到負載均衡終結點後面的同一資料中心 IP。 當用戶端從同一源 IP 啟動新會話時，源埠會更改並導致流量轉到其他資料中心終結點。

![基於五元雜湊的分佈模式](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>來源 IP 同質性模式

也可以使用源 IP 關聯分佈模式配置負載等化器。 這個分配模式也稱為工作階段同質性或用戶端 IP 同質性。 該模式使用雙組（源 IP 和目標 IP）或三元組（源 IP、目標 IP 和協定類型）雜湊值將流量映射到可用伺服器。 通過使用源 IP 關聯，從同一用戶端電腦啟動的連接將轉到同一資料中心終結點。

下圖說明瞭雙組配置。 請注意雙組組如何通過負載等化器運行到虛擬機器 1 （VM1）。 然後，VM2 和 VM3 會備份 VM1。

![雙組會話關聯分佈模式](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

來源 IP 同質性模式可解決 Azure Load Balancer 與「遠端桌面閘道」(RD 閘道) 之間的不相容性。 藉由使用此模式，您將可以在單一雲端服務中建置「RD 閘道」伺服器陣列。

另一個使用案例是媒體上傳。 這會透過 UDP 來上傳資料，但會透過 TCP 來存取控制層：

* 用戶端啟動到負載平衡公共位址的 TCP 會話，並定向到特定的 DIP。 通道會保持作用中來監視連線健康情況。
* 從同一用戶端電腦啟動新的 UDP 會話到同一負載均衡的公共終結點。 該連線會被導向到與先前 TCP 連線相同的 DIP 端點。 媒體上傳既能以高輸送量的方式執行，同時又可透過 TCP.維護控制通道。

> [!NOTE]
> 當藉由移除或新增虛擬機器來變更負載平衡集時，系統會重新計算用戶端要求的分配。 您無法確定來自現有用戶端的新連線最後都會抵達相同的伺服器。 此外，使用來源 IP 同質性分配模式可能會導致流量的分配不相等。 在 Proxy 後方執行的用戶端可能會被視為一個獨特的用戶端應用程式。

## <a name="configure-source-ip-affinity-settings"></a>設定來源 IP 同質性設定

### <a name="azure-portal"></a>Azure 入口網站

您可以通過修改門戶中的負載平衡規則來更改分發模式的配置。

1. 登錄到 Azure 門戶，然後通過按一下資源組找到包含要更改的負載等化器**的資源組**。
2. 在負載平衡器概覽螢幕中，按一下 **"設置**"下的**負載平衡規則**。
3. 在負載平衡規則螢幕中，按一下要更改分佈模式的負載平衡規則。
4. 在規則下，通過更改 **"會話持久性**下拉"框來更改分發模式。  有下列選項可供使用：
    
    * **無（基於雜湊）** - 指定來自同一用戶端的連續請求可能由任何虛擬機器處理。
    * **用戶端 IP（源 IP 關聯 2 組）** - 指定來自同一用戶端 IP 位址的連續請求將由同一虛擬機器處理。
    * **用戶端 IP 和協定（源 IP 關聯 3 組）** - 指定來自同一用戶端 IP 位址和協定組合的連續請求將由同一虛擬機器處理。

5. 選擇分發模式，然後按一下"**保存**"。

### <a name="azure-powershell"></a>Azure PowerShell

對於使用資源管理器部署的虛擬機器，請使用 PowerShell 更改現有負載平衡規則上的負載平衡器分配設置。 以下命令更新分發模式： 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

針對傳統的虛擬機器，使用 Azure PowerShell 來變更分配設定。 將 Azure 端點新增到虛擬機器，然後設定負載平衡器分配模式：

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

為所需的負載平衡量`LoadBalancerDistribution`設置元素的值。 為雙元組（源 IP 和目標 IP）負載平衡指定源 IP。 為三元組（源 IP、目標 IP 和協定類型）負載平衡指定源 IP 協定。 為五元負載平衡的預設行為指定無。

使用下列設定來擷取端點負載平衡器分配模式組態：

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

當`LoadBalancerDistribution`元素不存在時，Azure 負載等化器使用預設的五元組演算法。

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

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

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

如前所述，將`LoadBalancerDistribution`元素設置為源IP，用於雙元關聯、源 IP 協定為三元關聯，或無無關聯（五元關聯）。

#### <a name="response"></a>回應

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>後續步驟

* [Azure 內部負載平衡器概觀](load-balancer-internal-overview.md)
* [開始設定網際網路對應負載平衡器](quickstart-create-standard-load-balancer-powershell.md)
* [設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
