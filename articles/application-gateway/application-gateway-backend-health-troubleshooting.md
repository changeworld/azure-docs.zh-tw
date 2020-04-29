---
title: 針對 Azure 應用程式閘道中的後端健康情況問題進行疑難排解
description: 說明如何針對 Azure 應用程式閘道的後端健康情況問題進行疑難排解
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: a16120194b1b8015466005f42336828c2b4ace6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80983835"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>針對應用程式閘道中的後端健康情況問題進行疑難排解
==================================================

<a name="overview"></a>總覽
--------

根據預設，Azure 應用程式閘道會探查後端伺服器，以檢查其健康狀態，並檢查是否已準備好處理要求。 使用者也可以建立自訂探查來提及主機名稱、要探查的路徑，以及要接受為狀況良好的狀態碼。 在每個案例中，如果後端伺服器未成功回應，應用程式閘道會將伺服器標示為狀況不良，並停止將要求轉送到伺服器。 伺服器開始回應成功之後，應用程式閘道繼續轉送要求。

### <a name="how-to-check-backend-health"></a>如何檢查後端健全狀況

若要檢查後端集區的健康情況，您可以使用 Azure 入口網站上的 [**後端健康**情況] 頁面。 或者，您可以使用[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0)、 [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)或[REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)。

任何這些方法所抓取的狀態可以是下列其中一項：

- Healthy

- Unhealthy

- Unknown

如果伺服器的後端健全狀況狀態為狀況良好，則表示應用程式閘道會將要求轉送到該伺服器。 但是，如果後端集區中所有伺服器的後端健康情況不良或不明，您可能會在嘗試存取應用程式時遇到問題。 本文說明每個所顯示錯誤的徵兆、原因和解決方式。

<a name="backend-health-status-unhealthy"></a>後端健全狀況狀態：狀況不良
-------------------------------

如果後端健全狀況狀態為狀況不良，入口網站視圖會類似下列螢幕擷取畫面：

