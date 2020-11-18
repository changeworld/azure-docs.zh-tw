---
title: 'Windows 虛擬桌面 RDP Shortpath (preview) '
titleSuffix: Azure
description: 如何設定適用于 Windows 虛擬桌面的 RDP Shortpath (preview) 。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: aee3f5602d0bd1ff12717d903d662ce4605de61d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683755"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows 虛擬桌面 RDP Shortpath (preview) 

> [!IMPORTANT]
> RDP Shortpath 目前處於公開預覽狀態。
> 此預覽版是在沒有服務等級協定的情況下提供，不建議將它用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

RDP Shortpath 是 Windows 虛擬桌面的一項功能，可在遠端桌面用戶端與會話主機之間建立直接以 UDP 為基礎的傳輸。 RDP 使用此傳輸來提供遠端桌面和 RemoteApp，同時提供更佳的可靠性和一致的延遲。

## <a name="key-benefits"></a>主要權益

* RDP Shortpath 傳輸是以高效率的  [通用速率控制通訊協定為基礎， (URCP) ](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)。 URCP 藉由主動監視網路狀況來增強 UDP，並提供公平且完整的連結使用率。 URCP 會依遠端桌面所需的低延遲和遺失層級運作。 URCP 藉由動態學習網路參數，並提供具有速率控制機制的通訊協定來達到最佳效能。
* RDP Shortpath 會在遠端桌面用戶端與會話主機之間建立直接連線。 直接連線可降低對 Windows 虛擬桌面閘道的相依性、改善連線的可靠性，並增加每個使用者會話的可用頻寬。
* 移除額外的轉送可減少來回行程時間，進而改善延遲敏感應用程式和輸入方法的使用者體驗。
* RDP Shortpath 透過差異服務的程式碼點 (DSCP) 標記，為 RDP 連線設定 [服務品質 (QoS) ](./rdp-quality-of-service-qos.md) 優先順序
* RDP Shortpath 傳輸可指定每個會話的節流閥速率，藉此 [限制輸出的網路流量](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) 。

## <a name="connection-security"></a>連接安全性

RDP Shortpath 正在擴充 RDP 多重傳輸功能。 它不會取代反向連接傳輸，而是補充它。 所有初始的會話代理都是透過 Windows 虛擬桌面基礎結構進行管理。

UDP 埠3390僅用於透過反向連接傳輸進行驗證的傳入 Shortpath 流量。 RDP Shortpath 接聽程式會忽略接聽程式的所有連接嘗試，除非它們符合反向連接會話。

RDP Shortpath 使用工作階段主機的憑證，在用戶端與會話主機之間使用 TLS 連接。 根據預設，用於 RDP 加密的憑證是在部署期間由作業系統自行產生的。 如有需要，客戶可以部署企業憑證授權單位單位所發行的集中受控憑證。 如需有關憑證設定的詳細資訊，請參閱 [Windows Server 檔](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations.md)。

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath 連接順序

安裝 [反向連接傳輸](./network-connectivity.md)之後，用戶端和工作階段主機會建立 RDP 連線，並協商多重傳輸功能。 其他步驟如下所述：

1. 工作階段主機會將其私人和公用 IPv4 和 IPv6 地址清單傳送給用戶端。
2. 用戶端會啟動背景執行緒，以直接建立一部主機 IP 位址的平行 UDP 傳輸。
3. 當用戶端探查提供的 IP 位址時，它會繼續透過反向連接傳輸來建立初始連線，以確保使用者連線時不會有延遲。
4. 如果用戶端有直接的連線，且防火牆設定是正確的，用戶端就會與會話主機建立安全的 TLS 連線。
5. 建立 Shortpath 傳輸之後，RDP 會將所有動態虛擬通道 (DVCs) ，包括遠端圖形、輸入和裝置重新導向至新的傳輸。
6. 如果防火牆或網路拓撲防止用戶端建立直接的 UDP 連線，RDP 會繼續進行反向連接傳輸。

下圖提供 RDP Shortpath 網路連線的高層級總覽。

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="RDP Shortpath 網路連線的圖表" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>需求

若要支援 RDP Shortpath，Windows 虛擬桌面用戶端必須能夠直接看到工作階段主機。 您可以使用下列其中一種技術來取得直接的瞭解：

