---
title: 使用現有的內部部署 proxy 伺服器和 Azure Active Directory
description: 涵蓋如何使用 Azure Active Directory 來使用現有的內部部署 proxy 伺服器。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2311b905aeeaacb6c445f441d5268d06a150de64
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107635"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>使用現有的內部部署 Proxy 伺服器

本文說明如何設定 Azure Active Directory (Azure AD) 應用程式 Proxy 連接器以使用輸出 Proxy 伺服器。 它適用於具有現有 Proxy 之網路環境的客戶。

我們先來看看這些主要部署案例︰

* 設定連接器以略過您的內部部署輸出 Proxy。
* 設定要使用輸出 Proxy 來存取 Azure AD 應用程式 Proxy 的連接器。
* 使用連接器和後端應用程式之間的 Proxy 進行設定。

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

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>步驟 1:設定連接器和相關服務以通過輸出 Proxy

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

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>步驟 2:設定 Proxy 以允許來自連接器與相關服務的流量通過

在輸出 Proxy 有 4 個層面需要考量：

* Proxy 輸出規則
* Proxy 驗證
* Proxy 連接埠
* TLS 檢查

#### <a name="proxy-outbound-rules"></a>Proxy 輸出規則

允許存取下列 URL：

| URL | 使用方式 |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | 連接器和應用程式 Proxy 雲端服務之間的通訊 |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | 連接器會使用這些 URL 來驗證憑證 |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*.microsoftonline.com<br>* .microsoftonline-p.com<br>*.msauth.net<br>* .msauthimages.net<br>*.msecnd.net<br>* .msftauth.net<br>*.msftauthimages.net<br>* .phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com:80 | 連接器會在註冊程序進行期間使用這些 URL。 |

如果防火牆或 Proxy 可供設定 DNS 允許清單，則可允許連往 \*.msappproxy.net 和 \*.servicebus.windows.net 的連線。

如果您不允許 FQDN 連線且需要改為指定 IP 範圍，請使用下列選項：

* 允許連接器輸出存取所有目的地。
* 允許連接器輸出存取全部的 Azure 資料中心 IP 範圍。 Azure 資料中心 IP 範圍清單在使用上的麻煩在於此清單是每週更新。 您必須制定程序，以確保存取規則會跟著更新。 僅使用 IP 位址的子集可能會導致您的組態中斷。 若要下載最新的 Azure 資料中心 IP 範圍，請流覽至「 [https://download.microsoft.com](https://download.microsoft.com) AZURE IP 範圍和服務標籤」並進行搜尋。 請務必選取相關的雲端。 例如，您可以使用「Azure IP 範圍和服務標籤–公用雲端」來找到公用雲端 IP 範圍。 您可以藉由搜尋「Azure IP 範圍和服務標籤– US 政府雲端」來找到 US 政府雲端。

#### <a name="proxy-authentication"></a>Proxy 驗證

目前不支援 Proxy 驗證。 目前的建議是允許連接器匿名存取網際網路目的地。

#### <a name="proxy-ports"></a>Proxy 連接埠

連接器可使用 CONNECT 方法進行輸出 TLS 型連線。 此方法基本上會透過輸出 Proxy 設定通道。 設定 Proxy 伺服器以允許連接埠 443 和 80 通道。

> [!NOTE]
> 當服務匯流排在 HTTPS 上執行時，它會使用連接埠 443。 不過，根據預設，服務匯流排會嘗試導向 TCP 連線，且僅在直接連線失敗時，才會改為使用 HTTPS。

#### <a name="tls-inspection"></a>TLS 檢查

請勿對連接器流量使用 TLS 檢查，因為這樣會導致連接器流量發生問題。 連接器會使用憑證來向應用程式 Proxy 服務進行驗證，該憑證可能會在 TLS 檢查期間遺失。

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>使用連接器和後端應用程式之間的 Proxy 進行設定
針對與後端應用程式的通訊使用正向 Proxy，可能是某些環境中的特殊需求。
若要啟用此動作，請遵循後續步驟：

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>步驟 1:將所需登錄值新增至伺服器
1. 若要啟用使用預設 Proxy，請將下列登錄值 (DWORD) `UseDefaultProxyForBackendRequests = 1` 新增至位於 "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector" 中的連接器設定登錄機碼。

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>步驟 2:使用 netsh 命令手動設定 Proxy 伺服器
1.  啟用群組原則「為每部電腦建立 Proxy 設定」。 這可在下列位置找到︰電腦設定\原則\系統管理範本\windows 元件\Internet Explorer。 這需要設定，而不是將此原則設為每位使用者。
2.  在伺服器上執行 `gpupdate /force`，或重新啟動伺服器，以確保其使用更新的群組原則設定。
3.  以系統管理員權限啟動提升權限的命令提示字元，然後輸入 `control inetcpl.cpl`。
4.  設定必要的 Proxy 設定。 

這些設定會讓連接器使用相同的正向 Proxy 來與 Azure 和後端應用程式進行通訊。 如果 Azure 通訊的連接器不需要正向 Proxy，或需要其他正向 Proxy，則可修改檔案 ApplicationProxyConnectorService.exe.config 來進行設定，如＜略過輸出 Proxy＞或＜使用輸出 Proxy 伺服器＞等章節中所述。

> [!NOTE]
> 在作業系統中設定網際網路 proxy 的方式有很多種。 透過 NETSH WINHTTP 設定的 proxy 設定 (執行 `NETSH WINHTTP SHOW PROXY` ，以確認) 覆寫您在步驟2中設定的 proxy 設定。 

連接器更新程式服務也會使用電腦 Proxy。 您可藉由修改檔案 ApplicationProxyConnectorUpdaterService.exe.config 來變更此行為。

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>針對連接器 Proxy 問題和服務連線問題進行疑難排解

現在您應該會看到所有的流量通過 Proxy。 如果您有問題，下列疑難排解資訊應該會有幫助。

找出連接器連線問題並進行疑難排解的最佳方式，是在啟動連接器服務時進行網路擷取。 以下是擷取和篩選網路追蹤的一些快速秘訣。

您可以使用您選擇的監視工具。 基於本文的目的，我們使用 Microsoft Message Analyzer。

下列範例針對的是 Message Analyzer，但是這些原則可以套用到任何分析工具。

### <a name="take-a-capture-of-connector-traffic"></a>擷取連接器流量

如需初始的疑難排解，執行下列步驟︰

1. 從 services.msc 停止 Azure AD 應用程式 Proxy 連接器服務。

   ![services.msc 中的 Azure AD 應用程式 Proxy 連接器服務](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. 以系統管理員身分執行 Message Analyzer。
1. 選取 [啟動本機追蹤]。
1. 啟動 Azure AD 應用程式 Proxy 連接器服務。
1. 停止網路擷取。

   ![螢幕擷取畫面顯示 [停止網路擷取] 按鈕](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

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
* 如果連接器連線有問題，請在 [Azure Active Directory 的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-active-directory.html)中提出問題，或向支援小組建立票證。
