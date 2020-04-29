---
title: 網路效能監控解決方案服務連線能力-Azure Log Analytics
description: 使用網路效能監控中的服務連線能力監視功能，對任何具有已開啟 TCP 連接埠的端點進行網路連線監視。
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 93f3820b7cf1db85b9ff4cd514fe22efc75a90d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249239"
---
# <a name="service-connectivity-monitor"></a>服務連線能力監視

您可以使用[網路效能監控](network-performance-monitor.md)中的服務連線能力監視功能，對任何具有已開啟 TCP 連接埠的端點進行網路連線監視。 這類端點包括網站、SaaS 應用程式、PaaS 應用程式和 SQL 資料庫。 

您可以使用服務連線能力監視來執行下列功能： 

- 監視多個分公司或位置與您的應用程式和網路服務的網路連線。 應用程式和網路服務包括 Office 365、Dynamics CRM、內部企業營運系統應用程式及 SQL 資料庫。
- 使用內建測試來監視 Office365 和 Dynamics365 端點的網路連線。 
- 判斷連線至端點時的回應時間、網路延遲及封包遺失。
- 判斷應用程式效能低落是因為網路還是因為應用程式提供者端的某些問題所造成。
- 檢視拓撲地圖上每個躍點所提供的延遲，識別可能導致應用程式效能低落的作用點。