* [ExpressRoute 私人對等互連](../expressroute/expressroute-circuit-peerings.md)
* [站對站 VPN (以 IPsec 為基礎的) ](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [點對站 VPN (以 IPsec 為基礎的) ](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [公用 IP 位址指派](../virtual-network/virtual-network-public-ip-address.md)

如果您使用其他 VPN 類型來連線至 Azure 虛擬網路，建議使用 UDP 型 VPN 以獲得最佳結果。 雖然大部分的 TCP 型 VPN 解決方案會封裝所有 IP 封包（包括 UDP），但它們會新增 TCP 擁塞控制的繼承額外負荷，進而降低 RDP 效能。

直接看到的行表示防火牆不會封鎖 UDP 埠3390，而用戶端可以直接連接到工作階段主機。

## <a name="enabling-rdp-shortpath-preview"></a>啟用 RDP Shortpath 預覽

若要參與 RDP Shortpath 的預覽，您需要在工作階段主機上啟用 RDP Shortpath 接聽程式。 您可以在環境中使用的任何數目的工作階段主機上啟用 RDP Shortpath。 不需要在集區中的所有主機上啟用 RDP Shortpath。
若要啟用 Shortpath 接聽程式，您必須設定下列登錄值：

> [!WARNING]
> 如果您使用登錄編輯程式或使用其他方法不正確地修改登錄，可能會發生嚴重的問題。 這些問題可能需要您重新安裝作業系統。 Microsoft 無法保證可以解決這些問題。 您必須自行承擔修改登錄的風險。

1. 在工作階段主機上，啟動 Regedit.exe，然後流覽至下列位置：

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. 建立名為 **fUseUdpPortRedirector** 的新 **DWORD** 值，並將它設定為 **1** (decimal) 
3. 建立名為 **UdpPortNumber** 的新 **DWORD** 值，並將它設定為 **3390** (decimal) 
4. 結束 [登錄編輯程式]。
5. 重新開機工作階段主機

您也可以在提高許可權的 PowerShell 視窗中執行下列 Cmdlet，以設定這些登錄值：

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

您也可以使用 PowerShell 來設定群組原則

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>使用 Advanced Security 設定 Windows Defender 防火牆

若要允許 RDP Shortpath 的輸入網路流量，請使用群組原則管理 MMC 嵌入式管理單元中的 [具有 Advanced Security 的 Windows Defender 防火牆] 節點來建立防火牆規則。

1. 開啟群組原則管理主控台，以 [使用 Advanced Security Windows Defender 防火牆](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security)。
2. 在流覽窗格中，選取 [ **輸入規則**]。
3. 選取 [ **動作**]，然後選取 [ **新增規則**]。
4. 在 [新增輸入規則] 嚮導的 [ **規則類型** ] 頁面上，選取 [ **自訂**]，然後選取 **[下一步]**。
5. 在 [ **程式** ] 頁面上，選取 [ **這個程式路徑**]，然後輸入 "% SystemRoot% \system32\svchost.exe '，然後選取 **[下一步]**。
6. 在 [ **通訊協定和埠** ] 頁面上，選取 UDP 通訊協定類型。 在 [ **本機埠**] 中選取 [特定埠]，然後輸入3390。
7. 您可以在 [範圍] 頁面中，指定規則只套用到在此頁面上輸入之 IP 位址的連入或連出網路流量。 針對您的設計適當地設定，然後選取 **[下一步]**。
8. 在 [ **動作** ] 頁面上，選取 [ **允許** 連線]，然後選取 **[下一步]**。
9. 在 [ **設定檔** ] 頁面上，選取要套用此規則的網路位置類型，然後選取 **[下一步]**。
10. 在 [ **名稱** ] 頁面上，輸入規則的名稱和描述，然後選取 **[完成]**。

您可以驗證新規則是否符合以下螢幕擷取畫面： :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="RDP Shortpath 網路連線的防火牆設定 [一般]" lightbox="media/rdp-shortpath-firewall-general-tab.png":::索引標籤的螢幕擷取畫面

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="RDP Shortpath 網路連線之防火牆設定的 [程式和服務] 索引標籤螢幕擷取畫面" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="RDP Shortpath 網路連線防火牆設定的 [通訊協定] 和 [埠] 索引標籤的螢幕擷取畫面" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

您也可以使用 PowerShell 來設定 Windows 防火牆：

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>使用 PowerShell 設定 Windows Defender 防火牆

您也可以使用 PowerShell 來設定群組原則

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

## <a name="configuring-azure-network-security-group"></a>正在設定 Azure 網路安全性群組

若要允許跨網路安全性界限存取 RDP Shortpath 接聽程式，您必須設定 Azure 網路安全性群組以允許輸入 UDP 埠3390。
遵循 [網路安全性群組檔](../virtual-machines/windows/nsg-quickstart-portal.md) 來建立輸入安全性規則，以允許具有下列參數的流量：

* **來源**  - 用戶端所在的 **任何** 或 IP 範圍
* **來源埠範圍**-* *\** _ _ **目的地**  -  **任何**
* **目的地埠範圍**  - **3390**
* **通訊協定**  - **UDP**
* **動作**  - **允許**
* （選擇性）變更 **優先順序**。 優先順序會影響規則的套用順序，數值越低的規則越早套用。
* **名稱** -- **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>停用特定子網的 RDP Shortpath

如果您需要使用 RDP Shortpath 傳輸封鎖特定子網，您可以設定其他指定來源 IP 範圍的網路安全性群組。

## <a name="verifying-the-connectivity"></a>驗證連線能力

### <a name="using-connection-information-dialog"></a>使用連接資訊對話方塊

若要確認連線使用 RDP Shortpath，請按一下 [連線] 工具列中的天線圖示，以開啟 [連接資訊] 對話方塊。

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="遠端桌面連線 Bar 的影像":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="遠端桌面連線資訊對話方塊的影像":::

### <a name="using-event-logs"></a>使用事件記錄檔

若要確認會話是否使用 RDP Shortpath 傳輸：

1. 使用 Windows 虛擬桌面用戶端連線到 VM 桌面。
2. 啟動事件檢視器，然後流覽至下列節點： **應用程式和服務記錄檔 > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > 操作**
3. 若要判斷是否使用 RDP Shortpath 傳輸，請尋找事件識別碼131。

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>使用 Log Analytics 來確認 Shortpath 連線能力

如果您使用 [Azure Log Analytics](./diagnostics-log-analytics.md)，您可以藉由查詢 [WVDConnections 資料表](/azure/azure-monitor/reference/tables/wvdconnections)來監視連接。 名為 UdpUse 的資料行，指出 Windows 虛擬桌面 RDP 堆疊是否在目前的使用者連接上使用 UDP 通訊協定。
可能的值包括：

* **0** -使用者連接未使用 RDP Shortpath
* **1** -使用者連接正在使用 RDP Shortpath
  
下列查詢清單可讓您查看連接資訊。 您可以在 [Log Analytics 查詢編輯器](../azure-monitor/log-query/get-started-portal.md#write-and-run-basic-queries)中執行此查詢。 針對每個查詢，將取代為 `userupn` 您要查閱之使用者的 UPN。

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>疑難排解

### <a name="verify-shortpath-listener"></a>確認 Shortpath 接聽程式

若要確認已啟用 UDP 接聽程式，請在工作階段主機上使用下列 PowerShell 命令：

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

若已啟用，您將會看到如下所示的輸出

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

如果發生衝突，您可以使用下列命令來識別佔用埠的進程

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>停用 RDP Shortpath

在某些情況下，您可能需要停用 RDP Shortpath 傳輸。 您可以使用群組原則來停用 RDP Shortpath。

### <a name="disabling-rdp-shortpath-on-the-client"></a>在用戶端上停用 RDP Shortpath

若要停用特定用戶端的 RDP Shortpath，您可以使用下列群組原則來停用 UDP 支援：

1. 在用戶端上，執行 **gpedit.msc**。
2. 流覽至 [ **電腦設定 > 系統管理範本] > [Windows 元件] > 遠端桌面服務 > 遠端桌面連線用戶端**。
3. 將 [**在用戶端上關閉 UDP]** 設定設為 [**已啟用**]

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>停用工作階段主機上的 RDP Shortpath

若要停用特定工作階段主機的 RDP Shortpath，您可以使用下列群組原則來停用 UDP 支援：

1. 在工作階段主機上執行 **gpedit.msc**。
2. 流覽至 [ **電腦設定 > 系統管理範本] > [Windows 元件] > 遠端桌面服務 > 遠端桌面連線主機 > 連接**。
3. 將 [**選取 RDP 傳輸通訊協定]** 設定設為 [**僅限 TCP** ]

## <a name="public-preview-feedback"></a>公開預覽意見反應

我們想要聽取您對於此公開預覽體驗的意見！
* 針對問題、要求、批註和其他意見反應，請 [使用此意見反應表單](https://aka.ms/RDPShortpathFeedback)。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 Windows 虛擬桌面網路連線能力，請參閱 [瞭解 Windows 虛擬桌面網路連線能力](network-connectivity.md)。
* 若要開始使用適用于 Windows 虛擬桌面的服務品質 (QoS) ，請參閱 [為 Windows 虛擬桌面 (qos) 執行服務品質](rdp-quality-of-service-qos.md)。
