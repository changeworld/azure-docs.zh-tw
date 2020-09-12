---
title: Azure Sentinel 中的 Advanced 多階段攻擊偵測
description: 使用 Azure Sentinel 中的融合技術來減少警示疲勞，並根據 advanced 多階段攻擊偵測來建立可採取動作的事件。
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
ms.openlocfilehash: 1f415294c77b743996993f1f00be45e36f9d6002
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660670"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel 中的 Advanced 多階段攻擊偵測


> [!IMPORTANT]
> Azure Sentinel 中的一些融合功能目前處於公開預覽狀態。
> 這些功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



藉由使用以機器學習為基礎的融合技術，Azure Sentinel 可以結合在終止鏈的不同階段觀察到的異常行為和可疑活動，來自動偵測多階段攻擊。 Azure Sentinel 然後產生可能很難捕捉的事件。 這些事件會將兩個或多個警示或活動。 根據設計，這些事件的數量很低、高精確度和高嚴重性。

針對您的環境進行自訂，這種偵測不僅能減少誤報的正面費率，還可以偵測到資訊有限或遺失的攻擊。

## <a name="configuration-for-advanced-multistage-attack-detection"></a>進階多階段攻擊偵測組態

Azure Sentinel 預設會啟用此偵測。 若要檢查狀態，或將它停用，可能是因為您使用替代解決方案來根據多個警示建立事件，請使用下列指示：

