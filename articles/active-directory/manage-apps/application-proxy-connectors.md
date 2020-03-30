---
title: 了解 Azure AD 應用程式 Proxy 連接器 | Microsoft Docs
description: 涵蓋 Azure AD 應用程式 Proxy 連接器的基本概念。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: b097ce3781a77a8c5e8a94b9c2bf0977f3efcfd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481325"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>了解 Azure AD 應用程式 Proxy 連接器

連接器是讓 Azure AD 應用程式 Proxy 運作的關鍵。 它們很簡單、容易部署及維護且超級強大。 本文討論什麼是連接器、其運作方式，以及如何最佳化部署的一些建議。

## <a name="what-is-an-application-proxy-connector"></a>什麼是應用程式 Proxy 連接器？

連接器是輕量級代理程式，位於內部部署並可推動應用程式 Proxy 服務的輸出連線。 連接器必須安裝在可存取後端應用程式的 Windows 伺服器上。 您可以在連接器群組內將連接器加以組織，每個群組處理特定應用程式的流量。

## <a name="requirements-and-deployment"></a>需求和部署

若要成功部署應用程式 Proxy，您至少需要一個連接器，但我們建議兩個以上可獲得較佳的復原功能。 在運行 Windows Server 2012 R2 或更高版本的電腦上安裝連接器。 連接器需與應用程式 Proxy 服務以及您發佈的內部部署應用程式進行通訊。

### <a name="windows-server"></a>Windows 伺服器
您需要執行 Windows Server 2012 R2 或更新版本的伺服器，您可以在該伺服器上安裝「應用程式 Proxy」連接器。 伺服器需要連線至 Azure 中的「應用程式 Proxy」服務，以及您所發佈的內部部署應用程式。

您安裝「應用程式 Proxy」連接器之前，Windows 伺服器需要先啟用 TLS 1.2。 要在伺服器上啟用 TLS 1.2：

1. 設定下列登錄機碼：
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. 重新啟動伺服器

如需連接器伺服器之網路需求的詳細資訊，請參閱[開始使用應用程式 Proxy 並安裝連接器](application-proxy-add-on-premises-application.md)。

## <a name="maintenance"></a>維護

連接器和服務會負責所有高可用性的工作。 它們可以動態新增或移除。 每當新要求抵達時，它會路由傳送至其中一個目前可用的連接器。 如果連接器暫時無法使用，則不會回應此流量。

連接器是無狀態的，且沒有任何電腦上的設定資料。 它們所儲存的唯一資料是用於連線服務和其驗證憑證的設定。 當它們連線至服務時，會提取所有必要的組態資料，且每隔幾分鐘會重新整理。

連接器也會輪詢伺服器，以尋找是否有較新版的連接器。 如果找到，連接器會自行更新。

您可以使用事件記錄和效能計數器，從執行連接器的電腦監視您的連接器。 或者，您可以從 Azure 入口網站的應用程式 Proxy 頁面檢視其狀態：

![示例：Azure AD 應用程式代理連接器](./media/application-proxy-connectors/app-proxy-connectors.png)

您不必手動刪除未使用的連接器。 當連接器執行時，它在連接到服務時會保持作用中。 未使用的連接器會標記為_非作用中_，且將在未作用 10 天之後移除。 不過，如果您需要將連接器解除安裝，請從伺服器將連接器服務和更新程式服務解除安裝。 重新啟動電腦，才能完全移除此服務。

## <a name="automatic-updates"></a>自動更新

Azure AD 會提供您部署之所有連接器的自動更新。 只要應用程式 Proxy 連接器更新程式服務正在執行，您的連接器便會自動更新。 如果您在伺服器上沒有看到連接器更新程式服務，則需要[重新安裝您的連接器](application-proxy-add-on-premises-application.md)以取得任何更新。

如果不想等候您的連接器自動更新，您可以執行手動升級。 移至您的連接器所在伺服器上的[連接器下載頁面](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)並選取 [下載]****。 此流程就會開始進行本機連接器的升級。

對於具有多個連接器的租用戶，自動更新會一次以每個群組中的一個連接器為目標，以免您的環境發生停機。

如果是下列情況，連接器更新時可能會遭遇停機︰
  
