---
title: 使用現有的內部部署 Proxy 伺服器與 Azure AD | Microsoft Docs
description: 涵蓋如何使用現有的內部部署 Proxy 伺服器。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d7c7d9f6d59ffd57ddb14f7c060d0a3f6f2a6eb
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967744"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>使用現有的內部部署 Proxy 伺服器

本文說明如何設定 Azure Active Directory (Azure AD) 應用程式 Proxy 連接器以使用輸出 Proxy 伺服器。 它適用於具有現有 Proxy 之網路環境的客戶。

我們先來看看這些主要部署案例︰

* 設定連接器以略過您的內部部署輸出 Proxy。
* 設定要使用輸出 Proxy 來存取 Azure AD 應用程式 Proxy 的連接器。

如需連接器運作方式的詳細資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)。

## <a name="bypass-outbound-proxies"></a>略過輸出 Proxy

連接器有會發出輸出要求的基礎作業系統元件。 這些元件會使用 Web Proxy 自動探索 (WPAD)，自動嘗試在網路上找出 Proxy 伺服器。

OS 元件會嘗試藉由對 wpad.domainsuffix 執行 DNS 查閱來尋找 Proxy 伺服器。 如果查閱以 DNS 進行解晰，則會對 wpad.dat 的 IP 位址提出 HTTP 要求。 此要求會成為您環境中的 Proxy 組態指令碼。 連接器會使用這個指令碼來選取輸出 Proxy 伺服器。 不過，連接器流量可能會因為 Proxy 上所需的其他組態設定而仍然不會通過。

您可以將連接器設定為略過內部部署 Proxy，以確保它會使用與 Azure 服務的直接連線。 我們之所以建議這種方式 (只要您的網路原則允許)，是因為這表示您有一個要維護的較少組態。

若要停用連接器的輸出 Proxy 使用，請編輯 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 檔案，並新增此程式碼範例中所示的 system.net 區段︰

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

若要確保連接器更新程式服務也會略過 Proxy，對 ApplicationProxyConnectorUpdaterService.exe.config 檔案進行類似的變更。 此檔案位於 C:\Program Files\Microsoft AAD App Proxy Connector Updater。

請務必複製原始檔案，以免您需要還原為預設 .config 檔案。

## <a name="use-the-outbound-proxy-server"></a>使用輸出 Proxy 伺服器

某些環境會要求所有輸出流量通過輸出 Proxy，無一例外。 如此一來，略過 Proxy 不是選項。