1. 如果您尚未這麼做，請登入 [Azure 入口網站](https://portal.azure.com)。

2. 流覽至**Azure Sentinel**設定  >  **Configuration**  >  **分析**

3. 選取 [作用中**規則**]，然後在 [**名稱**] 欄中找出**Advanced 多階段攻擊偵測**。 請檢查 [ **狀態** ] 資料行，確認是否已啟用或停用此偵測。

4. 若要變更狀態，請選取此專案，並在 [ **Advanced 多階段攻擊偵測** ] 分頁上，選取 [ **編輯**]。

5. 在 [ **規則建立嚮導]** 分頁上，系統會自動為您選取狀態的變更，因此請選取 **[下一步：檢查]**，然後再 **儲存**。 

規則範本不適用於 advanced 多階段攻擊偵測。

> [!NOTE]
> Azure Sentinel 目前使用30天的歷程記錄資料來定型機器學習系統。 這項資料一律會使用 Microsoft 的金鑰進行加密，因為它通過機器學習管線。 但是，如果您在 Azure Sentinel 工作區中啟用 CMK，則不會使用客戶管理的金鑰來加密定型資料 [ (CMK) ](customer-managed-keys.md) 。 若要退出融合，請流覽至**Azure Sentinel**設定分析使用中   \>  **Configuration**   \>  ** \> 規則 \> Advanced 多階段攻擊偵測**，並在 [**狀態**] 欄位中選取 [停用] **。**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>使用 Palo Alto Networks 和適用于端點的 Microsoft Defender (之前的 Microsoft Defender ATP 進行融合) 

這些案例結合了安全性分析師所使用的兩個基本記錄：來自 Microsoft Defender for Endpoint 的 Palo Alto Networks 和端點偵測記錄的防火牆記錄。 在下列所有案例中，在包含外部 IP 位址的結束點中偵測到可疑的活動，然後再由外部 IP 位址的異常流量傳回防火牆。 在 Palo Alto 記錄中，Azure Sentinel 著重于 [威脅記錄](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，並在允許威脅時，將流量視為可疑 (可疑的資料、檔案、氾濫、封包、掃描、間諜軟體、url、病毒、弱點、wildfire 病毒、野火) 。

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 匿名化服務的網路要求，後面接著 Palo Alto Networks 防火牆標示的異常流量。

在此案例中，Azure Sentinel 首先會偵測到 Microsoft Defender for Endpoint (之前的 Microsoft Defender for ATP) 偵測到導致異常活動的 TOR 匿名化服務的網路要求。 這是在 {time} 以 SID 識別碼 {SID} 的帳戶 {account name} 底下起始。 連線的連出 IP 位址為 {IndividualIp}。
然後，在 {TimeGenerated} 的 Palo Alto Networks 防火牆偵測到不尋常的活動。 這表示惡意流量進入您的網路。網路流量的目的地 IP 位址為 {DestinationIP}。

此案例目前處於公開預覽狀態。


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell 進行了可疑的網路連線，後面接著 Palo Alto Networks 防火牆標示的異常流量。

在此案例中，Azure Sentinel 首先會偵測到 Microsoft Defender for Endpoint (先前為適用于 ATP 的 Microsoft Defender) 偵測到 PowerShell 進行了可疑的網路連線，而導致 Palo Alto 網路防火牆偵測到的異常活動。 此帳戶是由具有 SID 識別碼 {SID} 的帳戶 {account name} 于 {time} 起始。 連線的連出 IP 位址為 {IndividualIp}。 然後，在 {TimeGenerated} 的 Palo Alto Networks 防火牆偵測到不尋常的活動。 這表示惡意流量進入您的網路。 網路流量的目的地 IP 位址為 {DestinationIP}。

此案例目前處於公開預覽狀態。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>具有未授權存取歷程記錄的 IP 輸出連線，後面接著 Palo Alto Networks 防火牆標示的異常流量

在此案例中，Azure Sentinel 會偵測到 Microsoft Defender for Endpoint (先前為適用于 ATP 的 Microsoft Defender) 偵測到 IP 位址的輸出連線，並有未經授權的存取嘗試，導致 Palo Alto Networks 防火牆偵測到異常的活動。 此帳戶是由具有 SID 識別碼 {SID} 的帳戶 {account name} 于 {time} 起始。 連線的連出 IP 位址為 {IndividualIp}。 在此之後，Palo Alto Networks 防火牆偵測到 {TimeGenerated} 的異常活動。 這表示惡意流量進入您的網路。 網路流量的目的地 IP 位址為 {DestinationIP}。

此案例目前處於公開預覽狀態。



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>使用 Identity Protection 和 Microsoft Cloud App Security 進行融合

使用 advanced 多階段攻擊偵測，Azure Sentinel 支援下列案例，這些案例會結合 Azure Active Directory Identity Protection 和 Microsoft Cloud App Security 的異常事件：

- [不可能移動至非典型的位置，然後再移動異常的 Office 365 活動](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [不熟悉的位置的登入活動，後面接著異常的 Office 365 活動](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [來自受感染裝置的登入活動，後面接著異常的 Office 365 活動](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [來自匿名 IP 位址的登入活動，後面接著異常的 Office 365 活動](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [從使用者登入活動，並在後面加上異常的 Office 365 活動](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

您必須設定 [Azure AD Identity Protection data connector](connect-azure-ad-identity-protection.md) 和 [Cloud App Security](connect-cloud-app-security.md) 連接器。

在接下來的描述中，Azure Sentinel 將會顯示此頁面上所代表之資料的實際值，作為括弧中的變數。 例如，帳戶的實際顯示名稱，而不是 \<*account name*> ，而是實際的數位，而不是 \<*number*> 。

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>不可能移動至非典型的位置，然後再移動異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件，會將不可能的移動從 Azure AD Identity Protection 和 Microsoft Cloud App Security 產生的異常 Office 365 警示結合為非典型位置警示：

- **不可能移動到會導致 Office 365 信箱遭到外泄的非典型位置**
    
    此警示表示 \<*account name*>  從不可能的移動到的登入事件 \<*location*> 、非典型的位置，然後在使用者的收件匣設定了可疑的收件匣轉送規則。
    
    這可能表示帳戶已遭盜用，且已使用信箱來竊取您組織的資訊。 使用者 \<*account name*> 建立或更新了可將所有內送電子郵件轉寄至外部地址的收件匣轉寄規則 \<*email address*> 。

- **不可能移動至不尋常的位置，而導致可疑的雲端應用程式系統管理活動**
    
    此警示表示 \<*account name*> 從不可能的移動到非典型位置的登入事件 \<*location*> 。
    
    接下來，帳戶會 \<*account name*> \<*number*> 在單一會話中透過系統管理活動執行。

- **不可能移動至不尋常的位置，而導致大量檔案刪除**
    
    此警示表示「登入事件」 \<*account name*> \<*location*> （非典型位置）。 
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **不可能移動至不尋常的位置，而導致大量檔案下載**
    
    此警示表示 \<*account name*> 從不可能的移動到非典型位置的登入事件 \<*location*> 。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **不可能移動到會導致 Office 365 模擬的非典型位置**
    
    此警示表示 \<*account name*> 從不可能的移動到非典型位置的登入事件 \<*location*> 。 
    
    接下來，帳戶 \<*account name*> 在單一會話中執行的模擬活動) 不尋常 (量 \<*number of activities*> 。

- **不可能移動至不尋常的位置，而導致大量檔案共用**
    
    此警示表示 \<*account name*> 從不可能的移動到非典型位置的登入事件 \<*location*> 。 
    
    接下來是 \<*account name*> \<*number of*> 在單一會話中，透過唯一檔案共用的帳戶。

- **不可能移動到在雲端應用程式中導致勒索軟體的非典型位置**
    
    此警示表示 \<*account name*> 從不可能的移動到非典型位置的登入事件 \<*location*> 。 
    
    接下來，帳戶 \<*account name*> 上傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    此活動模式表示潛在的勒索軟體攻擊。


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>不熟悉的位置的登入活動，後面接著異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件，其中結合了 Azure AD Identity Protection 和 Microsoft Cloud App Security 所產生的異常 Office 365 警示的登入活動，不熟悉的位置警示。

- **從導致 Exchange Online 信箱遭到外泄的不熟悉位置登入事件**
    
    此警示表示在 \<*account name*> \<*location*> 使用者的收件匣上設定登入事件的方式，不熟悉的位置，後面接著可疑的收件匣轉送規則。
    
    這可能表示帳戶已遭盜用，且已使用信箱來竊取您組織的資訊。 使用者 \<*account name*> 建立或更新了可將所有內送電子郵件轉寄至外部地址的收件匣轉寄規則 \<*email address*> 。 

- **從不熟悉的位置登入事件，導致可疑的雲端應用程式系統管理活動**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，帳戶會 \<*account name*> \<*number of*> 在單一會話中透過系統管理活動執行。

- **從不熟悉的位置登入事件，導致大量檔案刪除**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **從不熟悉的位置登入事件，導致大量檔案下載**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **來自不熟悉的位置的登入事件，會導致 Office 365 模擬**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。
    
    接下來，在 \<*account name*> \<*number of*> 單一會話中透過不同帳戶模擬的帳戶。

- **從不熟悉的位置登入事件，導致大量檔案共用**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來是 \<*account name*> \<*number of*> 在單一會話中，透過唯一檔案共用的帳戶。

- **從不熟悉的位置登入事件，導致雲端應用程式中的勒索軟體**
    
    此警示表示來自的登入事件，不熟悉的 \<*account name*> \<*location*> 位置。 
    
    接下來，帳戶 \<*account name*> 上傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    此活動模式表示潛在的勒索軟體攻擊。

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>來自受感染裝置的登入活動，後面接著異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件，可將來自受感染裝置警示的登入活動與 Microsoft Cloud App Security 產生的 Azure AD Identity Protection 和異常的 Office 365 警示結合：

- **來自受感染裝置的登入事件，導致 Office 365 信箱遭到外泄**
    
    此警示表示 \<*account name*> 從可能受惡意程式碼感染的裝置登入事件，後面接著在使用者的收件匣設定了可疑的收件匣轉送規則。
    
    這可能表示帳戶已遭盜用，且已使用信箱來竊取您組織的資訊。 使用者 \<*account name*> 建立或更新了可將所有內送電子郵件轉寄至外部地址的收件匣轉寄規則 \<*email address*> 。 

- **來自受感染裝置的登入事件，導致可疑的雲端應用程式系統管理活動**
    
    此警示表示 \<*account name*> 從可能受惡意程式碼感染的裝置登入事件。
    
    接下來，帳戶會 \<*account name*> \<*number of*> 在單一會話中透過系統管理活動執行。

- **來自受感染裝置的登入事件，導致大量檔案刪除**
    
    此警示表示 \<*account name*> 從可能受惡意程式碼感染的裝置登入事件。 
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **來自受感染裝置的登入事件，導致大量檔案下載**
    
    此警示表示 \<*account name*> 從可能受惡意程式碼感染的裝置登入事件。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **來自受感染裝置的登入事件，導致 Office 365 模擬**
    
    此警示表示 \<*account name*> 從可能受惡意程式碼感染的裝置登入事件。 
    
    接下來，在 \<*account name*> \<*number of*> 單一會話中透過不同帳戶模擬的帳戶。

- **來自受感染裝置的登入事件，導致大量檔案共用**
    
    此警示表示 \<*account name*> 從可能受惡意程式碼感染的裝置登入事件。 
    
    接下來是 \<*account name*> \<*number of*> 在單一會話中，透過唯一檔案共用的帳戶。

- **來自受感染裝置的登入事件，導致雲端應用程式中的勒索軟體**
    
    此警示表示 \<*account name*> 從可能受惡意程式碼感染的裝置登入事件。 
    
    接下來，帳戶 \<*account name*> 上傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    此活動模式表示潛在的勒索軟體攻擊。

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>來自匿名 IP 位址的登入活動，後面接著異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件，可將來自匿名 IP 位址警示的登入活動，從 Microsoft Cloud App Security 產生的 Azure AD Identity Protection 和異常的 Office 365 警示合併：

- **來自匿名 IP 位址的登入事件，會導致 Office 365 信箱遭到外泄**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> ，後面接著在使用者的收件匣設定了可疑的收件匣轉送規則。
    
    這可能表示帳戶已遭盜用，且已使用信箱來竊取您組織的資訊。 使用者 \<*account name*> 建立或更新了可將所有內送電子郵件轉寄至外部地址的收件匣轉寄規則 \<*email address*> 。 

- **來自匿名 IP 位址的登入事件，導致可疑的雲端應用程式系統管理活動**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，帳戶會 \<*account name*> \<*number of*> 在單一會話中透過系統管理活動執行。

- **來自匿名 IP 位址的登入事件，導致大量檔案刪除**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **來自匿名 IP 位址的登入事件，導致大量檔案下載**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **從指向 Office 365 模擬的匿名 IP 位址登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，在 \<*account name*> \<*number of*> 單一會話中透過不同帳戶模擬的帳戶。

- **從導致大量檔案共用的匿名 IP 位址登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來是 \<*account name*> \<*number of*> 在單一會話中，透過唯一檔案共用的帳戶。

- **從匿名 IP 位址登入至 cloud app 中勒索軟體的登入事件**
    
    此警示表示 \<*account name*> 來自匿名 PROXY IP 位址的登入事件 \<*IP address*> 。 
    
    接下來，帳戶 \<*account name*> 上傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    此活動模式表示潛在的勒索軟體攻擊。

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>從使用者登入活動，並在後面加上異常的 Office 365 活動

有七個可能的 Azure Sentinel 事件，會將來自使用者的登入活動與 Microsoft Cloud App Security 所產生的 Azure AD Identity Protection 和異常的 Office 365 警示合併。

- **從使用者登入事件，而洩漏的認證會導致 Office 365 信箱遭到外泄**
    
    此警示表示使用洩漏的認證登入事件 \<*account name*> ，後面接著在使用者的收件匣設定了可疑的收件匣轉送規則。 
    
    這可能表示帳戶已遭盜用，且已使用信箱來竊取您組織的資訊。 使用者 \<*account name*> 建立或更新了可將所有內送電子郵件轉寄至外部地址的收件匣轉寄規則 \<*email address*> 。 

- **從使用者登入事件，而洩漏的認證導致可疑的雲端應用程式系統管理活動**
    
    此警示表示使用洩漏的認證來登入事件 \<*account name*> 。
    
    接下來，帳戶會 \<*account name*> \<*number of*> 在單一會話中透過系統管理活動執行。

- **使用者登入事件，而洩漏的認證導致大量檔案刪除**
    
    此警示表示使用洩漏的認證來登入事件 \<*account name*> 。
    
    接下來，帳戶 \<*account name*> 會 \<*number of*> 在單一會話中刪除唯一的檔案。

- **使用者登入事件，而洩漏的認證導致大量檔案下載**
    
    此警示表示使用洩漏的認證來登入事件 \<*account name*> 。
    
    接下來，在 \<*account name*> 單一會話中，透過唯一檔案下載的帳戶 \<*number of*> 。

- **從使用者登入事件，而洩漏的認證會導致 Office 365 模擬**
    
    此警示表示使用洩漏的認證來登入事件 \<*account name*> 。 
    
    接下來，在 \<*account name*> \<*number of*> 單一會話中透過不同帳戶模擬的帳戶。

- **使用者登入事件，而洩漏的認證導致大量檔案共用**
    
    此警示表示使用洩漏的認證來登入事件 \<*account name*> 。
    
    接下來是 \<*account name*> \<*number of*> 在單一會話中，透過唯一檔案共用的帳戶。

- **在雲端應用程式中，將認證洩漏給勒索軟體的使用者登入事件**
    
    此警示表示使用洩漏的認證來登入事件 \<*account name*> 。 
    
    接下來，帳戶 \<*account name*> 上傳檔案 \<*number of*> ，並刪除檔案總數 \<*number of*> 。 
    
    此活動模式表示潛在的勒索軟體攻擊。

## <a name="next-steps"></a>接下來的步驟

現在您已瞭解有關 advanced 多階段攻擊偵測的詳細資訊，您可能會對下列快速入門感興趣，以瞭解如何查看您的資料和潛在威脅： [開始使用 Azure Sentinel](quickstart-get-visibility.md)。

如果您已經準備好調查為您建立的事件，請參閱下列教學課程： [使用 Azure Sentinel 調查事件](tutorial-investigate-cases.md)。