- 您只擁有一個連接器，建議安裝第二個連接器並[建立連接器群組](application-proxy-connector-groups.md)。 如此可避免出現停機時間，並提供更高的可用性。  
- 更新開始時，連接器正處於交易中途。 雖然遺失起始交易，您的瀏覽器應該會自動重試作業，或您可以重新整理頁面。 當重新傳送要求時，流量會路由傳送至備份連接器。

若要查看先前發行的版本和所含變更的資訊，請參閱[應用程式 Proxy - 版本發行記錄](application-proxy-release-version-history.md)。

## <a name="creating-connector-groups"></a>建立連接器群組

連接器群組可讓您指派要服務特定應用程式的特定連接器。 您可以將多個連接器分組在一起，然後將每個應用程式指派給群組。

連接器群組可讓您更輕鬆地管理大型部署。 這些群組也會改善在不同區域中裝載應用程式之租用戶的延遲，因為您可以建立以位置為基礎的連接器群組，只服務本機應用程式。

若要深入了解連接器群組，請參閱[使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)。

## <a name="capacity-planning"></a>容量規劃

請務必確定連接器之間已規劃了足夠的容量，能夠處理預計流量。 我們建議每個連接器組至少有兩個連接器，以提供高可用性和擴展。 擁有三個連接器是最佳選擇，以防您隨時需要維修機器。

一般而言，使用者越多，您需要的機器就越大。 下表概述了不同電腦可以處理的卷和預期延遲。 請注意，這完全是根據預期的每秒交易數 (TPS)，而不是依據使用者，因為使用模式都不同，無法用來預測負載。 根據回應大小和後端應用程式回應時間，也會有一些差異，較大的回應和較慢的回應時間會導致「最大 TPS」較低。 我們還建議增加電腦，以便整個機器的分散式負載始終提供充足的緩衝。 額外的容量可確保具備高可用性和復原能力。

|核心|RAM|預期延遲 (MS)-P99|最大 TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\*此電腦使用自訂設置將一些預設連接限制提升到 .NET 建議設置之外。 我們建議先使用預設設定執行測試，然後再連絡支援人員為您的租用戶變更此限制。

