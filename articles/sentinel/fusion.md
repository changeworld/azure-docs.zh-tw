---
title: Azure Sentinel 中的 Advanced 多階段攻擊偵測
description: 在 Azure Sentinel 中使用融合技術來減少警示疲勞，並建立以先進多階段攻擊偵測為基礎的可採取動作事件。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77587918"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel 中的 Advanced 多階段攻擊偵測


> [!IMPORTANT]
> Azure Sentinel 中的某些融合功能目前為公開預覽狀態。
> 這些功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



藉由使用以機器學習為基礎的融合技術，Azure Sentinel 可以結合異常行為和在終止鏈的各種階段觀察到的可疑活動，來自動偵測多階段攻擊。 Azure Sentinel 接著會產生嚴重難以攔截的事件。 這些事件會將兩個或多個警示或活動。 根據設計，這些事件的數量很低、高精確度和高嚴重性。

針對您的環境進行自訂，這種偵測不僅能減少誤報的正面，還能偵測到有限制或遺失資訊的攻擊。

## <a name="configuration-for-advanced-multistage-attack-detection"></a>進階多階段攻擊偵測組態

預設會在 Azure Sentinel 中啟用此偵測。 若要檢查狀態或停用它，可能是因為您使用替代解決方案來根據多個警示建立事件，請使用下列指示：

1. 如果您尚未這麼做，請登入[Azure 入口網站](https://portal.azure.com)。

2. 流覽至**Azure Sentinel**設定  >  **Configuration**  >  **分析**

3. 選取作用中的**規則**，並在 [**名稱**] 資料行中找出 [ **Advanced 多階段攻擊偵測**] 檢查 [**狀態**] 欄，確認是否已啟用或停用此偵測。

4. 若要變更狀態，請選取此專案，然後在 [ **Advanced 多階段攻擊偵測**] 分頁上，選取 [**編輯**]。

5. 在 [**規則建立嚮導]** 分頁上，系統會自動為您選取狀態的變更，因此請選取 **[下一步：審查]**，然後按一下 [**儲存**]。 

規則範本不適用於 advanced 多階段攻擊偵測。

> [!NOTE]
> Azure Sentinel 目前使用30天的歷程記錄資料來訓練機器學習系統。 當此資料通過機器學習管線時，一律會使用 Microsoft 的金鑰進行加密。 不過，如果您在 Azure Sentinel 工作區中啟用 CMK，則不會使用[客戶管理的金鑰（CMK）](customer-managed-keys.md)來加密定型資料。 若要選擇不融合，請流覽至**Azure Sentinel**設定分析使用中   \>  **Configuration**   \>  **規則] [ \> \> Advanced 多階段攻擊偵測**]，然後在 [**狀態**] 欄中選取 [停用] **。**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>使用 Palo Alto 網路和 Microsoft Defender ATP 進行融合

