---
title: Azure Sentinel UEBA 擴充參考 |Microsoft Docs
description: 本文顯示 Azure Sentinel 的實體行為分析所產生的實體擴充。
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
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901693"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Azure Sentinel UEBA 擴充參考

這些資料表會列出並描述可用來將安全性事件的調查集中在一起的實體擴充。

前兩個數據表 [ **使用者見解** ] 和 [ **裝置深入** 解析] 包含來自 Active Directory/Azure AD 和 Microsoft 威脅情報來源的實體資訊。

<a name="baseline-explained"></a>**活動深入解析資料表** 下的其餘資料表，包含以 Azure Sentinel 的實體行為分析所建立的行為設定檔為基礎的實體資訊。 活動會針對每次使用時動態編譯的基準進行分析。 每個活動都有定義此動態基準的定義回顧期間。 這段期間是在此資料表的 [ [**基準**](#activity-insights-tables) ] 資料行中指定。

> [!NOTE] 
> 這三個數據表中的 [ **擴充名稱** ] 欄位會顯示兩個數據列。 第一個（以 **粗體顯示**）是擴充的「易記名稱」。 第二個 *(的斜體和括弧)* 是儲存在 [**行為分析資料表**](identify-threats-with-entity-behavior-analytics.md#data-schema)中擴充的功能變數名稱。

## <a name="user-insights-table"></a>使用者見解資料表

| 擴充名稱 | 描述 | 範例值 |
| --- | --- | --- | --- |
| **帳戶顯示名稱**<br>*(AccountDisplayName)* | 使用者的帳戶顯示名稱。 | 系統管理員、Hayden 庫 |
| **帳戶網域**<br>*(AccountDomain)* | 使用者的帳戶網域名。 |  |
| **帳戶物件識別碼**<br>*(AccountObjectID)* | 使用者的帳戶物件識別碼。 | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **群發半徑**<br>*(BlastRadius)* | 會根據數個因素來計算群發半徑：使用者在組織樹狀結構中的位置，以及使用者的 Azure Active Directory 的角色和許可權。 | 低、中、高 |
| **為休眠帳戶**<br>*(IsDormantAccount)* | 此帳戶尚未用於過去180天。 | True、False |
| **是本機系統管理員**<br>*(IsLocalAdmin)* | 帳戶具有本機系統管理員許可權。 | True、False |
| **是新帳戶**<br>*(IsNewAccount)* | 帳戶是在過去30天內建立的。 | True、False |
| **內部部署 SID**<br>*(OnPremisesSID)* | 與動作相關之使用者的內部部署 SID。 | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>裝置見解資料表

| 擴充名稱 | 描述 | 範例值 |
| --- | --- | --- | --- |
| **瀏覽器**<br>*(瀏覽器)* | 動作中使用的瀏覽器。 | Edge、Chrome |
| **裝置系列**<br>*(DeviceFamily)* | 動作中使用的裝置系列。 | Windows |
| **裝置類型**<br>*(DeviceType)* | 動作中使用的用戶端裝置類型 | 桌上型 |
| **Isp**<br>*(ISP)* | 動作中使用的網際網路服務提供者。 |  |
| **作業系統**<br>*(作業系統)* | 動作中使用的作業系統。 | Windows 10 |
| **威脅 intel 指標描述**<br>*(ThreatIntelIndicatorDescription)* | 從動作中使用的 IP 位址解決之觀察到的威脅指標描述。 | 主機是殭屍網路： azorult 的成員 |
| **威脅 intel 指標類型**<br>*(ThreatIntelIndicatorType)* | 從動作中使用的 IP 位址解析的威脅指標類型。 | 殭屍網路、C2、CryptoMining、Darknet、Ddos、MaliciousUrl、Malware、網路釣魚、Proxy、PUA、關注清單 |
| **使用者代理程式**<br>*(UserAgent)* | 動作中使用的使用者代理程式。 | Microsoft Azure Graph 用戶端程式庫1.0、<br>Swagger-Codegen/1.4.0.0/csharp、<br>EvoSTS |
| **使用者代理程式系列**<br>*(UserAgentFamily)* | 動作中使用的使用者代理程式系列。 | Chrome、Edge、Firefox |
|

## <a name="activity-insights-tables"></a>活動見解資料表

#### <a name="action-performed"></a>執行的動作

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **使用者第一次執行動作**<br>*(FirstTimeUserPerformedAction)* | 180 | 使用者第一次執行此動作。 | True、False |
| **使用者執行的動作覆**<br>*(ActionUncommonlyPerformedByUser)* | 10 | 使用者通常不會執行此動作。 | True、False |
| **在對等之間執行的動作覆**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | 動作通常不會在使用者的對等之間執行。 | True、False |
| **在租使用者中執行的第一次動作**<br>*(FirstTimeActionPerformedInTenant)* | 180 | 此動作是由組織中的任何人第一次執行。 | True、False |
| **在租使用者中執行的動作覆**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | 此動作不會在組織中經常執行。 | True、False |
|

#### <a name="app-used"></a>使用的應用程式

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **使用者第一次使用應用程式**<br>*(FirstTimeUserUsedApp)* | 180 | 使用者第一次使用應用程式。 | True、False |
| **使用者使用的應用程式覆**<br>*(AppUncommonlyUsedByUser)* | 10 | 使用者通常不會使用此應用程式。 | True、False |
| **對等之間使用的應用程式覆**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | 應用程式通常不會在使用者的對等之間使用。 | True、False |
| **租使用者中第一次觀察到的應用程式**<br>*(FirstTimeAppObservedInTenant)* | 180 | 在組織中第一次觀察到應用程式。 | True、False |
| **租使用者中使用的應用程式覆**<br>*(AppUncommonlyUsedInTenant)* | 180 | 應用程式通常不會在組織中使用。 | True、False |
| 

#### <a name="browser-used"></a>使用的瀏覽器

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **使用者第一次透過瀏覽器連線**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | 使用者第一次觀察到瀏覽器。 | True、False |
| **使用者使用的瀏覽器覆**<br>*(BrowserUncommonlyUsedByUser)* | 10 | 使用者通常不會使用瀏覽器。 | True、False |
| **對等之間使用的瀏覽器覆**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | 瀏覽器不常用於使用者的對等。 | True、False |
| **第一次在租使用者中觀察瀏覽器**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | 在組織中第一次觀察到瀏覽器。 | True、False |
| **租使用者中使用的瀏覽器覆**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | 此瀏覽器不常用於組織。 | True、False |
| 

#### <a name="country-connected-from"></a>國家/地區的連線來源

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **使用者第一次從國家/地區連線**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | 使用者第一次從 IP 位址解析的地理位置是由使用者第一次連線。 | True、False |
| **從使用者連線的國家/地區覆**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | 從 IP 位址解析的地理位置通常不是由使用者進行連線。 | True、False |
| **從對等互連的國家/地區覆**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | 從 IP 位址解析的地理位置，通常不會與使用者的對等之間的連線。 | True、False |
| **第一次從租使用者中觀察到的國家/地區連接**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | 組織中的任何人第一次連線的國家/地區。 | True、False |
| **從租使用者中連線的國家/地區覆**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | 地理位置（從 IP 位址解析）通常不會與組織進行連線。 | True、False |
| 

#### <a name="device-used-to-connect"></a>用來連接的裝置

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **第一次從裝置連線的使用者**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | 第一次由使用者連接來源裝置。 | True、False |
| **使用者使用的裝置覆**<br>*(DeviceUncommonlyUsedByUser)* | 10 | 使用者通常不會使用該裝置。 | True、False |
| **對等之間使用的裝置覆**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | 裝置通常不會在使用者的對等之間使用。 | True、False |
| **在租使用者中第一次觀察到裝置**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | 在組織中第一次觀察到裝置。 | True、False |
| **租使用者中使用的裝置覆**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | 裝置通常不會在組織中使用。 | True、False |
| 

#### <a name="other-device-related"></a>其他裝置相關

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **使用者第一次登入裝置**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | 使用者第一次連接到目的地裝置。 | True、False |
| **租使用者中使用的裝置系列覆**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | 組織中通常不會使用裝置系列。 | True、False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>用來連接的網際網路服務提供者

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **使用者第一次透過 ISP 連線**<br>*(FirstTimeUserConnectedViaISP)* | 30 | 使用者第一次觀察到 ISP。 | True、False |
| **使用者使用的 ISP 覆**<br>*(ISPUncommonlyUsedByUser)* | 10 | 使用者通常不會使用 ISP。 | True、False |
| **對等之間使用的 ISP 覆**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | 使用者的對等使用者通常不會使用 ISP。 | True、False |
| **第一次透過租使用者中的 ISP 連接**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | 在組織中第一次觀察到 ISP。 | True、False |
| **租使用者中使用的 ISP 覆**<br>*(ISPUncommonlyUsedInTenant)* | 30 | 組織中通常不會使用 ISP。 | True、False |
| 

#### <a name="resource-accessed"></a>資源已存取

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **使用者第一次存取資源**<br>*(FirstTimeUserAccessedResource)* | 180 | 第一次有使用者存取資源。 | True、False |
| **使用者存取的資源覆**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | 使用者通常不會存取資源。 | True、False |
| **在對等中存取的資源覆**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | 資源通常不會在使用者的對等之間進行存取。 | True、False |
| **第一次在租使用者中存取資源**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | 組織中的任何人首次存取資源。 | True、False |
| **在租使用者中存取的資源覆**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | 資源通常不會在組織中存取。 | True、False |
| 

#### <a name="miscellaneous"></a>其他

| 擴充名稱 | [基準](#baseline-explained) (日)  | 描述 | 範例值 |
| --- | --- | --- | --- |
| **上次使用者執行動作的時間**<br>*(LastTimeUserPerformedAction)* | 180 | 上次使用者執行相同動作的時間。 | <Timestamp> |
| **先前未執行類似的動作**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | 使用者未執行相同資源提供者中的任何動作。 | True、False |
| **來源 IP 位置**<br>*(SourceIPLocation)* | *N/A* | 從動作的來源 IP 解析的國家/地區。 | [Surrey，英國] |
| **不尋常的大量作業**<br>*(UncommonHighVolumeOfOperations)* | 7 | 使用者在相同的提供者中執行了類似作業的高載 | True、False |
| **不尋常的 Azure AD 條件式存取失敗數目**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | 由於條件式存取而無法驗證的使用者數目不尋常 | True、False |
| **新增的裝置數目不尋常**<br>*(UnusualNumberOfDevicesAdded)* | 5 | 使用者新增了不尋常的裝置數目。 | True、False |
| **刪除的裝置數目不尋常**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | 使用者刪除了不尋常的裝置數目。 | True、False |
| **新增至群組的使用者數目不尋常**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | 使用者將不尋常的使用者數目新增至群組。 | True、False |
|