> [!NOTE]
> 在 4、8 和 16 核心的機器之間，最大 TPS 沒有太大差異。 它們之間的主要差異在於預期延遲。
>
> 此表還重點介紹連接器基於其安裝的電腦類型的預期性能。 這與應用程式代理服務的限制是分開的，請參閱[服務限制和限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。

## <a name="security-and-networking"></a>安全性和網路服務

在允許連接器將要求傳送至應用程式 Proxy 服務的網路上，任何地方都可以安裝連接器。 重點是執行連接器的電腦也可存取您的應用程式。 您可以在貴公司網路內或在雲端中執行的虛擬機器上安裝連接器。 連接器可以在周邊網路（也稱為非軍事區 （DMZ） 內運行，但不需要，因為所有流量都是出站，因此您的網路保持安全。

連接器僅會傳送輸出要求。 輸出流量會傳送到應用程式 Proxy 服務和已發佈應用程式。 不需要開啟輸入連接埠，因為建立工作階段後，流量就會雙向流動。 也不需要透過您的防火牆來設定內部存取。

如需設定輸出防火牆規則的詳細資訊，請參閱[使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。

## <a name="performance-and-scalability"></a>效能和延展性

應用程式 Proxy 服務的級別很明顯，但級別是連接器的一項因素。 您需要有足夠的連接器，以處理尖峰流量。 因為連接器是無狀態，所以不受使用者或工作階段的數目所影響。 相反地，它們會依要求數目和其承載大小而作出反應。 以標準 Web 流量而言，一台普通電腦每秒可以處理幾千個要求。 具體產能取決於確切的電腦特性。

連接器效能受限於 CPU 和網路。 TLS 加密和解密需要 CPU 性能，而網路對於快速連接到 Azure 中的應用程式和聯機服務非常重要。

相反地，連接器發行需要較少的記憶體。 線上服務可以處理大部分的處理程序，及所有未經驗證的流量。 所有可在雲端中完成的內容都是在雲端中完成。

如果該連接器或機器由於任何原因而變成無法使用，流量將會開始流向群組中的其他連接器。 此備援能力也是我們建議使用多個連接器的理由。

影響效能的另一個因素是連接器之間的網路品質，包括︰

- **線上服務**：Azure 的應用程式 Proxy 服務連線變慢或高度延遲都會影響連接器效能。 為了達到最佳效能，請使用 Express Route 將貴組織連線到 Azure。 否則，請網路服務小組確定與 Azure 的連線盡可能以有效方式處理。
- **後端應用程式︰** 在某些情況下，連接器和後端應用程式之間有其他 Proxy，可能會使連線變慢或無法連線。 若要針對此情節進行疑難排解，可從連接器伺服器開啟瀏覽器，並嘗試存取應用程式。 如果您在 Azure 中執行連接器，但應用程式為內部部署，體驗就可能無法如您的使用者所預期。
- **網域控制站**：如果連接器使用 Kerberos 限制委派執行單一登入 （SSO），則在將要求傳送到後端之前，它們會與網域控制站聯繫。 連接器有 Kerberos 票證的快取 (但是在忙碌環境中)，網域控制器的回應速度可能會影響效能。 在 Azure 中執行、但與內部部署之網域控制器通訊的連接器會更常發生這個問題。

如需將您網路最佳化的詳細資訊，請參閱[使用 Azure Active Directory 應用程式 Proxy 時的網路拓撲考量](application-proxy-network-topology.md)。

## <a name="domain-joining"></a>加入網域

連接器可以在未加入網域的電腦上執行。 不過，如果您想要對使用整合式 Windows 驗證 (IWA) 的應用程式執行單一登入 (SSO)，則需要已加入網域的電腦。 在此情況下，連接器電腦必須代表已發佈應用程式的使用者加入可以執行 [Kerberos](https://web.mit.edu/kerberos) 限制委派的網域。

連接器也可以加入具有部分信任的網域或樹系，或加入唯讀網域控制站。

## <a name="connector-deployments-on-hardened-environments"></a>強化環境上的連接器部署

連接器部署通常都直截了當，不需要特殊組態。 不過，應該考量一些獨特的情況︰

- 限制傳出流量的組織必須[開啟必要的連接埠](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)。
- 符合 FIPS 規範的電腦可能需要變更設定，以允許連接器程序產生憑證並加以儲存。
- 根據發出網路要求的處理程序鎖定其環境的組織，必須確定已啟用兩個連接器服務才可存取所有必要的連接埠和 IP。
- 在某些情況下，輸出的正向 Proxy 可能會中斷雙向憑證驗證，並造成失敗的通訊。

## <a name="connector-authentication"></a>連接器驗證

為了提供安全服務，連接器必須向服務驗證，且服務必須向連接器驗證。 可在連接器將連線初始化時，使用用戶端和伺服器憑證來完成此驗證。 如此一來，系統管理員的使用者名稱和密碼不會儲存在連接器電腦上。

使用的憑證是特定於應用程式 Proxy 服務。 它們會在初始註冊期間建立，且每隔幾個月會由連接器自動更新。

如果連接器有數個月未連線至服務，它的憑證可能會過期。 在此情況下，請解除安裝並重新安裝連接器以觸發註冊。 您可以執行下列 PowerShell 命令：

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>背後原理

連接器是以 Windows Server Web 應用程式 Proxy 為基礎，因此有大部分相同的管理工具，包括 Windows 事件記錄

![使用事件檢視器](./media/application-proxy-connectors/event-view-window.png)

和 Windows 效能計數器管理事件記錄。

![使用效能監視器將計數器新增至連接器](./media/application-proxy-connectors/performance-monitor.png)

連接器有系統管理員和工作階段記錄。 系統管理記錄包含索引鍵事件和其錯誤。 這些工作階段記錄包含所有交易，以及它們的處理詳細資料。

若要查看記錄，請移至事件檢視器，開啟 [檢視]**** 功能表，並啟用 [顯示分析與偵錯記錄]****。 接著，啟用它們以開始收集事件。 這些記錄不會出現在 Windows Server 2012 R2 中的 Web 應用程式 Proxy，因為連接器會根據較新的版本。

您可以檢查 [服務] 視窗中的服務狀態。 連接器包含兩個 Windows 服務︰實際連接器和更新程式。 這兩者必須一直執行。

 ![示例：顯示 Azure AD 服務本地的服務視窗](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>後續步驟

- [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)
- [使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
- [針對應用程式 Proxy 和連接器錯誤進行疑難排解](application-proxy-troubleshoot.md)
- [如何靜默安裝 Azure AD 應用程式代理連接器](application-proxy-register-connector-powershell.md)
