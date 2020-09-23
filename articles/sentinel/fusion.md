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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: ba872f221f3bde29f0bb48b04dc2259d3ab4938a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906289"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel 中的 Advanced 多階段攻擊偵測


> [!IMPORTANT]
> Azure Sentinel 中的一些融合功能目前處於 **公開預覽**狀態。
> 這些功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

藉由使用以機器學習為基礎的融合技術，Azure Sentinel 可以藉由識別在終止鏈的不同階段觀察到的異常行為和可疑活動的組合，來自動偵測多階段攻擊。 在這些探索的基礎上，Azure Sentinel 會產生不容易攔截的事件。 這些事件包含兩個或多個警示或活動。 根據設計，這些事件具有低量、高精確度且高嚴重性。

此偵測技術針對您的環境自訂，不僅可減少誤報的正面費率，還可以偵測具有有限或遺失資訊的攻擊。

## <a name="configuration-for-advanced-multistage-attack-detection"></a>進階多階段攻擊偵測組態

Azure Sentinel 預設會啟用此偵測。 若要檢查狀態，或在您使用替代解決方案來根據多個警示建立事件時將其停用，請使用下列指示：

1. 如果您尚未這麼做，請登入 [Azure 入口網站](https://portal.azure.com)。

1. 流覽至**Azure Sentinel**設定  >  **Configuration**  >  **分析**

1. 選取 [作用中**規則**]，然後藉由篩選**融合**規則類型的清單，在 [**名稱**] 資料行中找出**Advanced 多階段攻擊偵測**。 請檢查 [ **狀態** ] 資料行，確認是否已啟用或停用此偵測。

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. 若要變更狀態，請選取此專案，並在 [ **Advanced 多階段攻擊偵測** ] 分頁上，選取 [ **編輯**]。

1. 在 [ **規則建立嚮導]** 分頁上，系統會自動為您選取狀態的變更，因此請選取 **[下一步：檢查]**，然後再 **儲存**。 

 由於 **融合** 規則類型只包含一個無法修改的規則，因此規則範本不適用此規則類型。

> [!NOTE]
> Azure Sentinel 目前使用30天的歷程記錄資料來定型機器學習系統。 這項資料一律會使用 Microsoft 的金鑰進行加密，因為它通過機器學習管線。 但是，如果您在 Azure Sentinel 工作區中啟用 CMK，則不會使用客戶管理的金鑰來加密定型資料 [ (CMK) ](customer-managed-keys.md) 。 若要退出融合，請流覽至**Azure Sentinel**設定分析使用中   \>  **Configuration**   \>  ** \> 規則 \> Advanced 多階段攻擊偵測**，並在 [**狀態**] 欄位中選取 [停用] **。**

## <a name="attack-detection-scenarios"></a>攻擊偵測案例

下節列出依威脅分類分組的相互關聯案例類型，Azure Sentinel 尋找使用融合技術。

如先前所述，因為融合會將多個安全性警示與不同的產品相互關聯，以偵測 advanced 多階段攻擊，成功的融合偵測會在 [Azure Sentinel**事件**] 頁面上顯示為**融合事件**，而不會在**記錄**中顯示為**安全性警示**資料表中的**警示**。

若要啟用這些融合式攻擊偵測案例，所有列出的資料來源都必須使用相關聯的 Azure Sentinel 資料連線器來內嵌。

> [!NOTE]
> 其中有些案例處於 **公開預覽**狀態。 這些將會指出。

## <a name="compute-resource-abuse"></a>計算資源濫用

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>在可疑的 Azure Active Directory 登入之後執行多個 VM 建立活動
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 初始存取權，影響 

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、資源劫持 (T1496) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在 Azure AD 帳戶的可疑登入之後，在單一會話中建立了異常數目的 Vm。 這種類型的警示指出，融合事件描述中所注明的帳戶已遭到入侵，並用來建立新的 Vm 以進行未經授權的用途，例如執行加密的挖掘作業。 有多個 VM 建立活動警示的可疑 Azure AD 登入警示排列如下：

- **不可能移動至不尋常的位置，而導致多個 VM 建立活動**

- **從不熟悉的位置登入事件，導致多個 VM 建立活動**

- **來自受感染裝置的登入事件，導致多個 VM 建立活動**

- **來自匿名 IP 位址的登入事件，會導致多個 VM 建立活動**

- **使用者的登入事件，而洩漏的認證會導致多個 VM 建立活動**

## <a name="data-exfiltration"></a>資料外洩

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>在可疑的 Azure AD 登入之後遭到外泄的 Office 365 信箱

**MITRE ATT&CK 策略：** 初始存取、遭到外泄、收集

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、電子郵件集合 (T1114) 、自動化遭到外泄 (T1020) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在使用者的收件匣上設定可疑的收件匣轉寄規則，並在 Azure AD 帳戶中進行可疑的登入。 這項指示能讓使用者的帳戶 (在融合事件描述中記下) 已遭入侵，而且是用來在沒有真正使用者知識的情況下啟用信箱轉送規則，藉此竊取組織網路中的資料。 使用 Office 365 信箱遭到外泄警示的可疑 Azure AD 登入警示排列如下：

- **不可能移動到會導致 Office 365 信箱遭到外泄的非典型位置**

- **來自不熟悉位置的登入事件，會導致 Office 365 信箱遭到外泄**

- **來自受感染裝置的登入事件，導致 Office 365 信箱遭到外泄**

- **來自匿名 IP 位址的登入事件，會導致 Office 365 信箱遭到外泄**

- **從使用者登入事件，而洩漏的認證會導致 Office 365 信箱遭到外泄**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>可疑的 Azure AD 登入之後的大量檔案下載

**MITRE ATT&CK 策略：** 初始存取權，遭到外泄

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示使用者在 Azure AD 帳戶的可疑登入之後下載了異常數量的檔案。 這項指示可提供高度信賴度，指出融合事件描述中所述的帳戶已遭入侵，並已用來從您的組織網路竊取資料。 可疑的 Azure AD 登入警示與大量檔案下載警示的相片順序如下：  

- **不可能移動至不尋常的位置，而導致大量檔案下載**

- **從不熟悉的位置登入事件，導致大量檔案下載**

- **來自受感染裝置的登入事件，導致大量檔案下載**

- **從匿名 IP 登入事件，導致大量檔案下載**

- **使用者登入事件，而洩漏的認證導致大量檔案下載**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>可疑 Azure AD 登入之後的大量檔案共用

**MITRE ATT&CK 策略：** 初始存取權，遭到外泄

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、遭到外泄 Over Web Service (T1567) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件指出，在 Azure AD 帳戶的可疑登入之後，其他人共用超過特定閾值的一些檔案。 這項指示可提供高度信賴度，指出融合事件描述中所述的帳戶已遭到入侵，並藉由共用檔、試算表等檔案（如檔、試算表等），與未經授權的使用者共用惡意用途，以竊取組織網路中的資料。 可疑 Azure AD 登入警示與大量檔案共用警示的相片順序如下：  

- **不可能移動到導致大量檔案共用的非典型位置**

- **從不熟悉的位置登入事件，導致大量檔案共用**

- **來自受感染裝置的登入事件，導致大量檔案共用**

- **從導致大量檔案共用的匿名 IP 位址登入事件**

- **使用者登入事件，而洩漏的認證導致大量檔案共用**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>可疑的收件匣操作規則設定下列可疑 Azure AD 登入
此案例屬於這份清單中的兩個威脅分類： **資料遭到外泄** 和 **橫向移動**。 為了清楚起見，它會出現在這兩個區段中。

此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 初始存取，橫向移動，遭到外泄

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、內部魚叉式網路釣魚 (T1534) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在使用者的收件匣上設定了異常的收件匣規則，並在 Azure AD 帳戶的可疑登入之後設定。 這可提供高度信賴的指示，指出融合事件描述中所注明的帳戶已遭到入侵，並用於操作使用者的電子郵件收件匣規則以因應惡意用途。 攻擊者可能會嘗試從組織的網路竊取資料。 或者，攻擊者可能會嘗試從組織內產生網路釣魚電子郵件， (略過從外部來源傳送電子郵件的網路釣魚偵測機制，) 目的是取得額外使用者及/或特殊許可權帳戶的存取權。 可疑的 Azure AD 登入警示與可疑的收件匣操作規則警示的相片順序如下：  

- **不可能移動至不尋常的位置，而導致可疑的收件匣操作規則**

- **從不熟悉的位置登入事件，導致可疑的收件匣操作規則**

- **來自受感染裝置的登入事件，導致可疑的收件匣操作規則**

- **來自匿名 IP 位址的登入事件，導致可疑的收件匣操作規則**

- **使用者的登入事件，而洩漏的認證導致可疑的收件匣操作規則**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>在可疑 Azure AD 登入之後，多個 Power BI 報告共用活動 
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 初始存取權，遭到外泄 

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、遭到外泄 Over Web Service (T1567) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在 Azure AD 帳戶發生可疑登入之後，會在單一會話中共用異常的 Power BI 報表數目。 這項指示可提供高度信賴度，指出融合事件描述中所述的帳戶已遭到入侵，並藉由與未經授權的使用者共用 Power BI 報表以因應惡意目的，而用來竊取組織網路中的資料。 具有多個 Power BI 報告共用活動的可疑 Azure AD 登入警示排列如下：  

- **不可能移動至不尋常的位置，導致多個 Power BI 報表共用活動**

- **從不熟悉的位置登入事件，會導致多個 Power BI 報告共用活動**

- **來自受感染裝置的登入事件，會導致多個 Power BI 報告共用活動**

- **來自匿名 IP 位址的登入事件，會導致多個 Power BI 報告共用活動**

- **具有洩漏認證的使用者登入事件，會導致多個 Power BI 報告共用活動**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>可疑 Azure AD 登入之後，共用可疑的 Power BI 報告
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 初始存取權，遭到外泄 

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、遭到外泄 Over Web Service (T1567) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在 Azure AD 帳戶發生可疑的登入之後，就會發生可疑的 Power BI 報告共用活動。 共用活動識別為可疑，因為 Power BI 報表包含使用自然語言處理所識別的機密資訊，而且因為它是與外部電子郵件地址共用、已發佈至 web，或是以快照集的形式傳遞到外部訂閱的電子郵件地址。 這項警示指出，融合事件描述中所注明的帳戶已遭入侵，並已用來與未經授權的使用者共用 Power BI 報表，以竊取您組織的機密資料。 可疑的 Azure AD 登入警示與可疑 Power BI 報告共用的相片順序如下：  

- **不可能移動至不尋常的位置，導致可疑的 Power BI 報表共用**

- **從不熟悉的位置登入事件，導致可疑的 Power BI 報告共用**

- **來自受感染裝置的登入事件，導致可疑的 Power BI 報告共用**

- **從匿名 IP 位址登入事件，導致可疑的 Power BI 報告共用**

- **具有洩漏認證的使用者登入事件，導致可疑的 Power BI 報告共用**

## <a name="data-destruction"></a>資料損毀

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>在可疑的 Azure AD 登入之後刪除大量檔案

**MITRE ATT&CK 策略：** 初始存取權，影響

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、Data 毀 (T1485) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在 Azure AD 帳戶的可疑登入之後刪除了異常的唯一檔案數目。 這會指出融合事件描述中所注明的帳戶可能已遭入侵，並被用來損毀資料以因應惡意用途。 可疑 Azure AD 登入警示與大量檔案刪除警示的相片順序如下：  

- **不可能移動至不尋常的位置，而導致大量檔案刪除**

- **從不熟悉的位置登入事件，導致大量檔案刪除**

- **來自受感染裝置的登入事件，導致大量檔案刪除**

- **來自匿名 IP 位址的登入事件，導致大量檔案刪除**

- **使用者登入事件，而洩漏的認證導致大量檔案刪除**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>可疑的 Azure AD 登入後，可疑的電子郵件刪除活動
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 初始存取權，影響 

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、Data 毀 (T1485) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在 Azure AD 帳戶的可疑登入之後，在單一會話中刪除了異常的電子郵件數目。 這會指出融合事件描述中所注明的帳戶可能已遭入侵，並被用來損毀資料以因應惡意用途，例如損害組織或隱藏垃圾郵件相關的電子郵件活動。 可疑的 Azure AD 登入警示與可疑的電子郵件刪除活動警示的相片順序如下：   

- **不可能移動至不尋常的位置，而導致可疑的電子郵件刪除活動**

- **從不熟悉的位置登入事件，導致可疑的電子郵件刪除活動**

- **來自受感染裝置的登入事件，導致可疑的電子郵件刪除活動**

- **從匿名 IP 位址登入事件，導致可疑的電子郵件刪除活動**

- **從使用者登入事件，而洩漏的認證導致可疑的電子郵件刪除活動**

## <a name="denial-of-service"></a>拒絕服務

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>在可疑的 Azure AD 登入之後，多個 VM 刪除活動
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 初始存取權，影響

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、Endpoint 阻斷服務 (T1499) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在 Azure AD 帳戶的可疑登入之後，在單一會話中刪除了異常數目的 Vm。 這項指示可提供高度信賴度，指出融合事件描述中所述的帳戶已遭到入侵，並被用來嘗試中斷或終結組織的雲端環境。 具有多個 VM 刪除活動警示的可疑 Azure AD 登入警示的相片順序如下：  

- **不可能移動到會導致多個 VM 刪除活動的非典型位置**

- **從不熟悉的位置登入事件，導致多個 VM 刪除活動**

- **來自受感染裝置的登入事件，導致多個 VM 刪除活動**

- **來自匿名 IP 位址的登入事件，會導致多個 VM 刪除活動**

- **從使用者登入事件，而洩漏的認證會導致多個 VM 刪除活動**

## <a name="lateral-movement"></a>橫向移動

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>在可疑 Azure AD 登入之後的 Office 365 模擬

**MITRE ATT&CK 策略：** 初始存取，橫向移動

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、內部魚叉式網路釣魚 (T1534) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在 Azure AD 帳戶的可疑登入之後，發生異常的模擬動作數目。 在某些軟體中，有一些選項可讓使用者模擬其他使用者。 例如，電子郵件服務可讓使用者授權其他使用者代表他們傳送電子郵件。 此警示表示有更高的信賴度，融合事件描述中所注明的帳戶已遭到入侵，並被用來進行惡意用途的模擬活動，例如傳送惡意程式碼發佈或橫向移動的網路釣魚電子郵件。 使用 Office 365 模擬警示的可疑 Azure AD 登入警示排列如下：  

- **不可能移動到導致 Office 365 模擬的非典型位置**

- **來自不熟悉的位置的登入事件，會導致 Office 365 模擬**

- **來自受感染裝置的登入事件，導致 Office 365 模擬**

- **從指向 Office 365 模擬的匿名 IP 位址登入事件**

- **從使用者登入事件，而洩漏的認證會導致 Office 365 模擬**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>可疑的收件匣操作規則設定下列可疑 Azure AD 登入
此案例屬於這份清單中的兩個威脅分類： **橫向移動** 和 **資料遭到外泄**。 為了清楚起見，它會出現在這兩個區段中。

此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 初始存取，橫向移動，遭到外泄

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) 、內部魚叉式網路釣魚 (T1534) 、自動化遭到外泄 (T1020) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在使用者的收件匣上設定了異常的收件匣規則，並在 Azure AD 帳戶的可疑登入之後設定。 這項指示可提供高信賴度，指出融合事件描述中所注明的帳戶已遭到入侵，並用於操作使用者的電子郵件收件匣規則以因應惡意用途。 攻擊者可能會嘗試從組織的網路竊取資料。 或者，攻擊者可能會嘗試從組織內產生網路釣魚電子郵件， (略過從外部來源傳送電子郵件的網路釣魚偵測機制，) 目的是取得額外使用者及/或特殊許可權帳戶的存取權。 可疑的 Azure AD 登入警示與可疑的收件匣操作規則警示的相片順序如下：

