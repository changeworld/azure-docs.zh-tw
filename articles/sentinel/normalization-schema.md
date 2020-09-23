---
title: Azure Sentinel 資料正規化架構參考 |Microsoft Docs
description: 本文顯示 Azure Sentinel 資料正規化架構。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: eb1752ea66f2cbebf6a653705b5a760e8e268240
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933951"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Azure Sentinel 資料正規化架構參考

## <a name="terminology"></a>詞彙

Sentinel 的架構中會使用下列術語：

| 詞彙 | 定義 |
| ---- | ---------- |
| 報告裝置 | 傳送記錄給 Azure Sentinel 的系統。 它可能不是記錄的主體系統。 |
| Record | 從報告裝置傳送的資料單位。 這通常稱為「記錄」、「事件」或「警示」，但不一定要是其中之一。 |
|

## <a name="data-types-and-formats"></a>資料類型和格式

值應根據下列指導方針進行正規化。 這是正規化欄位的必要項，建議用於其他欄位。 

| 資料類型 | 實體類型 | 格式和值 |
| --------- | ------------- | ---------------- |
| **日期/時間** | 視內嵌方法功能的遞減優先順序而定：<ul><li>Log Analytics 內建的日期時間類型</li><li>使用 Log Analytics datetime 數值標記法的整數位段</li><li>使用 Log Analytics datetime 數值標記法的字串欄位</li></ul> | Log Analytics 日期時程表示法。 <br></br>Log Analytics date & 時程表示法在本質上很類似，但不同于 Unix 時程表示法。 請參閱這些轉換指導方針。 <br></br>日期 & 時間應該是時區調整時間。 |
| **MAC 位址** | String | 冒號-十六進位標記法 |
| **IP 位址** | IP 位址 | 架構沒有個別的 IPv4 和 IPv6 位址。 任何 IP 位址欄位都可以包含 IPv4 位址或 IPv6 位址：<ul><li>以點-十進位標記法表示的 IPv4</li><li>IPv6 （8 hextets 標記法），允許此處所述的簡短形式。</li></ul> |
| **使用者** | String | 可用的3個使用者欄位如下：<ul><li>使用者名稱</li><li>使用者 UPN</li><li>使用者網域</li></ul> |
| **使用者識別碼** | String | 目前支援下列2個使用者識別碼：<ul><li>使用者 SID</li><li>Azure Active directory 識別碼</li></ul> |
| **裝置** | String | 支援下列3個裝置/主機資料行：<ul><li>ID</li><li>名稱</li><li>完整網域名稱 (FQDN)</li></ul> |
| **國家/地區** | String | 根據此優先順序使用 ISO 3166-1 的字串：<ul><li>Alpha-2 代碼 (也就是美國) </li><li>美國的美式程式碼 (即美國) </li><li>簡短名稱</li></ul> |
| **區域** | String | 使用 ISO 3166-2 的國家/地區子部門名稱 |
| **城市** | String | |
| **經度** | Double | ISO 6709 座標標記法 (帶正負號的十進位)  |
| **緯度** | Double | ISO 6709 座標標記法 (帶正負號的十進位)  |
| **雜湊演算法** | String | 支援下列4個雜湊資料行：<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **檔案類型** | String | 檔案類型的類型：<ul><li>延伸模組</li><li>類別</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>網路會話資料表架構

以下是網路會話資料表的架構（版本1.0.0）