![應用程式閘道後端健康情況-狀況不良](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

或者，如果您使用 Azure PowerShell、CLI 或 Azure REST API 查詢，您會收到類似下列的回應：
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
當您收到後端集區中所有伺服器的狀況不良後端伺服器狀態之後，要求不會轉送到伺服器，應用程式閘道會將「502錯誤的閘道」錯誤傳回給要求的用戶端。 若要疑難排解此問題，請檢查 [**後端健康**情況] 索引標籤上的 [**詳細資料**]

[**詳細資料**] 欄中顯示的訊息會提供有關此問題的詳細資訊，並根據這些資訊，您可以開始針對問題進行疑難排解。

> [!NOTE]
> 預設的\<探查要求會以通訊協定\>：//127.0.0.1：\<port\>/的格式傳送。 例如， http://127.0.0.1:80針對埠80上的 HTTP 探查。 只有200到399的 HTTP 狀態碼會被視為狀況良好。 通訊協定和目的地埠會繼承自 HTTP 設定。 如果您想要應用程式閘道探查不同的通訊協定、主機名稱或路徑，並將不同的狀態碼辨識為狀況良好，請設定自訂探查，並將其與 HTTP 設定產生關聯。

<a name="error-messages"></a>錯誤訊息
------------------------
#### <a name="backend-server-timeout"></a>後端伺服器超時

**訊息：** 後端回應應用程式閘道\'健康情況探查所花費的時間，超過探查設定中的超時閾值。

**原因：** 應用程式閘道將 HTTP （S）探查要求傳送至後端伺服器之後，它會等待來自後端伺服器的回應設定一段時間。 如果後端伺服器未在設定的期間內回應（超時值），則會將它標示為狀況不良，直到重新開始在設定的超時期間內回應為止。

**解決方式：** 檢查後端伺服器或應用程式在設定的超時時間內沒有回應的原因，同時檢查應用程式的相依性。 例如，檢查資料庫是否有任何可能會觸發回應延遲的問題。 如果您知道應用程式的行為，而且它應該只在超時值之後回應，請增加自訂探查設定的超時值。 您必須具有自訂探查，才能變更超時值。 如需有關如何設定自訂探查的詳細資訊，[請參閱檔頁面](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

若要增加超時值，請遵循下列步驟：

1.  直接存取後端伺服器，並檢查伺服器在該頁面上回應所花費的時間。 您可以使用任何工具來存取後端伺服器，包括使用開發人員工具的瀏覽器。

1.  在您確定應用程式回應所花費的時間之後，請選取 [**健康情況探查**] 索引標籤，然後選取與您的 HTTP 設定相關聯的探查。

1.  輸入大於應用程式回應時間的任何超時值（以秒為單位）。

1.  儲存自訂探查設定，並檢查後端健康狀態是否顯示為狀況良好。

#### <a name="dns-resolution-error"></a>DNS 解析錯誤

**訊息：** 應用程式閘道無法為此後端建立探查。 此錯誤通常發生在後端的 FQDN 未正確輸入時。 

**原因：** 如果後端集區的類型為 IP 位址/FQDN 或 App Service，應用程式閘道會解析為透過網域名稱系統（DNS）（自訂或 Azure 預設值）輸入之 FQDN 的 IP 位址，並嘗試連接到 HTTP 設定中所述 TCP 埠上的伺服器。 但是，如果顯示此訊息，則表示應用程式閘道無法成功解析輸入之 FQDN 的 IP 位址。

**解決方案：**

1.  請確認在後端集區中輸入的 FQDN 是正確的，而且它是公用網域，然後嘗試從本機電腦加以解析。

1.  如果您可以解析 IP 位址，則虛擬網路中的 DNS 設定可能會發生問題。

1.  檢查虛擬網路是否已設定自訂 DNS 伺服器。 如果是，請檢查 DNS 伺服器為什麼無法解析為指定 FQDN 的 IP 位址。

1.  如果您使用的是 Azure 預設 DNS，請洽詢您的網域註冊機構，瞭解是否已完成適當的記錄或 CNAME 記錄對應。

1.  如果網域是私人或內部，請嘗試從相同虛擬網路中的 VM 加以解析。 如果您可以解決此問題，請重新開機應用程式閘道，然後再檢查一次。 若要重新開機應用程式閘道，您必須使用這些連結資源中所述的 PowerShell 命令來[停止](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0)並[啟動](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0)。

#### <a name="tcp-connect-error"></a>TCP 連接錯誤

**訊息：** 應用程式閘道無法連接至後端。
請檢查後端是否回應用於探查的埠。
同時檢查是否有任何 NSG/UDR/防火牆封鎖此後端的 Ip 和埠存取

**原因：** 在 DNS 解析階段之後，應用程式閘道會嘗試連線到 HTTP 設定中設定的 TCP 通訊埠上的後端伺服器。 如果應用程式閘道無法在指定的埠上建立 TCP 會話，此訊息就會將探查標示為狀況不良。

**解決方案：** 如果您收到此錯誤，請遵循下列步驟：

1.  檢查您是否可以使用瀏覽器或 PowerShell，連接到 HTTP 設定中所述埠上的後端伺服器。 例如，執行下列命令：`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  如果所述的埠不是所需的埠，請輸入正確的埠號碼，讓應用程式閘道連接到後端伺服器

1.  如果您也無法從本機電腦連線至埠，則：

    a.  檢查後端伺服器網路介面卡和子網的網路安全性群組（NSG）設定，以及是否允許連至所設定埠的輸入連接。 如果不是，請建立新的規則以允許連接。 若要瞭解如何建立 NSG 規則，[請參閱檔頁面](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)。

    b.  檢查應用程式閘道子網的 NSG 設定是否允許輸出公用和私用流量，以便進行連線。 請檢查步驟3a 中提供的檔頁面，以深入瞭解如何建立 NSG 規則。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  檢查應用程式閘道的使用者定義路由（UDR）設定和後端伺服器的子網是否有任何路由異常。 請確定 UDR 不會將流量從後端子網導向出去。 例如，檢查網路虛擬裝置的路由，或透過 Azure ExpressRoute 和/或 VPN 向應用程式閘道子網通告的預設路由。

    d.  若要檢查網路介面卡的有效路由和規則，您可以使用下列 PowerShell 命令：
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  如果您在 NSG 或 UDR 中找不到任何問題，請檢查您的後端伺服器是否有應用程式相關問題，使用戶端無法在設定的埠上建立 TCP 會話。 有幾件事需要檢查：

    a.  開啟命令提示字元（Win + R-\> cmd），輸入`netstat`，然後選取 enter。

    b.  檢查伺服器是否正在接聽已設定的埠。 例如：
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  如果它沒有在設定的埠上接聽，請檢查您的 web 伺服器設定。 例如： IIS 中的網站系結、NGINX 中的伺服器區塊，以及 Apache 中的虛擬主機。

    d.  請檢查您的 OS 防火牆設定，以確定允許埠的連入流量。

#### <a name="http-status-code-mismatch"></a>HTTP 狀態碼不符

**訊息：** 後端\'s HTTP 回應的狀態碼不符合探查設定。 預期：已收到 {HTTPStatusCode0}： {HTTPStatusCode1}。

**原因：** 建立 TCP 連線並完成 TLS 交握後（如果已啟用 TLS），應用程式閘道會將探查當做 HTTP GET 要求傳送至後端伺服器。 如先前\<所述，預設探查會是通訊協定\>：//127.0.0.1\<：\>port/，而它會將開始風行200到399中的回應狀態碼視為狀況良好。 如果伺服器傳回任何其他狀態碼，則會將此訊息標示為狀況不良。

**解決方案：** 視後端伺服器的回應碼而定，您可以採取下列步驟。 以下列出一些常見的狀態碼：

| **錯誤** | **動作** |
| --- | --- |
| 探查狀態碼不相符：收到401 | 檢查後端伺服器是否需要驗證。 應用程式閘道探查此時無法傳遞認證以進行驗證。 允許\"探查狀態碼\"中的 HTTP 401 符合或探查至伺服器不需要驗證的路徑。 | |
| 探查狀態碼不相符：收到403 | 禁止存取。 檢查後端伺服器上是否允許存取路徑。 | |
| 探查狀態碼不相符：收到404 | 找不到頁面。 檢查後端伺服器上的主機名稱路徑是否可存取。 將 [主機名稱] 或 [路徑] 參數變更為可存取的值。 | |
| 探查狀態碼不相符：收到405 | 應用程式閘道的探查要求會使用 HTTP GET 方法。 檢查您的伺服器是否允許這種方法。 | |
| 探查狀態碼不相符：收到500 | 內部伺服器錯誤。 檢查後端伺服器的健康情況，以及服務是否正在執行中。 | |
| 探查狀態碼不相符：收到503 | 服務無法使用。 檢查後端伺服器的健康情況，以及服務是否正在執行中。 | |

或者，如果您認為回應是合法的，而且您想要應用程式閘道接受其他狀態碼狀況良好，您可以建立自訂探查。 在後端網站需要驗證的情況下，這個方法很有用。 由於探查要求不會包含任何使用者認證，因此將會失敗，而且後端伺服器會傳回 HTTP 401 狀態碼。

若要建立自訂探查，請遵循下列[步驟](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

#### <a name="http-response-body-mismatch"></a>HTTP 回應主體不符

**訊息：** 後端\'s HTTP 回應的主體與探查設定不相符。 收到的回應主體不包含 {string}。

**原因：** 當您建立自訂探查時，您可以透過比對字串與回應主體，將後端伺服器標示為狀況良好。 例如，您可以將應用程式閘道設定為接受「未經授權」，以符合的字串。 如果探查要求的後端伺服器回應包含**未經授權**的字串，則會將它標示為狀況良好。 否則，會將此訊息標示為狀況不良。

**解決方案：** 若要解決此問題，請遵循下列步驟：

1.  在本機或從探查路徑上的用戶端電腦存取後端伺服器，並檢查回應本文。

1.  請確認應用程式閘道自訂探查設定中的回應主體符合已設定的內容。

1.  如果兩者不相符，請變更探查設定，使其具有正確的字串值可接受。

深入瞭解[應用程式閘道探查](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)比對。

#### <a name="backend-server-certificate-invalid-ca"></a>後端伺服器憑證不正確 CA

**訊息：** 後端所使用的伺服器憑證不是由知名的憑證授權單位單位（CA）所簽署。 上傳後端所使用之伺服器憑證的根憑證，以將應用程式閘道的後端列入白名單。

**原因：** 具有應用程式閘道 v2 的端對端 SSL 需要驗證後端伺服器的憑證，才能將伺服器視為狀況良好。
若要讓 TLS/SSL 憑證受到信任，該後端伺服器的憑證必須由受信任存放區中的應用程式閘道所包含的 CA 發行。 如果憑證不是由受信任的 CA 所發行（例如，如果使用自我簽署憑證），則使用者應該將簽發者的憑證上傳至應用程式閘道。

**解決方案：** 請遵循下列步驟，將受信任的根憑證匯出並上傳至應用程式閘道。 （這些步驟適用于 Windows 用戶端）。

1.  登入裝載您應用程式的電腦。

1.  選取 [Win + R] 或以滑鼠右鍵按一下 [**開始**] 按鈕，然後選取 [**執行**]。

1.  輸入`certmgr.msc` ，然後選取 enter 鍵。 您也可以在 [**開始**] 功能表上搜尋 [憑證管理員]。

1.  找出憑證（通常在`\Certificates - Current User\\Personal\\Certificates\`中），然後將它開啟。

1.  選取 [根憑證]，然後選取 [**查看憑證**]。

1.  在 [憑證屬性] 中，選取 [**詳細資料**] 索引標籤。

1.  在 [**詳細資料**] 索引標籤上，選取 [**複製到**檔案] 選項，然後將檔案儲存在 Base-64 編碼的 x.509 （。CER）格式。

1.  開啟 Azure 入口網站中的 [應用程式閘道 HTTP**設定**] 頁面。

1. 開啟 [HTTP 設定]，選取 [**新增憑證**]，然後找出您剛才儲存的憑證檔案。

1. 選取 [**儲存**] 以儲存 HTTP 設定。

或者，您也可以透過瀏覽器直接存取伺服器（略過應用程式閘道）並從瀏覽器匯出根憑證，從用戶端電腦匯出根憑證。

如需如何在應用程式閘道中解壓縮和上傳受信任的根憑證的詳細資訊，請參閱[匯出受信任的根憑證（適用于 V2 SKU）](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。

#### <a name="trusted-root-certificate-mismatch"></a>受信任的根憑證不相符

**訊息：** 後端所使用之伺服器憑證的根憑證，與新增至應用程式閘道的受信任根憑證不相符。 請確定您已新增正確的根憑證，以將後端列入允許清單

**原因：** 具有應用程式閘道 v2 的端對端 SSL 需要驗證後端伺服器的憑證，才能將伺服器視為狀況良好。
針對要信任的 TLS/SSL 憑證，後端伺服器憑證必須由受信任存放區中的應用程式閘道所包含的 CA 發行。 如果憑證不是由受信任的 CA 所發行（例如，使用了自我簽署憑證），則使用者應該將簽發者的憑證上傳至應用程式閘道。

已上傳至應用程式閘道 HTTP 設定的憑證必須符合後端伺服器憑證的根憑證。

**解決方案：** 如果您收到此錯誤訊息，表示已上傳至應用程式閘道的憑證與上傳到後端伺服器的憑證不相符。

遵循上述方法中的步驟1-11，將正確的受信任根憑證上傳至應用程式閘道。

如需如何在應用程式閘道中解壓縮和上傳受信任的根憑證的詳細資訊，請參閱[匯出受信任的根憑證（適用于 V2 SKU）](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。
> [!NOTE]
> 如果後端伺服器未交換憑證的完整鏈，包括根 > 中繼（如果適用） > 分葉在 TLS 交握期間，也可能會發生此錯誤。 若要驗證，您可以使用任何用戶端的 OpenSSL 命令，並使用應用程式閘道探查中的已設定設定來連線到後端伺服器。

例如：
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
如果輸出未顯示所傳回憑證的完整鏈，請使用完整的鏈再次匯出憑證，包括根憑證。 在後端伺服器上設定該憑證。 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>後端憑證不正確一般名稱（CN）

**訊息：** 後端憑證的一般名稱（CN）不符合探查的主機標頭。

**原因：** 應用程式閘道檢查後端 HTTP 設定中所指定的主機名稱是否符合後端伺服器的 TLS/SSL 憑證所呈現的 CN。 這是 Standard_v2 並 WAF_v2 SKU 行為。 標準和 WAF SKU 的伺服器名稱指示（SNI）會設定為後端集區位址中的 FQDN。

在 v2 SKU 中，如果有預設探查（未設定和關聯任何自訂探查），會從 HTTP 設定中所述的主機名稱來設定 SNI。 或者，如果 HTTP 設定中提及了「從後端位址挑選主機名稱」，其中後端位址集區包含有效的 FQDN，則會套用此設定。

如果有與 HTTP 設定相關聯的自訂探查，則會從自訂探查設定中所述的主機名稱來設定 SNI。 或者，如果在自訂探查中選取了 [**從後端 HTTP 設定挑選主機名稱**]，則會從 HTTP 設定中所述的主機名稱來設定 SNI。

如果在 HTTP 設定中設定了 [**從後端位址挑選主機名稱**]，則後端位址集區必須包含有效的 FQDN。

如果您收到此錯誤訊息，後端憑證的 CN 不符合自訂探查或 HTTP 設定中設定的主機名稱（如果已選取**後端 HTTP 設定的 [選擇主機名稱**]）。 如果您使用預設探查，主機名稱將會設定為**127.0.0.1**。 如果這不是所需的值，您應該建立自訂探查，並將其與 HTTP 設定產生關聯。

**解決方法：**

若要解決此問題，請遵循下列步驟。

若為 Windows：

1.  登入裝載您應用程式的電腦。

1.  選取 [Win + R] 或以滑鼠右鍵按一下 [**開始**] 按鈕，然後選取 [**執行**]。

1.  輸入**certmgr.msc** ，然後選取 enter。 您也可以在 [**開始**] 功能表上搜尋 [憑證管理員]。

1.  找出憑證（通常在`\Certificates - Current User\\Personal\\Certificates`中），然後開啟憑證。

1.  在 [**詳細資料**] 索引標籤上，檢查憑證**主體**。

1.  從詳細資料確認憑證的 CN，並在自訂探查的 [主機名稱] 欄位或 HTTP 設定中輸入相同的（如果已選取 [**從後端選擇主機名稱] HTTP 設定**）。 如果這不是您的網站所需的主機名稱，您必須取得該網域的憑證，或在自訂探查或 HTTP 設定中輸入正確的主機名稱。

針對使用 OpenSSL 的 Linux：

1.  在 OpenSSL 中執行此命令：
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  從顯示的屬性中，尋找憑證的 CN，並在 HTTP 設定的 [主機名稱] 欄位中輸入相同的值。 如果這不是您的網站所需的主機名稱，您必須取得該網域的憑證，或在自訂探查或 HTTP 設定中輸入正確的主機名稱。

#### <a name="backend-certificate-is-invalid"></a>後端憑證無效

**訊息：** 後端憑證無效。 目前的日期不在憑證\"的有效\"來源\"和有效\"截止日期範圍內。

**原因：** 每個憑證都有一個有效範圍，除非伺服器的 TLS/SSL 憑證有效，HTTPS 連線才會是安全的。 目前的資料必須在**有效的 from**和**valid 到**範圍內。 如果不是，則會將憑證視為無效，而且會產生安全性問題，應用程式閘道將後端伺服器標示為狀況不良。

**解決方案：** 如果您的 TLS/SSL 憑證已過期，請使用您的廠商更新憑證，並使用新的憑證補救伺服器設定。 如果它是自我簽署憑證，您必須產生有效的憑證，並將根憑證上傳至應用程式閘道 HTTP 設定。 若要這樣做，請遵循下列步驟：

1.  在入口網站中開啟您的應用程式閘道 HTTP 設定。

1.  選取具有過期憑證的設定，選取 [新增**憑證**]，然後開啟新的憑證檔案。

1.  使用憑證旁的 [**刪除**] 圖示來移除舊憑證，然後選取 [**儲存**]。

#### <a name="certificate-verification-failed"></a>憑證驗證失敗

**訊息：** 無法驗證後端憑證的有效性。 若要找出原因，請檢查 OpenSSL 診斷是否有與錯誤碼 {errorCode} 相關聯的訊息

**原因：** 當應用程式閘道無法驗證憑證的有效性時，就會發生此錯誤。

**解決方案：** 若要解決此問題，請確認您伺服器上的憑證已正確建立。 例如，您可以使用[OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html)來驗證憑證和其屬性，然後嘗試將憑證 reuploading 至應用程式閘道 HTTP 設定。

<a name="backend-health-status-unknown"></a>後端健全狀況狀態：不明
-------------------------------
如果後端健康情況顯示為 [未知]，入口網站視圖會類似下列螢幕擷取畫面：

![應用程式閘道後端健康情況-未知](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

這種行為可能會因為下列一或多個原因而發生：

1.  應用程式閘道子網上的 NSG 會封鎖從 "Internet" 到埠65503-65534 （v1 SKU）或65200-65535 （v2 SKU）的輸入存取。
1.  應用程式閘道子網上的 UDR 會設定為預設路由（0.0.0.0/0），而下一個躍點則不會指定為「網際網路」。
1.  預設路由是透過 BGP 對虛擬網路的 ExpressRoute/VPN 連線來公告。
1.  在無法解析公用功能變數名稱的虛擬網路上設定自訂 DNS 伺服器。
1.  應用程式閘道處於狀況不良的狀態。

**解決方法：**

1.  檢查您的 NSG 是否封鎖從**網際網路**存取埠65503-65534 （v1 sku）或65200-65535 （v2 sku）：

    a.  在 [應用程式閘道**總覽**] 索引標籤上，選取 [**虛擬網路/子網**] 連結。

    b.  在虛擬網路的 [**子網**] 索引標籤上，選取已部署應用程式閘道的子網。

    c.  檢查是否已設定任何 NSG。

    d.  如果已設定 NSG，請在 [**搜尋**] 索引標籤或 [**所有資源**] 底下搜尋該 NSG 資源。

    e.  在 [**輸入規則**] 區段中，新增輸入規則以允許 v1 sku 或 65200-65535 v2 sku 的目的地埠範圍65503-65534，其**來源**設定為 [**任何**] 或 [**網際網路**]。

    f.  選取 [**儲存**]，並確認您可以將後端視為狀況良好。 或者，您也可以透過[PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)來執行此動作。

1.  檢查您的 UDR 是否有預設路由（0.0.0.0/0），並將下一個躍點設為 [**網際網路**]：
    
    a.  遵循步驟1a 和1b 來判斷您的子網。

    b.  檢查是否已設定任何 UDR。 如果有，請在搜尋列或 [**所有資源**] 底下搜尋資源。

    c.  檢查是否有任何預設路由（0.0.0.0/0）的下一個躍點未設定為**網際網路**。 如果設定為 [**虛擬裝置**] 或 [**虛擬網路閘道**]，您必須確定您的虛擬裝置或內部部署裝置可以將封包正確地路由回網際網路目的地，而不需要修改封包。

    d.  否則，請將下一個躍點變更為 [**網際網路**]，選取 [**儲存**]，然後確認後端健康情況。

1.  透過 BGP 對虛擬網路的 ExpressRoute/VPN 連線所通告的預設路由：

    a.  如果您有透過 BGP 對虛擬網路的 ExpressRoute/VPN 連線，而且您要公告預設路由，則必須確定封包會路由回到網際網路目的地，而不進行修改。 您可以使用應用程式閘道入口網站中的 [**連接疑難排解**] 選項來確認。

    b.  手動選擇目的地，做為任何可從網際網路路由傳送的 IP 位址，例如1.1.1.1。 將 [目的地埠] 設定為 [任何]，並確認連線能力。

    c.  如果下一個躍點是虛擬網路閘道，可能會有透過 ExpressRoute 或 VPN 通告的預設路由。

1.  如果虛擬網路上已設定自訂 DNS 伺服器，請確認伺服器（或伺服器）可以解析公用網域。 在應用程式閘道必須與 OCSP 伺服器之類的外部網域，或檢查憑證的撤銷狀態的情況下，可能會需要公用功能變數名稱解析。

1.  若要確認應用程式閘道狀況良好且正在執行，請移至入口網站中的 [**資源健康狀態**] 選項，並確認狀態為 [**狀況良好**]。 如果您看到**狀況不良**或已**降級**的狀態，請[聯絡支援人員](https://azure.microsoft.com/support/options/)。

<a name="next-steps"></a>後續步驟
----------

深入瞭解[應用程式閘道診斷和記錄](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。