- **不可能移動至不尋常的位置，而導致可疑的收件匣操作規則**

- **從不熟悉的位置登入事件，導致可疑的收件匣操作規則**

- **來自受感染裝置的登入事件，導致可疑的收件匣操作規則**

- **來自匿名 IP 位址的登入事件，導致可疑的收件匣操作規則**

- **使用者的登入事件，而洩漏的認證導致可疑的收件匣操作規則**

## <a name="malicious-administrative-activity"></a>惡意的系統管理活動

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>下列可疑的雲端應用程式系統管理活動：可疑的 Azure AD 登入

**MITRE ATT&CK 策略：** 初始存取、持續性、防禦規避、橫向移動、收集、遭到外泄和影響

**MITRE ATT&CK 技術：** N/A

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件指出，在單一會話中執行了異常數目的系統管理活動，並遵循可疑的 Azure AD 從同一個帳戶登入。 這會指出融合事件描述中所注明的帳戶可能已遭入侵，並已用來透過惡意意圖進行任何數量的未經授權系統管理動作。 這也表示具有系統管理許可權的帳戶可能已遭入侵。 可疑的「雲端應用程式」系統管理活動警示的可疑 Azure AD 登入警示排列如下：  

- **不可能移動至不尋常的位置，而導致可疑的雲端應用程式系統管理活動**