| 欄位名稱 | 值類型 | 範例 | 描述 | 相關聯的 OSSEM 實體 |
|-|-|-|-|-|
| EventType | String | 交通流量 | 要收集的事件種類 | 事件 |
| EventSubType | String | 驗證 | 類型的其他描述（如果適用） | 事件 |
| EventCount | 整數  | 10 | 匯總的事件數目（如果適用）。 | 事件 |
| EventEndTime | 日期/時間 | 請參閱「資料類型」 | 事件結束的時間 | 事件 |
| EventMessage | 字串 |  拒絕存取 | 記錄中包含或產生的一般訊息或描述 | 事件 |
| DvcIpAddr | IP 位址 |  23.21.23.34 | 產生記錄之裝置的 IP 位址 | 裝置、<br>IP |
| DvcMacAddr | String | 06：10：9f： eb：8f：14 | 傳送事件的來源報告裝置之網路介面的 MAC 位址。 | 裝置、<br>Mac |
| DvcHostname | 裝置名稱 (字串)  | syslogserver1.contoso.com | 產生訊息之裝置的裝置名稱。 | 裝置 |
| EventProduct | String | OfficeSharepoint | 產生事件的產品。 | 事件 |
| EventProductVersion | 字串 | 9.0 |  產生事件的產品版本。 | 事件 |
| EventResourceId | 裝置識別碼 (字串)  | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | 產生訊息之裝置的資源識別碼。 | 事件 |
| EventReportUrl | String | https://192.168.1.1/repoerts/ae3-56.htm | 報表裝置所建立的完整報表連結 | 事件 |
| EventVendor | String | Microsoft | 產生事件的產品廠商。 | 事件 |
| EventResult | 多重值： Success、Partial、失敗、[Empty] (字串)  | 成功 | 針對活動報告的結果。 如果不適用則為空白值。 | 事件 |
| EventResultDetails | String | 錯誤的密碼 | EventResult 中回報之結果的原因或詳細資料 | 事件 |
| EventSchemaVersion | Real | 0.1 | Azure Sentinel 架構版本。 目前為0.1。 | 事件 |
| EventSeverity | String | 低度 | 如果回報的活動有安全性影響，則表示影響的嚴重性。 | 事件 |
| EventOriginalUid | String | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | 來自報表裝置的記錄識別碼。 | 事件 |
| EventStartTime | 日期/時間 | 請參閱「資料類型」 | 事件的指定時間 | 事件 |
| TimeGenerated | 日期/時間 | 請參閱「資料類型」 | 事件發生的時間，如報告來源所報告。 | 自訂欄位 |
| EventTimeIngested | 日期/時間 | 請參閱「資料類型」 | 內嵌事件的時間 Azure Sentinel。 將由 Azure Sentinel 加入。 | 事件 |
| EventUid | Guid (字串)  | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Sentinel 用來標示資料列的唯一識別碼。 | 事件 |
| NetworkApplicationProtocol | String | HTTPS | 連接或會話所使用的應用層通訊協定。 | 網路 |
| DstBytes | int | 32455 | 從目的地傳送到連接或會話來源的位元組數目。 | Destination |
| SrcBytes | int | 46536 | 從來源傳送到連接或會話目的地的位元組數目。 | 來源 |
| NetworkBytes | int | 78991 | 雙向傳送的位元組數目。 如果 BytesReceived 和 BytesSent 都存在，則 BytesTotal 應等於其總和。 | 網路 |
| NetworkDirection | 多重值：輸入、輸出 (字串)  | 輸入 | 連接或會話進出組織的方向。 | 網路 |
| DstGeoCity | String | 伯 靈 頓 | 與目的地 IP 位址相關聯的城市 | 目的地<br>地理區域 |
| DstGeoCountry | 國家 (字串)  | USA | 與來源 IP 位址相關聯的國家/地區 | 目的地<br>地理區域 |
| DstDvcHostname | 裝置名稱 (字串)  |  victim_pc | 目的地裝置的裝置名稱 | Destination<br>裝置 |
| DstDvcFqdn | String | victim_pc | 建立記錄檔之主機的完整功能變數名稱 | 目的地<br>裝置 |
| DstDomainHostname | 字串 | CONTOSO | 目的地的網域、目的地主機的網域 (網站、功能變數名稱等 ) ，例如 DNS 查閱或 NS 查閱 | Destination |
| DstInterfaceName | 字串 | Microsoft Hyper-V 網路介面卡 | 目的地裝置用於連接或會話的網路介面。 | Destination |
| DstInterfaceGuid | 字串 | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | 用於驗證要求的網路介面 GUID  | Destination |
| DstIpAddr | IP 位址 | 2001： db8：： ff00：42：8329 | 連線或會話目的地的 IP 位址，最常稱為網路封包中的目的地 IP | 目的地<br>IP |
| DstDvcIpAddr | IP 位址 | 75.22.12.2 | 未直接與網路封包相關聯之裝置的目的地 IP 位址 | 目的地<br>裝置、<br>IP
| DstGeoLatitude | 緯度 (雙精度)  | 44.475833 | 與目的地 IP 位址相關聯之地理座標的緯度 | 目的地<br>地理區域 |
| DstMacAddr | String | 06：10：9f： eb：8f：14 | 連線或會話終止之網路介面的 MAC 位址，最常在網路封包中參考到目的地 MAC | 目的地<br>MAC |
| DstDvcMacAddr | String | 06：10：9f： eb：8f：14 | 未直接與網路封包相關聯之裝置的目的地 MAC 位址。 | 目的地<br>裝置、<br>MAC |
| DstDvcDomain | String | CONTOSO | 目的地裝置的網域。 | 目的地<br>裝置 |
| DstPortNumber | 整數 | 443 | 目的地 IP 通訊埠。 | 目的地<br>連接埠 |
| DstGeoRegion | 區域 (字串)  | 佛蒙特 | 與目的地 IP 位址相關聯的國家/地區內的區域 | 目的地<br>地理區域 |
| DstResourceId | 裝置識別碼 (字串)  |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | 目的地裝置的資源識別碼。 | Destination |
| DstNatIpAddr | IP 位址 | 2::1 | 如果中繼 NAT 裝置（如防火牆）回報，則 NAT 裝置使用的 IP 位址與來源進行通訊。 | 目的地 NAT、<br>IP |
| DstNatPortNumber | int | 443 | 如果是由中繼 NAT 裝置（如防火牆）所回報，則 NAT 裝置用來與來源進行通訊的埠。 | 目的地 NAT、<br>連接埠 |
| DstUserSid | 使用者 SID |  S-12-1445 | 與會話目的地相關聯之身分識別的使用者識別碼。 通常是用來驗證服務器的身分識別。 如需詳細資訊，請參閱「資料類型」。 | 目的地<br>User |
| DstUserAadId | 字串 (guid)  | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | 會話目的端使用者的 Azure AD 帳戶物件識別碼 | 目的地<br>User |
| DstUserName | Username (字串)  | johnd | 與會話目的地相關聯之身分識別的使用者名稱。  | 目的地<br>User |
| DstUserUpn | 字串 | johnd@anon.com | 與會話目的地相關聯之身分識別的 UPN。 | 目的地<br>User |
| DstUserDomain | 字串 | WORKGROUP | 位於會話目的地之帳戶的網域或電腦名稱稱 | 目的地<br>User |
| DstZone | String | Dmz | 目的地的網路區域，如報表裝置所定義。 | Destination |
| DstGeoLongitude | 經度 (雙精度)  | -73.211944 | 與目的地 IP 位址相關聯之地理座標的經度 | 目的地<br>地理區域 |
| DvcAction | 多重值：允許、拒絕、卸載 (字串)  | 允許 | 如果是由媒介裝置（如防火牆）所回報，則是由裝置所採取的動作。 | 裝置 |
| DvcInboundInterface | String | eth0 | 如果是由媒介裝置（如防火牆）所回報，則會使用網路介面連線到來源裝置。 | 裝置 |
| DvcOutboundInterface | String  | 乙太網路介面卡 Ethernet 4 | 如果是由媒介裝置（如防火牆）所回報，則會使用網路介面連線到目的地裝置。 | 裝置 |
| NetworkDuration | 整數 | 1500 | 網路會話或連接完成的時間量（以毫秒為單位） | 網路 |
| NetworkIcmpCode | 整數 | 34 | 針對 ICMP 訊息，ICMP 訊息類型數值 (RFC 2780 或 RFC 4443) 。 | 網路 |
| NetworkIcmpType | String | 目的地無法連線 | 針對 ICMP 訊息，ICMP 訊息類型文字表示 (RFC 2780 或 RFC 4443) 。 | 網路 |
| DstPackets | int  | 446 | 從目的地傳送到連接或會話來源的封包數目。 封包的意義是由報表裝置所定義。 | Destination |
| SrcPackets | int  | 6478 | 從來源傳送到連接或會話目的地的封包數目。 封包的意義是由報表裝置所定義。 | 來源 |
| NetworkPackets | int  | 0 | 雙向傳送的封包數目。 如果 PacketsReceived 和 PacketsSent 都存在，則 BytesTotal 應等於其總和。 | 網路 |
| HttpRequestTime | 整數 | 700 | 將要求傳送到伺服器所需的時間（如果適用）。 | Http |
| HttpResponseTime | 整數 | 800 | 在伺服器中接收回應所花費的時間（如果適用）。 | Http |
| NetworkRuleName | String | AnyAnyDrop | DeviceAction 決定依據的規則名稱或識別碼 | 網路 |
| NetworkRuleNumber | int |  23 | 符合的規則編號  | 網路 |
| NetworkSessionId | 字串 | 172_12_53_32_4322__123_64_207_1_80 | 報表裝置所報告的會話識別碼。 例如，在驗證之後，特定應用程式的 L7 會話識別碼 | 網路 |
| SrcGeoCity | String | 伯 靈 頓 | 與來源 IP 位址相關聯的城市 | 源<br>地理區域 |
| SrcGeoCountry | 國家 (字串)  | USA | 與來源 IP 位址相關聯的國家/地區 | 源<br>地理區域 |
| SrcDvcHostname | 裝置名稱 (字串)  |  小人 | 來源裝置的裝置名稱 | 源<br>裝置 |
| SrcDvcFqdn | 字串 | Villain.malicious.com | 建立記錄檔之主機的完整功能變數名稱 | 源<br>裝置 |
| SrcDvcDomain | 字串 | EVILORG | 起始會話的裝置網域 | 源<br>裝置 |
| SrcDvcOs | String | iOS | 來源裝置的作業系統 | 源<br>裝置 |
| SrcDvcModelName | String | Samsung Galaxy Note | 來源裝置的型號名稱 | 源<br>裝置 |
| SrcDvcModelNumber | String | 10.0 | 來源裝置的模型編號 | 源<br>裝置 |
| SrcDvcType | String | 行動 | 來源裝置的類型 | 源<br> 裝置 |
| SrcIntefaceName | String | eth01 | 來源裝置用於連接或會話的網路介面。 | 來源 |
| SrcInterfaceGuid | String | 46ad544b-eaf0-47ef-827c-266030f545a6 | 所使用網路介面的 GUID | 來源 |
| SrcIpAddr | IP 位址 | 77.138.103.108 | 從中產生連接或會話的 IP 位址。 | 源<br>IP |
| SrcDvcIpAddr | IP 位址 | 77.138.103.108 | 裝置的來源 IP 位址，不會直接與提供者所收集的網路封包或明確計算的)  (相關聯。 | 源<br>裝置、<br>IP |
| SrcGeoLatitude | 緯度 (雙精度)  | 44.475833 | 與來源 IP 位址相關聯之地理座標的緯度 | 源<br>地理區域 |
| SrcGeoLongitude | 經度 (雙精度)  | -73.211944 | 與來源 IP 位址相關聯之地理座標的經度 | 源<br>地理區域 |
| SrcMacAddr | String | 06：10：9f： eb：8f：14 | 從中產生連接 od 會話之網路介面的 MAC 位址。 | 源<br>Mac |
| SrcDvcMacAddr | String | 06：10：9f： eb：8f：14 | 未直接與網路封包相關聯之裝置的來源 MAC 位址。 | 源<br>裝置、<br>Mac |
| SrcPortNumber | 整數 | 2335 | 從中產生連接的 IP 埠。 可能不會與包含多個連接的會話相關。 | 源<br>連接埠 |
| SrcGeoRegion | 區域 (字串)  | 佛蒙特 | 與來源 IP 位址相關聯的國家/地區內的區域 | 源<br>地理區域 |
| SrcResourceId | String | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | 產生訊息之裝置的資源識別碼。 | 來源 |
| SrcNatIpAddr | IP 位址 | 4.3.2.1 | 如果中繼 NAT 裝置（如防火牆）回報，則 NAT 裝置用來與目的地通訊的 IP 位址。 | 來源 NAT、<br>IP |
| SrcNatPortNumber | 整數 | 345 | 如果是由中繼 NAT 裝置（如防火牆）所回報，則 NAT 裝置用來與目的地通訊的埠。 | 來源 NAT、<br>連接埠 |
| SrcUserSid | 使用者識別碼 (字串)  | S-15-1445 | 與會話來源相關聯之身分識別的使用者識別碼。 一般來說，使用者在用戶端上執行動作。 如需詳細資訊，請參閱「資料類型」。 | 源<br>User |
| SrcUserAadId | 字串 (guid)  | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | 位於會話來源端之使用者的 Azure AD 帳戶物件識別碼 | 源<br>User |
| SrcUserName | Username (字串)  | 鮑勃 | 與會話來源相關聯之身分識別的使用者名稱。 一般來說，使用者在用戶端上執行動作。 如需詳細資訊，請參閱「資料類型」。 | 來源<br>User |
| SrcUserUpn | 字串 | bob@alice.com | 起始會話的帳戶 UPN | 源<br>User |
| SrcUserDomain | 字串 | DESKTOP | 起始會話之帳戶的網域 | 源<br>User |
| SrcZone | String | 點選 | 來源的網路區域，如報表裝置所定義。 | 來源 |
| NetworkProtocol | String | TCP | 連接或會話所使用的 IP 通訊協定。 通常是 TCP、UDP 或 ICMP | 網路 |
| CloudAppName | String | Facebook | Proxy 所識別之 HTTP 應用程式的目的地應用程式名稱。 | Cloud |
| CloudAppId | String | 124 | Proxy 識別的 HTTP 應用程式之目的應用程式的識別碼。 此值通常是使用的 proxy 所特有。 | Cloud |
| CloudAppOperation | String | DeleteFile | 使用者在目的地應用程式內容中執行的作業，如 proxy 所識別的 HTTP 應用程式。 此值通常是使用的 proxy 所特有。 | Cloud |
| CloudAppRiskLevel | String | 3 | 與 proxy 所識別的 HTTP 應用程式相關聯的風險層級。 此值通常是使用的 proxy 所特有。 | Cloud |
| FileName | String | ImNotMalicious.exe | 針對通訊協定（例如 FTP 和 HTTP）透過網路連線傳輸的檔案名，提供檔案名資訊。 | 檔案 |
| FilePath | String | C:\Malicious\ImNotMalicious.exe | 檔案的完整路徑，包括檔案名 | 檔案 |
| FileHashMd5 | String | 51BC68715FC7C109DCEA406B42D9D78F | 透過網路連線傳輸通訊協定的 MD5 雜湊值。 | 檔案 |
| FileHashSha1 | String | 491AE3...C299821476F4 | 透過網路連線傳輸通訊協定的 SHA1 雜湊值。 | 檔案 |
| FileHashSha256 | String | 9B8F8EDB...C129976F03 | 透過網路連線來傳輸通訊協定的 SHA256 雜湊值。 | 檔案 |
| FileHashSha512 | String | 5E127D...F69F73F01F361 | 透過網路連線來傳送通訊協定之檔案的 SHA512 雜湊值。 | 檔案 |
| FileExtension |  String | exe | 針對通訊協定（例如 FTP 和 HTTP）透過網路連線傳輸的檔案類型。 | 檔案
| FileMimeType | String | 應用程式/msword | 針對通訊協定（例如 FTP 和 HTTP）透過網路連線傳輸的檔 MIME 類型 | 檔案 |
| FileSize | 整數 | 23500 | 以位元組為單位的檔案大小（以位元組為單位），透過網路連接來傳送通訊協定。 | 檔案 |
| HttpVersion | String | 2.0 | HTTP/HTTPS 網路連接的 HTTP 要求版本。 | Http |
| HttpRequestMethod | String | GET | HTTP/HTTPS 網路會話的 HTTP 方法。 | Http |
| HttpStatusCode | String | 404 | HTTP/HTTPS 網路會話的 HTTP 狀態碼。 | Http |
| HttpContentType | String | 多部分/表單資料;界限 = 某個事物 | HTTP/HTTPS 網路會話的 HTTP 回應內容類型標頭。 | Http |
| HttpReferrerOriginal | String | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS 網路會話的 HTTP 查閱者標頭。 | Http |
| HttpUserAgentOriginal | String | Mozilla/5.0 (Windows NT 10.0;WOW64) AppleWebKit/537.36 (KHTML，例如 Gecko) Chrome/83.0.4103.97 Safari/537.36 | HTTP/HTTPS 網路會話的 HTTP 使用者代理程式標頭。 | Http |
| HttpRequestXff | String | 120.12.41.1 | HTTP/HTTPS 網路會話的 HTTP X 轉送標頭。 | Http |
| UrlCategory | String | 搜尋引擎 | URL 的定義群組 (或可能只根據 URL 中的網域) 與 (的內容相關，例如：成人、新聞、廣告、寄存網域等等 )  | url |
| UrlOriginal | String | HTTPs://contoso.com/fo/?k=v&q = u # f | HTTP/HTTPS 網路會話的 HTTP 要求 URL。 | Url |
| UrlHostname | String | contoso.com | HTTP/HTTPS 網路會話的 HTTP 要求 URL 網域部分。 | Url |
| ThreatCategory | String | 木馬 | 安全性系統（例如 IP 的 Web 安全性閘道）所識別的威脅類別，與此網路會話相關聯。 | 威脅 |
| ThreatId | String | Tr. 124 | 安全性系統（例如 IP 的 Web 安全性閘道）所識別的威脅識別碼，與此網路會話相關聯。 | 威脅 |
| ThreatName | String | EICAR 測試檔案 | 所識別的威脅或惡意程式碼名稱 | 威脅 |
| AdditionalFields | 動態 (JSON 包)  | {<br>Property1： "val1"、<br>Property2： "val2"<br>} | 當架構中沒有任何個別的資料行符合時，其他欄位可以儲存在 JSON 包中。<br>針對查詢時間剖析，建議您不要使用此方法，因為將資料封裝成 JSON 將會降低查詢效能。 相反地，建議您升級額外的資料行。<br>在未來的內嵌時間剖析案例中，會將其他資料收集到這個 JSON 包資料行中。 | 自訂欄位 |
| 