這些案例結合了安全性分析師所使用的兩個基本記錄：來自 Palo Alto 網路的防火牆記錄，以及來自 Microsoft Defender ATP 的端點偵測記錄。 在下面所列的所有案例中，會在包含外部 IP 位址的端點中偵測到可疑的活動，然後將來自外部 IP 位址的異常流量傳回防火牆。 在 Palo Alto 記錄中，Azure Sentinel 著重于[威脅記錄](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，而當允許威脅時，會將流量視為可疑（可疑的資料、檔案、洪水、封包、掃描、間諜軟體、url、病毒、弱點、野火病毒、wildfires）。

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 匿名服務的網路要求，後面接著 Palo Alto 網路防火牆所標記的異常流量。

在此案例中，Azure Sentinel 會先偵測到 Microsoft Defender Advanced 威脅防護對導致異常活動的 TOR 匿名 service 提出網路要求的警示。 這是在 {time} 的帳戶 {account name} （具有 SID 識別碼 {sid}）底下起始。 連線的連出 IP 位址是 {IndividualIp}。
然後，Palo Alto Networks Firewall 在 {TimeGenerated} 偵測到不尋常的活動。 這表示惡意流量進入您的網路，網路流量的目的地 IP 位址是 {DestinationIP}。

此案例目前為公開預覽狀態。


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell 建立了可疑的網路連線，後面接著以 Palo Alto 網路防火牆標記的異常流量。

在此案例中，Azure Sentinel 會先偵測到 Microsoft Defender Advanced 威脅防護偵測到 PowerShell 建立了可疑的網路連線，因而導致 Palo Alto 網路防火牆所偵測到的異常活動。 這是由帳戶 {account name} 在 {time} 的 SID 識別碼 {sid} 起始。 連線的連出 IP 位址是 {IndividualIp}。 然後，Palo Alto Networks Firewall 在 {TimeGenerated} 偵測到不尋常的活動。 這表示惡意流量進入您的網路。 網路流量的目的地 IP 位址是 {DestinationIP}。

此案例目前為公開預覽狀態。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>連到 IP 的輸出連線，包含未經授權的存取嘗試歷程記錄，後面接著 Palo Alto 網路防火牆所標記的異常流量

在此案例中，Azure Sentinel 會偵測到警示，表示 Microsoft Defender Advanced 威脅防護偵測到 IP 位址的輸出連線，其記錄了未經授權的存取嘗試，而導致 Palo Alto 網路防火牆偵測到異常活動。 這是由帳戶 {account name} 在 {time} 的 SID 識別碼 {sid} 起始。 連線的連出 IP 位址是 {IndividualIp}。 在此情況下，Palo Alto Networks Firewall 會在 {TimeGenerated} 偵測到不尋常的活動。 這表示惡意流量進入您的網路。 網路流量的目的地 IP 位址是 {DestinationIP}。

此案例目前為公開預覽狀態。



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>使用 Identity Protection 和 Microsoft Cloud App Security 進行融合

使用 advanced 多階段攻擊偵測，Azure Sentinel 支援下列結合 Azure Active Directory Identity Protection 和 Microsoft Cloud App Security 異常事件的案例：

- [不可能到達非典型位置，後面接著異常的 Office 365 活動](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [不熟悉的位置登入活動，後面接著異常的 Office 365 活動](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [來自受感染裝置的登入活動，後面接著異常的 Office 365 活動](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [從匿名 IP 位址登入活動，後面接著異常的 Office 365 活動](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [來自使用者的登入活動，其中含有流失的認證，後面接著異常的 Office 365 活動](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

您必須設定[Azure AD Identity Protection 資料連線器](connect-azure-ad-identity-protection.md)和[Cloud App Security](connect-cloud-app-security.md)連接器。

在接下來的描述中，Azure Sentinel 會顯示資料中的實際值，此頁面是以括弧括住的變數。 例如，帳戶的實際顯示名稱，而不是，而是 \<*account name*> 實際的數目，而不是 \<*number*> 。

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>不可能到達非典型位置，後面接著異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件會將不可能的移動到非典型位置的警示，從 Azure AD Identity Protection 和 Microsoft Cloud App Security 產生的異常 Office 365 警示中進行合併：

- **不可能移動到導致 Office 365 信箱外泄的非典型位置**
    
    此警示表示登入事件的指示 \<*account name*> ，從不可能移動到 \<*location*> 非典型位置，然後在使用者的收件匣上設定可疑的收件匣轉送規則。
    
    這可能表示帳戶遭到入侵，而且信箱正用來竊取組織中的資訊。 使用者 \<*account name*> 建立或更新了收件匣轉送規則，可將所有內送電子郵件轉寄至外部地址 \<*email address*> 。

- **不可能移動到導致可疑雲端應用程式管理活動的非典型位置**
    
    此警示表示登入事件的指示 \<*account name*> ，從不可能移動到 \<*location*> 非典型位置。
    
    接下來，帳戶會 \<*account name*> \<*number*> 在單一會話中執行系統管理活動。

- **不可能移動到導致大量檔案刪除的非典型位置**
    
    此警示表示登入事件 \<*account name*> \<*location*> （由非典型位置）的指示。 
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **不可能移動到導致大量檔案下載的非典型位置**
    
    此警示表示登入事件的指示 \<*account name*> ，從不可能移動到 \<*location*> 非典型位置。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **不可能移動到導致 Office 365 模擬的非典型位置**
    
    此警示表示登入事件的指示 \<*account name*> ，從不可能移動到 \<*location*> 非典型位置。 
    
    接下來，帳戶 \<*account name*> 在單一會話中執行了不尋常的模擬活動數量（ \<*number of activities*> ）。

- **不可能移動到導致大量檔案共用的非典型位置**
    
    此警示表示登入事件的指示 \<*account name*> ，從不可能移動到 \<*location*> 非典型位置。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一的檔案共用的帳戶 \<*number of*> 。

- **不可能移動到在雲端應用程式中導致勒索軟體的非典型位置**
    
    此警示表示登入事件的指示 \<*account name*> ，從不可能移動到 \<*location*> 非典型位置。 
    
    接下來，帳戶會上 \<*account name*> 傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    這種活動模式表示可能是勒索軟體攻擊。


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>不熟悉的位置登入活動，後面接著異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件會將不熟悉的位置警示的登入活動結合 Microsoft Cloud App Security 所產生的 Azure AD Identity Protection 和異常 Office 365 警示。

- **來自不熟悉的位置的登入事件，因而導致 Exchange Online 信箱外泄**
    
    此警示表示來自的登入事件 \<*account name*> \<*location*> 、不熟悉的位置，以及在使用者的收件匣上設定可疑的收件匣轉送規則。
    
    這可能表示帳戶遭到入侵，而且信箱正用來竊取組織中的資訊。 使用者 \<*account name*> 建立或更新了收件匣轉送規則，可將所有內送電子郵件轉寄至外部地址 \<*email address*> 。 

- **從不熟悉的位置登入事件，因而導致可疑的雲端應用程式系統管理活動**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，帳戶會 \<*account name*> \<*number of*> 在單一會話中執行系統管理活動。

- **來自不熟悉的位置的登入事件，因而導致大量檔案刪除**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **來自不熟悉的位置的登入事件，因而導致大量檔案下載**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **來自不熟悉的位置的登入事件，因而導致 Office 365 模擬**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。
    
    接下來，在 \<*account name*> \<*number of*> 單一會話中透過不同帳戶模擬的帳戶。

- **來自不熟悉的位置的登入事件，因而導致大量檔案共用**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一的檔案共用的帳戶 \<*number of*> 。

- **從在雲端應用程式中導致勒索軟體的不熟悉位置登入事件**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，帳戶會上 \<*account name*> 傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    這種活動模式表示可能是勒索軟體攻擊。

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>來自受感染裝置的登入活動，後面接著異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件，可將登入活動從受感染的裝置警示結合 Azure AD Identity Protection 和 Microsoft Cloud App Security 產生的異常 Office 365 警示：

- **來自受感染裝置的登入事件，因而導致 Office 365 信箱外泄**
    
    此警示表示 \<*account name*> 從可能受到惡意程式碼感染的裝置登入事件，隨後會在使用者的收件匣上設定可疑的收件匣轉送規則。
    
    這可能表示帳戶遭到入侵，而且信箱正用來竊取組織中的資訊。 使用者 \<*account name*> 建立或更新了收件匣轉送規則，可將所有內送電子郵件轉寄至外部地址 \<*email address*> 。 

- **來自受感染裝置的登入事件，因而導致可疑的雲端應用程式系統管理活動**
    
    此警示表示 \<*account name*> 從可能受到惡意程式碼感染的裝置登入事件。
    
    接下來，帳戶會 \<*account name*> \<*number of*> 在單一會話中執行系統管理活動。

- **來自受感染裝置的登入事件，因而導致大量檔案刪除**
    
    此警示表示 \<*account name*> 從可能受到惡意程式碼感染的裝置登入事件。 
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **來自受感染裝置的登入事件，因而導致大量檔案下載**
    
    此警示表示 \<*account name*> 從可能受到惡意程式碼感染的裝置登入事件。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **來自受感染裝置的登入事件，因而導致 Office 365 模擬**
    
    此警示表示 \<*account name*> 從可能受到惡意程式碼感染的裝置登入事件。 
    
    接下來，在 \<*account name*> \<*number of*> 單一會話中透過不同帳戶模擬的帳戶。

- **來自受感染裝置的登入事件，因而導致大量檔案共用**
    
    此警示表示 \<*account name*> 從可能受到惡意程式碼感染的裝置登入事件。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一的檔案共用的帳戶 \<*number of*> 。

- **來自受感染裝置的登入事件，導致雲端應用程式中的勒索軟體**
    
    此警示表示 \<*account name*> 從可能受到惡意程式碼感染的裝置登入事件。 
    
    接下來，帳戶會上 \<*account name*> 傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    這種活動模式表示可能是勒索軟體攻擊。

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>從匿名 IP 位址登入活動，後面接著異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件，可將來自匿名 IP 位址警示的登入活動與 Microsoft Cloud App Security 產生的 Azure AD Identity Protection 和異常 Office 365 警示結合：

- **來自採用 Office 365 信箱外泄之匿名 IP 位址的登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> ，隨後會在使用者的收件匣上設定可疑的收件匣轉送規則。
    
    這可能表示帳戶遭到入侵，而且信箱正用來竊取組織中的資訊。 使用者 \<*account name*> 建立或更新了收件匣轉送規則，可將所有內送電子郵件轉寄至外部地址 \<*email address*> 。 

- **來自匿名 IP 位址的登入事件，導致可疑的雲端應用程式系統管理活動**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，帳戶會 \<*account name*> \<*number of*> 在單一會話中執行系統管理活動。

- **從導致大量檔案刪除的匿名 IP 位址登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **從導致大量檔案下載的匿名 IP 位址登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **來自採用 Office 365 模擬之匿名 IP 位址的登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，在 \<*account name*> \<*number of*> 單一會話中透過不同帳戶模擬的帳戶。

- **從導致大量檔案共用的匿名 IP 位址登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一的檔案共用的帳戶 \<*number of*> 。

- **在雲端應用程式中從匿名 IP 位址到勒索軟體的登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，帳戶會上 \<*account name*> 傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    這種活動模式表示可能是勒索軟體攻擊。

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>來自使用者的登入活動，其中含有流失的認證，後面接著異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件，會將使用者的登入活動與洩漏的認證警示結合 Azure AD Identity Protection 和 Microsoft Cloud App Security 產生的異常 Office 365 警示：

- **來自使用者的登入事件，其認證會導致 Office 365 信箱外泄**
    
    此警示表示登入事件使用流失的 \<*account name*> 認證，後面接著在使用者的收件匣上設定可疑的收件匣轉送規則。 
    
    這可能表示帳戶遭到入侵，而且信箱正用來竊取組織中的資訊。 使用者 \<*account name*> 建立或更新了收件匣轉送規則，可將所有內送電子郵件轉寄至外部地址 \<*email address*> 。 

- **具有洩漏認證的使用者登入事件，因而導致可疑的雲端應用程式系統管理活動**
    
    此警示表示使用流失的認證登入事件 \<*account name*> 。
    
    接下來，帳戶會 \<*account name*> \<*number of*> 在單一會話中執行系統管理活動。

- **來自具有洩露認證的使用者登入事件，因而導致大量檔案刪除**
    
    此警示表示使用流失的認證登入事件 \<*account name*> 。
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **來自具有洩露認證的使用者登入事件，因而導致大量檔案下載**
    
    此警示表示使用流失的認證登入事件 \<*account name*> 。
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **來自使用者的登入事件，其認證會導致 Office 365 模擬**
    
    此警示表示使用流失的認證登入事件 \<*account name*> 。 
    
    接下來，在 \<*account name*> \<*number of*> 單一會話中透過不同帳戶模擬的帳戶。

- **具有洩漏認證的使用者登入事件，因而導致大量檔案共用**
    
    此警示表示使用流失的認證登入事件 \<*account name*> 。
    
    接下來，在 \<*account name*> 單一會話中，透過唯一的檔案共用的帳戶 \<*number of*> 。

- **在雲端應用程式中將認證洩漏給勒索軟體的使用者登入事件**
    
    此警示表示使用流失的認證登入事件 \<*account name*> 。 
    
    接下來，帳戶會上 \<*account name*> 傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    這種活動模式表示可能是勒索軟體攻擊。

## <a name="next-steps"></a>後續步驟

現在您已深入瞭解 advanced 多階段攻擊偵測，您可能會對下列快速入門感興趣，以瞭解如何查看您的資料和潛在威脅：[開始使用 Azure Sentinel](quickstart-get-visibility.md)。

如果您已準備好調查為您建立的事件，請參閱下列教學課程：[使用 Azure Sentinel 調查事件](tutorial-investigate-cases.md)。