![服務連線能力監視](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>設定 
若要開啟網路效能監控的設定，請開啟[網路效能監控解決方案](network-performance-monitor.md)，然後選取 [**設定**]。

![設定網路效能監控](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>設定 Log Analytics 代理程式以進行監視
在用於監視的節點上啟用下列防火牆規則，讓解決方案可以探索從您的節點到服務端點的拓撲： 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>建立服務連線能力監視測試 

開始建立您的測試，來監視服務端點的網路連線。

1. 選取 [服務連線能力監視]**** 索引標籤。
2. 選取 [新增測試]**** 並輸入測試名稱和描述。 您可以為每個工作區建立最多450個測試。 
3. 選取測試類型：<br>

    * 選取 [Web]**** 以監視回應 HTTP/S 要求之服務的連線，例如 outlook.office365.com 或 bing.com。<br>
    * 選取 [網路]**** 以監視回應 TCP 要求但不會回應 HTTP/S 要求之服務的連線，例如 SQL 伺服器、FTP 伺服器或 SSH 連接埠。 
    * 例如：若要建立 blob 儲存體帳戶的 web 測試，請選取 [ **web** ]，然後輸入 target as *yourstorageaccount*. blob.core.windows.net。 同樣地，您可以使用[此連結](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)來建立其他資料表儲存體、佇列儲存體和 Azure 檔案儲存體的測試。
4. 如果您不想要執行網路量測，例如網路延遲、封包遺失和拓撲探索，請清除 [執行網路量測]**** 核取方塊。 保持選取狀態可充分發揮功能。 
5. 在 [目標]**** 中，輸入您要監視網路連線的 URL/FQDN/IP 位址。
6. 在 [連接埠號碼]**** 中，輸入目標服務的連接埠號碼。 
7. 在 [測試頻率]**** 中，輸入您想要執行測試的頻率值。 
8. 選取您要監視服務網路連線的來源節點。 請確定每個測試新增的代理程式數目小於150。 任何代理程式都可以測試150端點/代理程式的上限。

    >[!NOTE]
    > 對於以 Windows Server 為基礎的節點，功能會使用以 TCP 為基礎的要求來執行網路測量。 對於以 Windows 用戶端為基礎的節點，功能會使用以 ICMP 為基礎的要求來執行網路測量。 在某些情況下，目標應用程式會在節點是以 Windows 用戶端為基礎時，封鎖以連入 ICMP 為基礎的要求。 此解決方案無法執行網路量測。 建議您在此情況下使用以 Windows Server 為基礎的節點。 

9. 如果您不想要建立所選項目的健康情況事件，則清除 [在此測試所涵蓋的目標上啟用健康情況監視]****。 
10. 選擇監視條件。 您可以輸入閾值，以設定健康情況事件產生的自訂閾值。 只要條件的值高於針對所選網路或子網路配對選取的閾值時，就會產生健康情況事件。 
11. 選取 [儲存]**** 以儲存組態。 

    ![服務連線能力監視測試組態](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>逐步介紹 

移至 [網路效能監控] 儀表板檢視。 若要對您所建立的不同測試取得健康情況摘要，請查看 [服務連線能力監視]**** 頁面。 

![服務連線能力監視頁面](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

選取圖格以檢視 [測試]**** 頁面上的測試詳細資料。 在左側資料表中，您可以檢視所有測試的時間點健康情況和服務回應時間的值、網路延遲及封包遺失。 使用 [網路狀態錄製器] 控制項，檢視過去另一個時間的網路快照集。 在資料表中選取您要調查的測試。 您可以在右側窗格的圖表中，檢視遺失、延遲和回應時間值的歷史趨勢。 選取 [測試詳細資料]**** 連結，即可檢視每個節點的效能。

![服務連線能力監視測試](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

在 [測試節點]**** 檢視中，您可以觀察來自每個節點的網路連線。 選取效能降低的節點。 這是能觀察到應用程式執行速度緩慢的節點。

若要判斷應用程式效能低落是因為網路還是因為應用程式提供者端的某些問題所造成，可觀察應用程式回應時間與網路延遲之間的相互關聯。 

* **應用程式問題：** 如果回應時間突然增加，但網路延遲是一致的，則表示網路運作正常，而問題可能是應用程式端的問題所致。 

    ![服務連線能力監視應用程式問題](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **網路問題：** 如果回應時間尖峰與對應的網路延遲尖峰同時出現，則表示回應時間增加可能是網路延遲的增加所致。 

    ![服務連線能力監視網路問題](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

在您判定問題是因為網路之後，選取 [拓撲]**** 檢視連結，以識別拓撲地圖上的問題躍點。 範例如下圖所示。 節點與應用程式端點之間的 105 毫秒延遲總計之中，有 96 毫秒是因為躍點標示為紅色。 識別出問題躍點之後，您可以採取矯正措施。 

![服務連線能力監視測試](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>診斷 

如果您觀察到異常，請依照下列步驟：

* 如果服務回應時間、網路遺失和延遲顯示為 NA，可能是因為下列一個或多個原因：

    - 應用程式已關閉。
    - 用於檢查服務之網路連線的節點已關閉。
    - 測試組態中輸入的目標不正確。
    - 節點沒有任何網路連線。

* 如果顯示有效的服務回應時間，但網路遺失和延遲顯示為 NA，可能是因為下列一個或多個原因：

    - 如果用於檢查服務之網路連線的節點為 Windows 用戶端機器，則目標服務會封鎖 ICMP 要求，或是網路防火牆會封鎖源自該節點的 ICMP 要求。
    - [執行網路量測]**** 核取方塊在測試組態中為空白。 

* 如果服務回應時間為 NA，但是網路遺失和延遲都是有效的，則目標服務可能並非 Web 應用程式。 編輯測試組態，然後選擇 [網路]**** 而不是 [Web]**** 作為測試類型。 

* 如果應用程式執行緩慢，請判斷應用程式效能低落是因為網路還是應用程式提供者端的某些問題所造成。

## <a name="gcc-office-urls-for-us-government-customers"></a>適用于美國政府客戶的 GCC Office Url
針對美國政府弗吉尼亞州地區，只有 DOD Url 是內建的 NPM。 使用 GCC Url 的客戶需要建立自訂測試，並個別新增每個 URL。

| 欄位 | GCC |
|:---   |:--- |
| Office 365 入口網站和共用 | portal.apps.mil |
| Office 365 驗證和身分識別 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online  | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS 團隊 | gov.teams.microsoft.us | 

## <a name="next-steps"></a>後續步驟
[搜尋記錄](../../azure-monitor/log-query/log-query-overview.md)以檢視詳細的網路效能資料記錄。