- **從不熟悉的位置登入事件，導致可疑的雲端應用程式系統管理活動**

- **來自受感染裝置的登入事件，導致可疑的雲端應用程式系統管理活動**

- **來自匿名 IP 位址的登入事件，導致可疑的雲端應用程式系統管理活動**

- **從使用者登入事件，而洩漏的認證導致可疑的雲端應用程式系統管理活動**

## <a name="malicious-execution-with-legitimate-process"></a>使用合法程式進行惡意執行

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell 進行了可疑的網路連線，後面接著 Palo Alto Networks 防火牆旗標的異常流量。
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 執行

**MITRE ATT&CK 技術：** 命令與腳本解譯器 (T1059) 

**資料連線器來源：** 適用于端點 (的 Microsoft Defender 先前 Microsoft Defender 進階威脅防護或 MDATP) ，Palo Alto Networks 

**描述：** 此類型的融合事件表示已透過 PowerShell 命令提出輸出連線要求，並遵循該 Palo Alto Networks 防火牆偵測到的異常輸入活動。 這會指出攻擊者可能取得您網路的存取權，並嘗試執行惡意動作。 遵循此模式的 PowerShell 連接嘗試可能會指出惡意程式碼命令和控制活動、下載其他惡意程式碼的要求，或攻擊者建立遠端互動式訪問。 如同所有「離 land」攻擊，此活動可能是合法的 PowerShell 用途。 不過，PowerShell 命令執行後面接著可疑的輸入防火牆活動，可提高 PowerShell 以惡意方式使用的信心，並應進一步調查。 在 Palo Alto 記錄中，Azure Sentinel 著重于 [威脅記錄](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，並在允許威脅時，將流量視為可疑 (可疑的資料、檔案、氾濫、封包、掃描、間諜軟體、url、病毒、弱點、wildfire 病毒、野火) 。 另請參考對應到融合事件描述中所列 [威脅/內容類型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威脅記錄檔，以取得其他警示詳細資料。

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>可疑的遠端 WMI 執行，後面接著 Palo Alto Networks 防火牆標示的異常流量
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 執行，探索

**MITRE ATT&CK 技術：** Windows Management Instrumentation (T1047) 

**資料連線器來源：** 適用于端點 (的 Microsoft Defender 先前 MDATP) ，Palo Alto Networks 

**描述：** 此類型的融合事件表示 Windows 管理介面 (WMI) 命令已從遠端在系統上執行，並遵循該 Palo Alto Networks 防火牆偵測到的可疑輸入活動。 這會指出攻擊者可能已取得您網路的存取權，而且正在嘗試橫向移動、提升許可權，以及/或執行惡意承載。 如同所有「離 land」攻擊，此活動可能是合法的 WMI 使用。 但是，執行遠端 WMI 命令之後，如果出現可疑的輸入防火牆活動，就會讓 WMI 以惡意方式來使用，並進一步調查。 在 Palo Alto 記錄中，Azure Sentinel 著重于 [威脅記錄](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，並在允許威脅時，將流量視為可疑 (可疑的資料、檔案、氾濫、封包、掃描、間諜軟體、url、病毒、弱點、wildfire 病毒、野火) 。 另請參考對應到融合事件描述中所列 [威脅/內容類型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威脅記錄檔，以取得其他警示詳細資料。

## <a name="malware-c2-or-download"></a>惡意程式碼 C2 或下載

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 匿名化服務的網路要求，後面接著 Palo Alto Networks 防火牆標示的異常流量。
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 命令與控制

**MITRE ATT&CK 技術：** 加密通道 (T1573) 、Proxy (T1090) 

**資料連線器來源：** 適用于端點 (的 Microsoft Defender 先前 MDATP) ，Palo Alto Networks 

**描述：** 此類型的融合事件表示已對 TOR 匿名化服務發出輸出連線要求，並在之後，Palo Alto Networks 防火牆偵測到異常的輸入活動。 這會指出攻擊者可能取得您網路的存取權，並嘗試隱藏其動作和意圖。 遵循此模式的 TOR 網路連線可能會指出惡意程式碼命令和控制活動、下載其他惡意程式碼的要求，或建立遠端互動式訪問的攻擊者。 在 Palo Alto 記錄中，Azure Sentinel 著重于 [威脅記錄](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，並在允許威脅時，將流量視為可疑 (可疑的資料、檔案、氾濫、封包、掃描、間諜軟體、url、病毒、弱點、wildfire 病毒、野火) 。 另請參考對應到融合事件描述中所列 [威脅/內容類型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威脅記錄檔，以取得其他警示詳細資料。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>具有未授權存取歷程記錄的 IP 輸出連線，後面接著 Palo Alto Networks 防火牆標示的異常流量
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 命令與控制

**MITRE ATT&CK 技術：** 不適用

**資料連線器來源：** 適用于端點 (的 Microsoft Defender 先前 MDATP) ，Palo Alto Networks 

**描述：** 此類型的融合事件表示已建立連至 IP 位址的輸出連線，並有未經授權的存取嘗試，並遵循該 Palo Alto Networks 防火牆偵測到的異常活動。 這會指出攻擊者可能取得您網路的存取權。 遵循此模式的連接嘗試可能會指出惡意程式碼命令和控制活動、下載其他惡意程式碼的要求，或攻擊者建立遠端互動式訪問。 在 Palo Alto 記錄中，Azure Sentinel 著重于 [威脅記錄](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，並在允許威脅時，將流量視為可疑 (可疑的資料、檔案、氾濫、封包、掃描、間諜軟體、url、病毒、弱點、wildfire 病毒、野火) 。 另請參考對應到融合事件描述中所列 [威脅/內容類型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威脅記錄檔，以取得其他警示詳細資料。

## <a name="ransomware"></a>勒索軟體

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>在可疑的 Azure AD 登入之後執行勒索軟體

**MITRE ATT&CK 策略：** 初始存取權，影響

**MITRE ATT&CK 技術：** 有效的帳戶 (T1078) ， (T1486 的影響加密資料) 

**資料連線器來源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**描述：** 此類型的融合事件表示在 Azure AD 帳戶的可疑登入之後，偵測到指出勒索軟體攻擊的異常使用者行為。 這項指示可提供高度信賴度，指出融合事件描述中所述的帳戶已遭到入侵，並且用來加密資料，以 extorting 資料擁有者或拒絕資料擁有者對其資料的存取。 可疑的 Azure AD 登入警示與勒索軟體執行警示的相片順序如下：  

- **不可能移動到在雲端應用程式中導致勒索軟體的非典型位置**

- **從不熟悉的位置登入事件，導致雲端應用程式中的勒索軟體**

- **來自受感染裝置的登入事件，導致雲端應用程式中的勒索軟體**

- **從導致 cloud app 中勒索軟體的匿名 IP 位址登入事件**

- **在雲端應用程式中導致勒索軟體的使用者登入事件已洩漏**

## <a name="remote-exploitation"></a>遠端攻擊

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>可疑的攻擊架構使用，後面接著 Palo Alto Networks 防火牆標示的異常流量
此案例目前處於 **公開預覽**狀態。

**MITRE ATT&CK 策略：** 初始存取、執行、橫向移動、許可權提升

**MITRE ATT&CK 技術：** 利用公眾對應的應用程式 (T1190) 、利用用戶端執行 (T1203) 、利用遠端服務 (T1210) 、惡意探索許可權擴大 (T1068) 

**資料連線器來源：** 適用于端點 (的 Microsoft Defender 先前 MDATP) ，Palo Alto Networks 

**描述：** 此類型的融合事件表示偵測不到的通訊協定（類似于使用 Metasploit 等攻擊架構）的非標準用法，以及之後，Palo Alto Networks 防火牆偵測到可疑的輸入活動。 這可能是因為攻擊者已利用服務來取得網路資源的存取權，或攻擊者已經取得存取權，並嘗試進一步利用可用的系統/服務，以橫向及/或提升許可權。 在 Palo Alto 記錄中，Azure Sentinel 著重于 [威脅記錄](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，並在允許威脅時，將流量視為可疑 (可疑的資料、檔案、氾濫、封包、掃描、間諜軟體、url、病毒、弱點、wildfire 病毒、野火) 。 另請參考對應到融合事件描述中所列 [威脅/內容類型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威脅記錄檔，以取得其他警示詳細資料。

## <a name="next-steps"></a>下一步

現在您已瞭解有關 advanced 多階段攻擊偵測的詳細資訊，您可能會對下列快速入門感興趣，以瞭解如何查看您的資料和潛在威脅： [開始使用 Azure Sentinel](quickstart-get-visibility.md)。

如果您已經準備好調查為您建立的事件，請參閱下列教學課程： [使用 Azure Sentinel 調查事件](tutorial-investigate-cases.md)。

