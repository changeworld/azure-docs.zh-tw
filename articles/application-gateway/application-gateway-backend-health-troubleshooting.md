---
title: 解決 Azure 應用程式閘道中的後端執行狀況問題
description: 描述如何解決 Azure 應用程式閘道的後端執行狀況問題
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: a16120194b1b8015466005f42336828c2b4ace6c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983835"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>解決應用程式閘道中的後端執行狀況問題
==================================================

<a name="overview"></a>概觀
--------

默認情況下,Azure 應用程式閘道會探測後端伺服器以檢查其運行狀況並檢查它們是否已準備好服務請求。 使用者還可以創建自定義探測器來提及主機名、要探測的路徑以及要接受為"正常"的狀態代碼。 在每種情況下,如果後端伺服器未成功回應,應用程式閘道將伺服器標記為不正常,並停止將請求轉發到伺服器。 伺服器成功開始回應后,應用程式閘道將恢復轉發請求。

### <a name="how-to-check-backend-health"></a>如何檢查後端介面執行狀況

要檢查後端池的運行狀況,可以使用 Azure 門戶上的 **「後端運行狀況**」頁。 或者,您可以使用[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [、CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)或[REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)。

這些方法中的任何一種檢索的狀態可以是以下任一:

- Healthy

- 狀況不良

- Unknown

如果伺服器的後端運行狀況為"正常",則意味著應用程式閘道會將請求轉發到該伺服器。 但是,如果後端池中的所有伺服器的後端運行狀況不正常或未知,則在嘗試訪問應用程式時可能會遇到問題。 本文介紹了顯示的每個錯誤的癥狀、原因和解決方法。

<a name="backend-health-status-unhealthy"></a>後端運行狀況:不正常
-------------------------------

如果後端執行狀況不正常,門戶檢視將類似於以下螢幕截圖:

![應用程式閘道後端介面執行狀況 ─ 不正常](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

或者,如果您使用的是 Azure PowerShell、CLI 或 Azure REST API 查詢,您將獲得類似於以下內容的回應:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
收到後端介面中所有伺服器的不正常後端伺服器狀態後,請求不會轉發到伺服器,應用程式閘道將「502 錯誤閘道」錯誤傳回給請求的用戶端。 要解決此問題,請查看 **「後端執行狀況**」選項卡上的 **「詳細資訊**」欄。

**"詳細資訊"** 列中顯示的消息提供有關該問題的更詳細見解,並基於這些見解,您可以開始排除故障。

> [!NOTE]
> 默認探測\<請求\>以協定 127.0.0.1:\<連接埠\>/的格式發送。 例如,http://127.0.0.1:80對於埠 80 上的 HTTP 探測。 只有 200 到 399 的 HTTP 狀態代碼被視為正常。 協議和目標埠是從 HTTP 設置繼承的。 如果希望應用程式網關探測其他協定、主機名或路徑,並將其他狀態代碼識別為"正常",請配置自定義探測並將其與 HTTP 設定相關聯。

<a name="error-messages"></a>錯誤訊息
------------------------
#### <a name="backend-server-timeout"></a>後端伺服器逾時

**訊息:** 後端回應應用程式閘道\'運行狀況探測器所佔用的時間超過探測設置中的超時閾值。

**原因:** 應用程式閘道向後端伺服器發送 HTTP(S) 探測請求後,它將等待後端伺服器的回應,等待配置的時間段。 如果後端伺服器未在配置的時間段內(超時值)內回應,則將其標記為不正常,直到它在配置的超時期間再次開始回應。

**解析度:** 檢查後端伺服器或應用程式在配置的超時期間未回應的原因,並檢查應用程式依賴項。 例如,檢查資料庫是否存在可能導致回應延遲的問題。 如果您知道應用程式的行為,並且它應僅在超時值后回應,請從自定義探測設置中增加超時值。 您必須具有自定義探測才能更改超時值。 有關如何設定自訂偵測的資訊,[請參考文件頁](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

要增加超時值,請按照以下步驟操作:

1.  直接訪問後端伺服器,並檢查伺服器在該頁上回應所需要時間。 您可以使用任何工具訪問後端伺服器,包括使用開發人員工具的瀏覽器。

1.  計算應用程式回應所需要時間後,選擇 **「運行狀況探測」** 選項卡,然後選擇與您的 HTTP 設定關聯的探測。

1.  輸入任何大於應用程式回應時間(以秒為單位)超時值。

1.  保存自定義探測設置,並檢查後端運行狀況是否立即顯示為"正常"。

#### <a name="dns-resolution-error"></a>DNS 解析錯誤

**訊息:** 應用程式閘道無法為此後端創建探測。 此錯誤通常發生在後端的 FQDN 未正確輸入時。 

**原因:** 如果後端介面的類型為 IP 位址/FQDN 或應用服務,則應用程式閘道將解析為透過網域名稱系統 (DNS)(自訂或 Azure 預設)輸入的 FQDN 的 IP 位址,並嘗試連接到 HTTP 設定中提到的 TCP 埠上的伺服器。 但是,如果顯示此消息,則表明應用程式閘道無法成功解析輸入的 FQDN 的 IP 位址。

**解決方案：**

1.  驗證在後端池中輸入的 FQDN 是否正確,以及它是公有域,然後嘗試從本地電腦解析它。

1.  如果可以解析 IP 位址,則虛擬網路中的 DNS 配置可能有問題。

1.  檢查虛擬網路是否配置了自訂 DNS 伺服器。 如果是,請檢查 DNS 伺服器,瞭解為什麼它無法解析為指定的 FQDN 的 IP 位址。

1.  如果使用 Azure 預設 DNS,請諮詢功能變數名稱註冊商,瞭解是否已完成適當的 A 記錄或 CNAME 記錄映射。

1.  如果域是私有的或內部的,請嘗試從同一虛擬網路中的 VM 解析它。 如果可以解析它,請重新啟動應用程式閘道並重新檢查。 要重新啟動應用程式閘道,您需要使用這些連結資源中描述的 PowerShell 命令[停止](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0)[並開始。](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0)

#### <a name="tcp-connect-error"></a>TCP 連線錯誤

**訊息:** 應用程式閘道無法連接到後端介面。
請檢查用於探頭的埠上的後端回應。
還要檢查是否有任何 NSG/UDR/防火牆阻止存取此後端的 Ip 和埠

**原因:** 在 DNS 解析階段之後,應用程式閘道將嘗試連接到 HTTP 設定中配置的 TCP 連接埠上的後端伺服器。 如果應用程式閘道無法在指定的埠上建立 TCP 會話,則使用此消息將探測標記為不正常。

**解決方案:** 如果收到此錯誤,請按照以下步驟操作:

1.  檢查是否可以使用瀏覽器或 PowerShell 連接到 HTTP 設定中提到的連接埠上的後端伺服器。 例如,執行以下指令:`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  如果提到的連接埠不是所需的連接埠,請輸入正確的連接埠號,以便應用程式閘道連接到後端伺服器

1.  如果也不能從本地電腦在埠上連接,則:

    a.  檢查後端伺服器的網路配接器和子網路網路安全組 (NSG) 設定,以及是否允許與配置埠的入站連接。 如果不是,請創建新規則以允許連接。 要瞭解如何建立 NSG 規則,[請參考文件頁](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)。

    b.  檢查應用程式閘道的 NSG 設定是否允許出站公共和專用流量,以便可以建立連接。 查看步驟 3a 中提供的文檔頁,瞭解有關如何創建 NSG 規則的更多詳細資訊。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  檢查應用程式閘道的使用者定義的路由 (UDR) 設置和後端伺服器的子網路是否有任何路由異常。 確保UDR不會將流量從後端子網定向到。 例如,檢查通過網路虛擬設備路由或通過 Azure ExpressRoute 和/或 VPN 向應用程式閘道網通告的預設路由。

    d.  要檢查網路介面介面的有效路由和規則,可以使用以下 PowerShell 命令:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  如果未發現 NSG 或 UDR 的任何問題,請檢查後端伺服器中是否有阻止用戶端在配置的埠上建立 TCP 作業階段的應用程式相關問題。 需要檢查的一些事項:

    a.  開啟命令提示符 (Win+R\> - cmd),輸入`netstat`,然後選擇 Enter。

    b.  檢查伺服器是否偵聽配置的埠。 例如：
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  如果未在配置的埠上偵聽,請檢查 Web 伺服器設置。 例如:IIS 中的網站綁定、NGINX 中的伺服器塊和 Apache 中的虛擬主機。

    d.  檢查作業系統防火牆設置,以確保允許傳入埠的流量。

#### <a name="http-status-code-mismatch"></a>HTTP 狀態代碼不符合

**訊息:** 後端\'的 HTTP 回應的狀態代碼與探測設定不匹配。 預期:{HTTPStatusCode0}已收到:[HTTPStatusCode1]。

**原因:** 建立 TCP 連接並完成 TLS 握手後(如果啟用 TLS),應用程式閘道將以 HTTP GET 請求方式將探測器發送到後端伺服器。 如前所述,默認探測器\<將\>到協定TT7.0.0.1:\<\>連接埠 /,並且它認為rage 200到399中的回應狀態代碼為"正常" 如果伺服器返回任何其他狀態代碼,則此消息將標記為"不正常"。

**解決方案:** 根據後端伺服器的回應代碼,您可以執行以下步驟。 此處列出了一些常見的狀態代碼:

| **錯誤** | **動作** |
| --- | --- |
| 探測狀態代碼不匹配:已接收 401 | 檢查後端伺服器是否需要身份驗證。 此時,應用程式網關探測器無法傳遞身份驗證憑據。 允許在探\"測 狀態代碼\"匹配中 允許 HTTP 401,要麼對伺服器不需要身份驗證的路徑進行探測。 | |
| 探測狀態代碼不匹配:已接收 403 | 禁止進入。 檢查後端伺服器上是否允許訪問該路徑。 | |
| 探測狀態代碼不匹配:已接收 404 | 找不到頁面。 檢查主機名路徑在後端伺服器上是否可訪問。 將主機名或路徑參數更改為可存取的值。 | |
| 探測狀態代碼不匹配:已接收 405 | 應用程式閘道的探測請求使用 HTTP GET 方法。 檢查伺服器是否允許此方法。 | |
| 探測狀態代碼不匹配:已接收 500 | 內部伺服器錯誤。 檢查後端伺服器的健康情況，以及服務是否正在執行中。 | |
| 探測狀態代碼不匹配:已收到 503 | 服務無法使用。 檢查後端伺服器的健康情況，以及服務是否正在執行中。 | |

或者,如果您認為回應是合法的,並且希望應用程式閘道接受其他狀態代碼為"正常",則可以創建自定義探測。 此方法在後端網站需要身份驗證的情況下非常有用。 由於探測請求不攜帶任何使用者憑據,因此它們將失敗,後端伺服器將返回 HTTP 401 狀態代碼。

要建立自訂偵測器,請按照[以下步驟操作](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

#### <a name="http-response-body-mismatch"></a>HTTP 回應本文不符

**訊息:** 後端\'的 HTTP 回應的正文與探測設定不匹配。 已接收的回應正文不包含 [字串]。

**原因:** 創建自定義探測時,可以選擇通過匹配回應正文中的字串將後端伺服器標記為"正常"。 例如,您可以將應用程式閘道配置為接受「未授權」作為要匹配的字串。 如果探測請求的後端伺服器回應包含**未授權**字串,它將標記為"正常"。 否則,它將被標記為"使用此消息不正常」。

**解決方案:** 要解決此問題,請按照以下步驟操作:

1.  在本地或從探測路徑上的用戶端計算機訪問後端伺服器,並檢查回應正文。

1.  驗證應用程式閘道自定義探測配置中的回應正文是否與配置的匹配。

1.  如果它們不匹配,請更改探測配置,以便具有正確的字串值。

瞭解有關[應用程式閘道探測器匹配](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)的更多。

#### <a name="backend-server-certificate-invalid-ca"></a>後端伺服器憑證不合法的憑證

**訊息:** 後端使用的伺服器證書不由知名證書頒發機構 (CA) 簽名。 通過上載後端使用的伺服器證書的根證書,在應用程式閘道上對後端進行白名單。

**原因:** 使用應用程式閘道 v2 的端到端 SSL 需要驗證後端伺服器的證書,以便認為伺服器正常。
要信任 TLS/SSL 憑證,後端伺服器的證書必須由包含在應用程式閘道的可信儲存中的 CA 頒發。 如果證書不是由受信任的 CA 頒發的(例如,如果使用自簽名證書),使用者應將頒發者證書上傳到應用程式閘道。

**解決方案:** 按照以下步驟將受信任的根證書匯出並上傳到應用程式閘道。 (這些步驟適用於 Windows 用戶端。

1.  登錄到託管應用程式的計算機。

1.  選擇"贏\R"或右鍵按兩下"**開始"** 按鈕,然後選擇"**運行**"。

1.  輸入`certmgr.msc`並選擇"輸入" 您還可以在 **「開始」** 選單上搜尋憑證管理員。

1.  找到證書,通常在`\Certificates - Current User\\Personal\\Certificates\`中 ,然後打開它。

1.  選擇根證書,然後選擇 **「查看證書**」。

1.  在"證書"屬性中,選擇 **「詳細資訊**」選項卡。

1.  在「**詳細資訊**」選項卡上,選擇「**複製到檔案**」選項,並將檔案儲存在 Base-64 編碼的 X.509 (中。CER) 格式。

1.  在 Azure 門戶中打開應用程式閘道 HTTP**設定**頁。

1. 開啟 HTTP 設定,選擇 **「新增證書**」,然後找到剛剛儲存的憑證檔。

1. 選擇 **「儲存**」以儲存 HTTP 設定。

或者,您可以通過直接通過瀏覽器訪問伺服器(繞過應用程式閘道)並從瀏覽器匯出根證書,從用戶端電腦匯出根證書。

有關如何在應用程式閘道中提取和上載受信任的根證書的詳細資訊,請參閱[匯出受信任的根證書(對於 v2 SKU)。](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)

#### <a name="trusted-root-certificate-mismatch"></a>信任的根憑證不符合

**訊息:** 後端使用的伺服器證書的根憑證與添加到應用程式閘道的受信任根證書不匹配。 確保將正確的根憑證加入後端白名單

**原因:** 使用應用程式閘道 v2 的端到端 SSL 需要驗證後端伺服器的證書,以便認為伺服器正常。
要信任 TLS/SSL 憑證,後端伺服器證書必須由應用程式閘道的可信存儲中包含的 CA 頒發。 如果證書不是由受信任的 CA 頒發的(例如,使用了自簽名證書),使用者應將頒發者證書上傳到應用程式閘道。

已上傳到應用程式閘道 HTTP 設定的憑證必須與後端伺服器證書的根憑證匹配。

**解決方案:** 如果收到此錯誤消息,則已上傳到應用程式閘道的證書與上載到後端伺服器的證書不匹配。

按照上述方法的步驟 1-11 將正確的受信任根證書上載到應用程式網關。

有關如何在應用程式閘道中提取和上載受信任的根證書的詳細資訊,請參閱[匯出受信任的根證書(對於 v2 SKU)。](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)
> [!NOTE]
> 如果後端伺服器在 TLS 握手期間未交換證書的完整鏈(包括根>中間體(如果適用)> Leaf,則也可能發生此錯誤。 要驗證,可以使用來自任何用戶端的 OpenSSL 命令,並使用應用程式閘道探測中的設定設定連接到後端伺服器。

例如：
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
如果輸出未顯示要返回的證書的完整鏈,則再次使用完整鏈(包括根證書)匯出證書。 在後端伺服器上配置該證書。 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>後端憑證不合法名稱 (CN)

**訊息:** 後端證書的通用名稱 (CN) 與探測器的主機標頭不匹配。

**原因:** 應用程式閘道檢查後端 HTTP 設定中指定的主機名是否與後端伺服器 TLS/SSL 憑證提供的 CN 的名稱匹配。 這是Standard_v2和WAF_v2 SKU 行為。 標準和 WAF SKU 的伺服器名稱指示 (SNI) 設置為後端池位址中的 FQDN。

在 v2 SKU 中,如果存在默認探測(未配置和關聯自定義探測器),將從 HTTP 設定中提到的主機名設置 SNI。 或者,如果在 HTTP 設定中提到「從後端位址選擇主機名」,其中後端位址池包含有效的 FQDN,則將應用此設置。

如果存在與 HTTP 設定關聯的自定義探測,將從自定義探測配置中提到的主機名設置 SNI。 或者,如果在自定義探測中選擇**後端 HTTP 設定中的選取主機名**,則將從 HTTP 設定中提到的主機名設置 SNI。

如果在 HTTP 設定中設定了**從後端位址選取主機名**,後端位址池必須包含有效的 FQDN。

如果收到此錯誤消息,後端證書的 CN 與自定義探測或 HTTP 設定中配置的主機名不匹配(如果從後端 HTTP**設定中選擇選取主機名**)。 如果使用預設探測器,主機名將設置為**127.0.0.1**。 如果這不是所需的值,則應創建自定義探測並將其與 HTTP 設定相關聯。

**解決機制:**

若要解決此問題，請遵循下列步驟。

若為 Windows：

1.  登錄到託管應用程式的計算機。

1.  選擇 Win+R 或右鍵按兩下 **「開始」** 按鈕,然後選擇「**執行**」 。

1.  輸入**憑證,** 然後選擇" 您還可以在 **「開始」** 選單上搜尋憑證管理員。

1.  找到證書(通常在`\Certificates - Current User\\Personal\\Certificates`中),然後打開證書。

1.  在 **「詳細資訊」** 選項卡上, 檢查憑證**主題**。

1.  從詳細資訊驗證證書的 CN,並在自定義探測的主機名字段中或 HTTP 設定中輸入相同內容(如果從後端**HTTP 設定中選擇選取主機名**)。 如果這不是網站所需的主機名,則必須獲取該域的證書,或在自定義探測或 HTTP 設定配置中輸入正確的主機名。

對 OpenSSL 的 Linux:

1.  在 OpenSSL 執行此指令:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  從顯示的屬性中,找到證書的 CN,並在 HTTP 設置的主機名字段中輸入相同的內容。 如果這不是網站所需的主機名,則必須獲取該域的證書,或在自定義探測或 HTTP 設定配置中輸入正確的主機名。

#### <a name="backend-certificate-is-invalid"></a>後端憑證無效

**訊息:** 後端證書無效。 當前日期\"不在證書上的"有效自\"\""\"和" 有效日期"

**原因:** 每個證書都有有效性範圍,除非伺服器的 TLS/SSL 證書有效,否則 HTTPS 連接將不安全。 當前數據必須在**有效範圍內**,**有效到**範圍。 如果不是,證書將被視為無效,這將造成一個安全問題,其中應用程式網關將後端伺服器標記為不正常。

**解決方案:** 如果您的 TLS/SSL 憑證已過期,請與供應商續訂證書,並使用新證書更新伺服器設置。 如果是自簽名證書,則必須生成有效的證書並將根證書上傳到應用程式閘道 HTTP 設定。 若要這樣做，請遵循下列步驟：

1.  在門戶中打開應用程式閘道 HTTP 設定。

1.  選擇具有過期證書的設置,選擇 **「添加證書**」並打開新的憑證檔。

1.  使用憑證旁邊的 **「刪除」** 圖示刪除舊證書,然後選擇 **「儲存**」 。

#### <a name="certificate-verification-failed"></a>憑證驗證失敗

**訊息:** 無法驗證後端證書的有效性。 要找出原因,請檢查與錯誤代碼 [錯誤代碼] 關聯的消息的 OpenSSL 診斷。

**原因:** 當應用程式閘道無法驗證證書的有效性時,將發生此錯誤。

**解決方案:** 要解決此問題,請驗證伺服器上的證書是否正確創建。 例如,可以使用[OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html)驗證證書及其屬性,然後嘗試將證書重新載入到應用程式閘道 HTTP 設定。

<a name="backend-health-status-unknown"></a>後端介面執行狀況:未知
-------------------------------
如果後端執行狀況顯示為「未知」,則門戶檢視將類似於以下螢幕截圖:

![應用程式閘道後端介面執行狀況 ─未知](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

發生此行為的原因有以下一個或多個:

1.  應用程式閘道網上的 NSG 阻止從"Internet"訪問埠 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU)。
1.  應用程式閘道網上的 UDR 設定為預設路由 (0.0.0.0/0),下一個躍點未指定為"Internet"。
1.  默認路由通過透過 BGP 與虛擬網路的 ExpressRoute/VPN 連接通告。
1.  自定義 DNS 伺服器設定在無法解決公有網域名稱的虛擬網路上。
1.  應用程式閘道處於不正常狀態。

**解決機制:**

1.  檢查您的 NSG 是否阻止**從 Internet**存取埠 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU):

    a.  在"應用程式閘道**概述'** 選項卡上,選擇**虛擬網路/子網**連結。

    b.  在虛擬**網路的「子網」** 選項卡上,選擇已部署應用程式閘道的子網路。

    c.  檢查是否配置了任何 NSG。

    d.  如果設定了 NSG,則在 **「搜尋」** 選項卡或 **「所有資源**」下搜索該 NSG 資源。

    e.  在 **「入站規則」** 部分中,添加入站規則以允許 v1 SKU 的目標埠範圍 65503-65534 或 65200-65535 v2 SKU,**源**設置為 **"任意'** 或 **"Internet"。**

    f.  選擇 **「保存**並驗證是否可以將後端視為正常」。 或者,您可以通過[PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)進行執行此操作。

1.  檢查您的 UDR 是否有預設路由 (0.0.0.0/0),下一個躍點未設置為**Internet:**
    
    a.  按照步驟 1a 和 1b 確定子網。

    b.  檢查是否配置了 UDR。 如果有,則在搜索欄或 **「所有資源**」下搜索資源。

    c.  檢查是否有任何預設路由 (0.0.0.0/0), 下一個躍點未設定為**Internet**。 如果設置是**虛擬設備**或**虛擬網路閘道**,則必須確保虛擬裝置或本地設備可以正確路由資料包回 Internet 目標,而無需修改數據包。

    d.  否則,將下一個躍點更改為**Internet,** 選擇 **「保存**」並驗證後端運行狀況。

1.  以 BGP 與虛擬網路的 ExpressRoute/VPN 連接通告的預設路由:

    a.  如果您通過 BGP 與虛擬網路建立了 ExpressRoute/VPN 連接,並且正在播發預設路由,則必須確保數據包路由回 Internet 目標,而無需對其進行修改。 您可以使用應用程式網關門戶中的 **「連接疑難解答」** 選項進行驗證。

    b.  手動選擇目標作為任何可網路路由 IP 位址(如 1.1.1.1)。 將目標埠設置為任何內容,並驗證連接。

    c.  如果下一個躍點是虛擬網路閘道,則可能存在透過 ExpressRoute 或 VPN 通告的預設路由。

1.  如果虛擬網路上配置了自訂 DNS 伺服器,請驗證伺服器(或伺服器)是否可以解析公共域。 在應用程式閘道必須連接到外部域(如 OCSP 伺服器)或檢查證書的吊銷狀態的情況下,可能需要公有域名稱解析。

1.  要驗證應用程式閘道是否正常且執行正常,請轉到門戶中的 **「資源執行狀況」** 選項,並驗證狀態是否**正常**。 如果看到**不正常**或**降級**狀態,[請與支援 區列 。](https://azure.microsoft.com/support/options/)

<a name="next-steps"></a>後續步驟
----------

瞭解有關[應用程式閘道診斷和日誌記錄](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)的更多資訊。
