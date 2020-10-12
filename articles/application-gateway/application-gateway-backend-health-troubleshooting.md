---
title: 針對 Azure 應用程式閘道中的後端健康情況問題進行疑難排解
description: 說明如何針對 Azure 應用程式閘道的後端健康情況問題進行疑難排解
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 531a7fd8547130b4897f3dad0900e1c27fb7fe9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132036"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>針對應用程式閘道中的後端健康情況問題進行疑難排解
==================================================

<a name="overview"></a>概觀
--------

根據預設，Azure 應用程式閘道會探查後端伺服器，以檢查其健全狀態，並檢查其是否已準備好處理要求。 使用者也可以建立自訂探查來提及主機名稱、要探查的路徑，以及要接受為「良好」的狀態碼。 在每個案例中，如果後端伺服器未成功回應，應用程式閘道會將伺服器標示為「狀況不良」，並停止將要求轉送到伺服器。 伺服器開始回應成功之後，應用程式閘道繼續轉送要求。

### <a name="how-to-check-backend-health"></a>如何檢查後端健康情況

若要檢查後端集區的健康情況，您可以使用 Azure 入口網站上的 [後端健康情況] 頁面。 或者，您可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0)、[CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health) 或 [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)。

任何這些方法所擷取的狀態可以是下列其中一項：

- Healthy

- 狀況不良

- Unknown

如果伺服器的後端健全狀態為「良好」，則表示應用程式閘道會將要求轉送到該伺服器。 但是，如果後端集區中所有伺服器的後端健康情況為「狀況不良」或不明，您可能會在嘗試存取應用程式時遇到問題。 本文說明每個所顯示錯誤的徵兆、原因和解決方式。

<a name="backend-health-status-unhealthy"></a>後端健全狀態：狀況不良
-------------------------------

如果後端健全狀態為「狀況不良」，則入口網站檢視會類似下列螢幕擷取畫面：