您可以設定連接器流量通過輸出 Proxy，如下圖所示：

 ![設定讓連接器流量通過輸出 Proxy 來到達 Azure AD 應用程式 Proxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

由於僅有輸出流量，因此您不需要設定透過防火牆來進行的輸入存取。

> [!NOTE]
> 應用程式 Proxy 不支援其他 Proxy 的驗證。 連接器/更新程式網路服務帳戶應該能夠連線到 Proxy，而不需進行驗證。

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>步驟 1：設定連接器和相關服務以通過輸出 Proxy

若 WPAD 在環境中啟用並正確地設定，連接器會自動探索輸出 Proxy 伺服器，並嘗試使用它。 不過，您可以明確地設定連接器以通過輸出 Proxy。

為此，請編輯 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 檔案，並新增此範例程式碼中顯示的 *system.net* 區段。 變更 proxyserver:8080 以反映您的本機 Proxy 伺服器名稱或 IP 位址與其正在接聽的連接埠。 即使您使用的是 IP 位址，該值也必須具有前置詞 http://。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

接著，設定連接器更新程式服務來使用 Proxy，方法為對 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config 檔案進行類似變更。

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>步驟 2︰設定 Proxy 以允許來自連接器與相關服務的流量通過

在輸出 Proxy 有 4 個層面需要考量：

* Proxy 輸出規則
* Proxy 驗證
* Proxy 連接埠
* SSL 審查

#### <a name="proxy-outbound-rules"></a>Proxy 輸出規則

允許存取下列 URL：

| URL | 使用方式 |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | 連接器和應用程式 Proxy 雲端服務之間的通訊 |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | 連接器會使用這些 Url 來驗證憑證 |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com<br>* . microsoftonline-p.com<br>*. msauth.net<br>* . msauthimages.net<br>*. msecnd.net<br>* . msftauth.net<br>*. msftauthimages.net<br>* . phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | 連接器會在註冊程序進行期間使用這些 URL。 |

如果您的防火牆或 proxy 允許您設定 DNS 允許清單，您可以允許連接到 \*msappproxy.net 和 \*. servicebus.windows.net。 若非如此，您需要允許存取 [Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 IP 範圍會每週更新。

如果您不允許 FQDN 連線且需要改為指定 IP 範圍，請使用下列選項：

* 允許連接器輸出存取所有目的地。
* 允許連接器輸出存取全部的 [Azure 資料中心 IP 範圍](https://www.microsoft.com//download/details.aspx?id=41653)。 Azure 資料中心 IP 範圍清單在使用上的麻煩在於此清單是每週更新。 您必須制定程序，以確保存取規則會跟著更新。 僅使用 IP 位址的子集可能會導致您的組態中斷。

#### <a name="proxy-authentication"></a>Proxy 驗證

目前不支援 Proxy 驗證。 目前的建議是允許連接器匿名存取網際網路目的地。

#### <a name="proxy-ports"></a>Proxy 連接埠

連接器可使用 CONNECT 方法進行輸出 SSL 型連線。 此方法基本上會透過輸出 Proxy 設定通道。 設定 Proxy 伺服器以允許連接埠 443 和 80 通道。

> [!NOTE]
> 當服務匯流排在 HTTPS 上執行時，它會使用連接埠 443。 不過，根據預設，服務匯流排會嘗試導向 TCP 連線，且僅在直接連線失敗時，才會改為使用 HTTPS。

#### <a name="ssl-inspection"></a>SSL 審查

請勿對連接器流量使用 SSL 審查，因為這樣會導致連接器流量發生問題。 連接器會使用憑證來驗證應用程式 Proxy 服務，而該憑證可能會在 SSL 審查期間會遺失。

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>針對連接器 Proxy 問題和服務連線問題進行疑難排解

現在您應該會看到所有的流量通過 Proxy。 如果您有問題，下列疑難排解資訊應該會有幫助。

找出連接器連線問題並進行疑難排解的最佳方式，是在啟動連接器服務時進行網路擷取。 以下是擷取和篩選網路追蹤的一些快速秘訣。

您可以使用您選擇的監視工具。 基於本文的目的，我們使用 Microsoft Message Analyzer。 您可以[從 Microsoft 下載](https://www.microsoft.com/download/details.aspx?id=44226)。

下列範例針對的是 Message Analyzer，但是這些原則可以套用到任何分析工具。

### <a name="take-a-capture-of-connector-traffic"></a>擷取連接器流量

如需初始的疑難排解，執行下列步驟︰

1. 從 services.msc 停止 Azure AD 應用程式 Proxy 連接器服務。

   ![services.msc 中的 Azure AD 應用程式 Proxy 連接器服務](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. 以系統管理員身分執行 Message Analyzer。
1. 選取 [啟動本機追蹤]。
1. 啟動 Azure AD 應用程式 Proxy 連接器服務。
1. 停止網路擷取。

   ![螢幕擷取畫面顯示 [停止網路捕捉] 按鈕](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>檢查連接器流量是否略過輸出 Proxy

如果您已設定應用程式 Proxy 連接器略過 Proxy 伺服器，並直接連接到應用程式 Proxy 服務，請嘗試查看網路擷取，看看是否有失敗的 TCP 連線。

請使用 Message Analyzer 篩選條件來識別這些嘗試。 在篩選方塊中輸入 `property.TCPSynRetransmit`，並選取 [套用]。

SYN 封包是傳送到建立 TCP 連線的第一個封包。 如果此封包未傳回回應，則會重新嘗試 SYN。 您可以使用上述篩選條件來查看任何重新傳輸的 SYN。 然後，您可以檢查這些 SYN 是否對應至任何與連接器相關的流量。

如果您希望該連接器直接連線至 Azure 服務，則連接埠 443 的 SynRetransmit 回應表示您有網路或防火牆問題。

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>檢查連接器流量是否使用輸出 Proxy

如果您已設定讓應用程式 Proxy 連接器流量通過 Proxy 伺服器，請尋找至 Proxy 的失敗 https 連線。

若要篩選這些連接嘗試的網路擷取，請在 Message Analyzer 篩選條件中輸入 `(https.Request or https.Response) and tcp.port==8080`，將 8080 取代為您的 Proxy 服務連接埠。 選取 [套用] 查看篩選結果。

上述篩選條件只會顯示 HTTPs 要求和往返 Proxy 連接埠的回應。 您正在尋找顯示與 Proxy 伺服器通訊的 CONNECT 要求。 成功時，您會收到 HTTP OK (200) 回應。

如果您看到其他回應碼，例如 407 或 502，這表示 Proxy 需要驗證或由於某些其他原因不允許流量。 此時您會與您的 Proxy 伺服器支援團隊合作。

## <a name="next-steps"></a>後續步驟

* [了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)
* 如果連接器連線有問題，請在 [Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD)中提出問題，或向支援小組建立票證。