![應用程式閘道後端健康情況 - 狀況不良](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

或者，如果您使用 Azure PowerShell、CLI 或 Azure REST API 查詢，則會收到類似下列的回應：
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
針對後端集區中的所有伺服器收到「狀況不良」後端伺服器狀態之後，要求就不會轉送到伺服器，而應用程式閘道會將「502 錯誤閘道」錯誤傳回給要求的用戶端。 若要對此問題進行疑難排解，請查看 [後端健康情況] 索引標籤上的 [詳細資料] 資料行。

[詳細資料] 資料行中顯示的訊息會提供有關此問題的詳細見解，而您可根據這些見解，開始針對問題進行疑難排解。

> [!NOTE]
> 預設探查要求會以 \<protocol\> ：//127.0.0.1：/的格式傳送 \<port\> 。 例如， http://127.0.0.1:80 適用於連接埠 80上的 HTTP 探查。 只有 200 到 399 的 HTTP 狀態碼會被視為狀況良好。 通訊協定和目的地連接埠均繼承自 HTTP 設定值。 如果您希望應用程式閘道探查不同的通訊協定、主機名稱或路徑，並將不同的狀態碼辨識為「良好」，請設定自訂探查並使其與 HTTP 設定產生關聯。

<a name="error-messages"></a>錯誤訊息
------------------------
#### <a name="backend-server-timeout"></a>後端伺服器逾時

**訊息：** 後端回應應用程式閘道的健康情況探查所需的時間超過探查設定中的逾時閾值。

**原因：** 應用程式閘道將 HTTP(S) 探查要求傳送至後端伺服器之後，其會等待來自後端伺服器的回應持續一段設定的時間。 如果後端伺服器未在設定的期間 (逾時值) 內回應，則會在重新開始於設定的逾時期間內回應前，系統會將其標示為「狀況不良」。

**解決方案：** 檢查後端伺服器或應用程式未在設定的逾期期間內回應的原因，同時檢查應用程式的相依性。 例如，檢查資料庫是否有任何可能觸發延遲回應的問題。 如果您知道應用程式的行為，而且其應該只在逾時值之後回應，請增加自訂探查設定中的逾時值。 您必須具有自訂探查，才能變更逾時值。 如需如何設定自訂探查的詳細資訊，[請參閱文件頁面](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

若要提高逾時值，請遵循下列步驟：

1.  直接存取後端伺服器，並檢查伺服器在該頁面上回應所花費的時間。 您可以使用任何工具來存取後端伺服器，包括使用開發人員工具的瀏覽器。

1.  在您算出;應用程式回應所花費的時間之後，請選取 [健康情況探查] 索引標籤，然後選取與您的 HTTP 設定相關聯的探查。

1.  輸入大於應用程式回應時間的任何逾時值 (以秒為單位)。

1.  儲存自訂探查設定，並檢查後端健康情況現在是否顯示為「良好」。

#### <a name="dns-resolution-error"></a>DNS 解析錯誤

**訊息：** 應用程式閘道無法建立此後端的探查。 此錯誤通常發生在後端的 FQDN 未正確輸入時。 

**原因：** 如果後端集區的類型為 IP 位址/FQDN 或 App Service，則應用程式閘道會解析為透過網域名稱系統 (DNS) (自訂或 Azure 預設值) 輸入的 FQDN IP 位址，並嘗試連線到 HTTP 設定中所提 TCP 連接埠上的伺服器。 但是，如果顯示此訊息，則表示應用程式閘道無法成功解析所輸入 FQDN 的 IP 位址。

**解決方案：**

1.  確認在後端集區中輸入的 FQDN 是正確的，而且是公用網域，然後嘗試從本機電腦進行解析。

1.  如果您可以解析 IP 位址，則虛擬網路中的 DNS 設定可能會發生問題。

1.  檢查是否已使用自訂 DNS 伺服器設定虛擬網路。 如果是，請檢查 DNS 伺服器為何無法解析為指定 FQDN 的 IP 位址。

1.  如果您使用的是 Azure 預設 DNS，請洽詢您的網域註冊機構，了解是否已完成適當的 A 記錄或 CNAME 記錄對應。

1.  如果是私人或內部網域，請嘗試從相同虛擬網路中的 VM 進行解析。 如果您可加以解析，請重新啟動應用程式閘道，再檢查一次。 若要重新啟動應用程式閘道，您必須使用這些連結資源中所述的 PowerShell 命令進行[停止](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0)和[啟動](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0)。

#### <a name="tcp-connect-error"></a>TCP 連線錯誤

**訊息：** 應用程式閘道無法連線至後端。
請檢查後端是否回應用於探查的連接埠。
同時檢查是否有任何 NSG/UDR/防火牆封鎖此後端的 IP 和連接埠存取

**原因：** 在 DNS 解析階段之後，應用程式閘道會嘗試連線到 HTTP 設定中設定的 TCP 通訊埠上的後端伺服器。 如果應用程式閘道無法在指定的連接埠上建立 TCP 工作階段，此訊息就會將探查標示為「狀況不良」。

**解決方案：** 如果您收到此錯誤，請依照下列步驟執行︰

1.  檢查您是否可以使用瀏覽器或 PowerShell，連線到 HTTP 設定中所提連結埠上的後端伺服器。 例如，執行下列命令：`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  如果提及的連結埠不是所需的連接埠，請輸入正確的連接埠號碼，讓應用程式閘道得以連線到後端伺服器

1.  如果您也無法從本機電腦連線至連接埠，則：

    a.  檢查後端伺服器的網路介面卡和子網路的網路安全性群組 (NSG) 設定，以及是否允許對所設定連接埠的輸入連線。 如果不是，請建立新規則以允許連線。 若要了解如何建立 NSG 規則，[請參閱文件頁面](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)。

    b.  檢查應用程式閘道子網路的 NSG 設定是否允許輸出公用和私人流量，以便進行連線。 檢查步驟 3a 中提供的文件頁面，深入了解如何建立 NSG 規則。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  檢查應用程式閘道的使用者定義路由 (UDR) 設定和後端伺服器的子網路是否有任何路由異常。 確定 UDR 不會從後端子網路將流量引導走。 例如，檢查網路虛擬設備的路由，或透過 Azure ExpressRoute 和/或 VPN 向應用程式閘道子網路公告的預設路由。

    d.  若要檢查網路介面卡的有效路由和規則，您可使用下列 PowerShell 命令：
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  如果您未找到任何 NSG 或 UDR 相關問題，請檢查您的後端伺服器是否有應用程式相關問題，使用戶端無法在設定的連接埠上建立 TCP 工作階段。 請檢查下列事項：

    a.  開啟命令提示字元 (Win+R -\> cmd)，輸入 `netstat`，然後選取 Enter。

    b.  檢查伺服器是否正在設定的連接埠上進行接聽。 例如：
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  如果其並未在設定的連接埠上接聽，請檢查您的 Web 伺服器設定。 例如：IIS 中的網站繫結、NGINX 中的伺服器區塊，以及 Apache 中的虛擬主機。

    d.  請檢查 OS 防火牆設定，確定允許連接埠的連入流量。

#### <a name="http-status-code-mismatch"></a>HTTP 狀態碼不符

**訊息：** 後端 HTTP 回應的狀態碼與探查設定不相符。 預期：{HTTPStatusCode0} 收到：{HTTPStatusCode1}。

**原因：** 建立 TCP 連線並完成 TLS 交握後 (如果已啟用 TLS)，應用程式閘道會將探查當作 HTTP GET 要求傳送至後端伺服器。 如先前所述，預設探查將會是 \<protocol\> ：//127.0.0.1： \<port\> /，且會將開始風行200至399中的回應狀態碼視為狀況良好。 如果伺服器傳回任何其他狀態碼，此訊息就會將其視為「狀況不良」。

**解決方案：** 視後端伺服器的回應碼而定，您可以採取下列步驟。 以下列出一些常見的狀態碼：

| **錯誤** | **動作** |
| --- | --- |
| 探查狀態碼不相符：收到 401 | 檢查後端伺服器是否需要驗證。 應用程式閘道探查無法通過認證來進行驗證。 在探查狀態碼相符中允許 \"HTTP 401\"，或探查至伺服器不需要驗證的路徑。 | |
| 探查狀態碼不相符：收到 403 | 禁止存取。 檢查後端伺服器上是否允許存取路徑。 | |
| 探查狀態碼不相符：收到 404 | 找不到頁面。 檢查後端伺服器上的主機名稱路徑是否可存取。 將主機名稱或路徑參數變更為可存取的值。 | |
| 探查狀態碼不相符：收到 405 | 應用程式閘道的探查要求會使用 HTTP GET 方法。 檢查您的伺服器是否允許此方法。 | |
| 探查狀態碼不相符：收到 500 | 內部伺服器錯誤。 檢查後端伺服器的健康情況，以及服務是否正在執行中。 | |
| 探查狀態碼不相符：收到 503 | 服務無法使用。 檢查後端伺服器的健康情況，以及服務是否正在執行中。 | |

或者，如果您認為回應是合法的，而且希望應用程式閘道接受其他狀態碼屬於「良好」狀態，即可建立自訂探查。 在後端網站需要驗證的情況下，這個方法很有用。 由於探查要求不會攜帶任何使用者認證，因此將會失敗，且後端伺服器將傳回 HTTP 401 狀態碼。

若要建立自訂探查，請依照[這些步驟](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)操作。

#### <a name="http-response-body-mismatch"></a>HTTP 回應本文不相符

**訊息：** 後端的 HTTP 回應本文與探查設定不相符。 收到的回應本文不包含 {string}。

**原因：** 當您建立自訂探查時，您可以透過比對回應本文中的字串，將後端伺服器標示為「良好」。 例如，您可以將應用程式閘道設定為接受「未經授權」作為要比對的字串。 如果探查要求的後端伺服器回應包含字串**未經授權**，則會將其標示為「良好」。 否則，此訊息會將其視為「狀況不良」。

**解決方案：** 若要解決此問題，請依照下列步驟執行︰

1.  在本機或從探查路徑上的用戶端電腦存取後端伺服器，並檢查回應本文。

1.  確認應用程式閘道自訂探查設定中的回應本文符合已設定的內容。

1.  如果兩者不相符，請變更探查設定，使其具有可接受的正確字串值。

深入了解[應用程式閘道探查比對](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)。

>[!NOTE]
> 對於所有 TLS 相關錯誤訊息，若要深入了解 SNI 行為和 v1 與 v2 SKU 之間的差異，請參閱 [TLS 概觀](ssl-overview.md)頁面。


#### <a name="backend-server-certificate-invalid-ca"></a>後端伺服器憑證 CA 無效

**訊息：** 後端所使用的伺服器憑證不是由知名的憑證授權單位單位 (CA) 所簽署。 上傳後端所使用伺服器憑證的根憑證，以允許應用程式閘道上的後端。

**原因：** 進行應用程式閘道 v2 的端對端 SSL 時，必須先驗證後端伺服器的憑證，才能將伺服器視為「良好」。
若要讓 TLS/SSL 憑證受到信任，後端伺服器的該憑證必須由應用程式閘道的信任存放區中所包含的 CA 發行。 如果憑證不是由信任的 CA 所發行 (例如，若使用自我簽署憑證)，則使用者應將簽發者的憑證上傳至應用程式閘道。

**解決方案：** 請遵循下列步驟，將受信任的根憑證匯出並上傳至應用程式閘道。 (這些步驟適用於 Windows 用戶端。)

1.  登入裝載您應用程式的電腦。

1.  選取 Win+R 或以滑鼠右鍵按一下 [開始] 按鈕，然後選取 [執行]。

1.  輸入 `certmgr.msc` 並選取 Enter。 您也可以在 [開始] 功能表上搜尋 [憑證管理員]。

1.  找出憑證 (通常位於 `\Certificates - Current User\\Personal\\Certificates\` 中)，然後加以開啟。

1.  選取根憑證，然後選取 [檢視憑證]。

1.  在 [憑證] 屬性中，選取 [詳細資料] 索引標籤。

1.  在 [詳細資料] 索引標籤上，選取 [複製到檔案] 選項，然後將檔案儲存在以 Base-64 編碼的 x.509 (.CER) 格式。

1.  在 Azure 入口網站中開啟應用程式閘道 HTTP [設定] 頁面。

1. 開啟 HTTP 設定，選取 [新增憑證]，然後找出您剛才儲存的憑證檔案。

1. 選取 [儲存]  以儲存 HTTP 設定。

或者，您也可以透過瀏覽器直接存取伺服器 (略過應用程式閘道) 並從瀏覽器匯出根憑證，進而從用戶端電腦匯出根憑證。

如需如何在應用程式閘道中擷取和上傳受信任根憑證的詳細資訊，請參閱[匯出受信任的根憑證 (適用於 v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。

#### <a name="trusted-root-certificate-mismatch"></a>受信任的根憑證不相符

**訊息：** 後端所使用伺服器憑證的根憑證不符合新增至應用程式閘道的受信任根憑證。 確保您已新增正確的根憑證，以將後端列入白名單

**原因：** 進行應用程式閘道 v2 的端對端 SSL 時，必須先驗證後端伺服器的憑證，才能將伺服器視為「良好」。
若要讓 TLS/SSL 憑證受到信任，後端伺服器憑證必須由應用程式閘道的信任存放區中所包含的 CA 發行。 如果憑證不是由信任的 CA 所發行 (例如，使用自我簽署憑證)，則使用者應將簽發者的憑證上傳至應用程式閘道。

已上傳至應用程式閘道 HTTP 設定的憑證必須符合後端伺服器憑證的根憑證。

**解決方案：** 如果您收到此錯誤訊息，表示已上傳至應用程式閘道的憑證與上傳到後端伺服器的憑證不相符。

遵循上述方法中的步驟 1-11，將正確的受信任根憑證上傳至應用程式閘道。

如需如何在應用程式閘道中擷取和上傳受信任根憑證的詳細資訊，請參閱[匯出受信任的根憑證 (適用於 v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。
> [!NOTE]
> 如果後端伺服器未在 TLS 交握期間交換憑證的完整鏈結 (包括根 > 中繼 (如果適用) > 分葉)，也可能發生此錯誤。 若要驗證，您可以使用任何用戶端的 OpenSSL 命令，並使用應用程式閘道探查中設好的設定來連線到後端伺服器。

例如：
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
如果輸出未顯示所傳回憑證的完整鏈結，請再次匯出包含完整鏈結的憑證，包括根憑證。 在後端伺服器上設定該憑證。 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>後端憑證通用名稱 (CN) 無效

**訊息：** 後端憑證的通用名稱 (CN) 與探查的主機標題不相符。

**原因：** 應用程式閘道會檢查後端 HTTP 設定中指定的主機名稱是否符合後端伺服器的 TLS/SSL 憑證所出示的 CN。 這是 Standard_v2 和 WAF_v2 SKU (V2) 行為。 標準和 WAF SKU 的 (v1) 伺服器名稱指示 (SNI) 會設定為後端集區位址中的 FQDN。 如需 SNI 行為和 v1 與 v2 SKU 之間差異的詳細資訊，請參閱 [應用程式閘道的 TLS 終止和端對端 TLS 概觀](ssl-overview.md)。

在 v2 SKU 中，如果有預設探查 (尚未設定和關聯任何自訂探查)，則會從 HTTP 設定中提及的主機名稱設定 SNI。 或者，如果 HTTP 設定中提及了「從後端位址挑選主機名稱」，其中後端位址集區包含有效的 FQDN，則會套用此設定。

如果有與 HTTP 設定相關聯的自訂探查，則會從自訂探查設定中提及的主機名稱設定 SNI。 或者，如果在自訂探查中選取了 [從後端 HTTP 設定挑選主機名稱]，則會從 HTTP 設定中提及的主機名稱設定 SNI。

如果 [從後端位址挑選主機名稱] 已設於 HTTP 設定中，則後端位址集區必須包含有效的 FQDN。

如果您收到此錯誤訊息，這表示後端憑證的 CN 不符合自訂探查或 HTTP 設定中設定的主機名稱 (如果選取了 [從後端 HTTP 設定挑選主機名稱])。 如果您使用預設探查，主機名稱將會設定為 **127.0.0.1**。 如果這不是所需的值，則您應建立自訂探查，並將其與 HTTP 設定產生關聯。

**解決方案：**

若要解決此問題，請遵循下列步驟。

若為 Windows：

1.  登入裝載您應用程式的電腦。

1.  選取 Win+R 或以滑鼠右鍵按一下 [開始] 按鈕，然後選取 [執行]。

1.  輸入 **certmgr.msc** 並選取 Enter。 您也可以在 [開始] 功能表上搜尋 [憑證管理員]。

1.  找出憑證 (通常位於 `\Certificates - Current User\\Personal\\Certificates` 中)，然後開啟憑證。

1.  在 [詳細資料] 索引標籤上，檢查憑證 [主旨]。

1.  從詳細資料確認憑證的 CN，並在自訂探查的主機名稱欄位或在 HTTP 設定中輸入相同的值 (如果選取了 [從後端 HTTP 設定挑選主機名稱])。 如果這不是您的網站所需的主機名稱，您必須取得該網域的憑證，或在自訂探查或 HTTP 設定組態中輸入正確的主機名稱。

對於使用 OpenSSL 的 Linux：

1.  在 OpenSSL 中執行此命令：
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  從顯示的屬性中，尋找憑證的 CN，並在 HTTP 設定的主機名稱欄位中輸入相同的值。 如果這不是您的網站所需的主機名稱，您必須取得該網域的憑證，或在自訂探查或 HTTP 設定組態中輸入正確的主機名稱。

#### <a name="backend-certificate-is-invalid"></a>後端憑證無效

**訊息：** 後端憑證無效。 目前的日期不在憑證的「開始有效期限」和「結束有效期限」日期範圍內。

**原因：** 每個憑證都有一個有效範圍，除非伺服器的 TLS/SSL 憑證有效，否則 HTTPS 連線不會是安全的。 目前的資料必須位於「開始有效期限」和「結束有效期限」範圍內。 若非如此，則會將憑證視為無效，因而造成安全性問題，以致應用程式閘道將後端伺服器標示為「狀況不良」。

**解決方案：** 如果您的 TLS/SSL 憑證已過期，請向您的廠商更新憑證，並使用新的憑證來更新伺服器設定。 如果這是自我簽署憑證，您必須產生有效的憑證，並將根憑證上傳至應用程式閘道 HTTP 設定。 若要這樣做，請遵循下列步驟：

1.  在入口網站中開啟您的應用程式閘道 HTTP 設定。

1.  選取具有過期憑證的設定，選取 [新增憑證]，然後開啟新的憑證檔案。

1.  使用憑證旁的 [刪除] 圖示來移除舊的憑證，然後選取 [儲存]。

#### <a name="certificate-verification-failed"></a>憑證驗證失敗

**訊息：** 無法驗證後端憑證的有效性。 若要找出原因，請檢查 OpenSSL 診斷是否有與錯誤碼 {errorCode} 相關聯的訊息

**原因：** 當應用程式閘道無法驗證憑證的有效性時，就會發生此錯誤。

**解決方案：** 若要解決此問題，請驗證您伺服器上的憑證是否已正確建立。 例如，您可以使用 [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) 來驗證憑證及其屬性，然後嘗試將憑證重新上傳至應用程式閘道的 HTTP 設定。

<a name="backend-health-status-unknown"></a>後端健全狀態：未知
-------------------------------
如果後端健康情況顯示為「未知」，則入口網站檢視會類似下列螢幕擷取畫面：

![應用程式閘道後端健康情況 - 未知](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

下列一個或多個原因會發生此行為：

1.  應用程式閘道子網路上的 NSG 會封鎖從「網際網路」對連接埠 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU) 的輸入存取。
1.  應用程式閘道子網路上的 UDR 會設定為預設路由 (0.0.0.0/0)，而下一個躍點不會指定為「網際網路」。
1.  預設路由是由透過 BGP 對虛擬網路進行的 ExpressRoute/VPN 連線來公告。
1.  自訂 DNS 伺服器會設定於無法解析公用網域名稱的虛擬網路上。
1.  應用程式閘道處於「狀況不良」狀態。

**解決方案：**

1.  檢查您的 NSG 是否封鎖從 [網際網路] 對連接埠 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU) 的存取：

    a.  在應用程式閘道的 [概觀] 索引標籤上，選取 [虛擬網路/子網路] 連結。

    b.  在虛擬網路的 [子網路] 索引標籤上，選取已部署應用程式閘道的子網路。

    c.  檢查是否已設定任何 NSG。

    d.  如果已設定 NSG，請在 [搜尋] 索引標籤上或在 [所有資源] 底下搜尋該 NSG 資源。

    e.  在 [輸入規則] 區段中，新增輸入規則以允許 [來源] 設為 [任何] 或 [網際網路]的目的地連接埠範圍 65503-65534 (適用於 v1 SKU) 或 65200-65535 (適用於 v2 SKU)。

    f.  選取 [儲存]，並確認您可以將後端視為「良好」。 或者，您也可以透過 [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) 來執行此作業。

1.  檢查您的 UDR 是否有下一個躍點未設定為 [網際網路] 的預設路由 (0.0.0.0/0)：
    
    a.  遵循步驟 1a 和 1b 來決定您的子網路。

    b.  檢查是否已設定任何 UDR。 如果有，請在搜尋列上或在 [所有資源] 之下搜尋資源。

    c.  檢查是否有下一個躍點未設定為 [網際網路] 的預設路由 (0.0.0.0/0)。 如果設定為 [虛擬設備] 或 [虛擬網路閘道]，您必須確定虛擬設備或內部部署裝置可以將封包正確地路由傳送回到網際網路目的地，而不需修改封包。

    d.  否則，將下一個躍點變更為 [網際網路]，選取 [儲存]，然後確認後端健康情況。

1.  預設路由是由透過 BGP 對虛擬網路進行的 ExpressRoute/VPN 連線來公告：

    a.  如果您有透過 BGP 對虛擬網路的 ExpressRoute/VPN 連線，而且您要公告預設路由，則必須確定封包會路由傳送回到網際網路目的地，而不需進行修改。 您可使用應用程式閘道入口網站中的 [連線疑難排解] 選項來進行確認。

    b.  手動將目的地選擇為任何可從網際網路路由傳送的 IP 位址，例如 1.1.1.1。 將目的地連接埠設定為任何連接埠，並確認連線能力。

    c.  如果下一個躍點是虛擬網路閘道，則可能會有透過 ExpressRoute 或 VPN 公告的預設路由。

1.  如果虛擬網路上已設定自訂 DNS 伺服器，請確認該伺服器可解析公用網域。 在應用程式閘道必須可觸達 OCSP 伺服器之類的外部網域，或在檢查憑證撤銷狀態的情況下，可能需要解析公用網域名稱。

1.  若要確認應用程式閘道狀況良好且正在執行，請移至入口網站中的 [資源健康情況] 選項，並確認狀態為 [良好]。 如果您看到 [狀況不良] 或 [已降級] 狀態，[請連絡支援部門](https://azure.microsoft.com/support/options/)。

<a name="next-steps"></a>後續步驟
----------

深入了解[應用程式閘道診斷和記錄](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。
