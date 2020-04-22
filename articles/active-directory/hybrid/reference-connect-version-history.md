---
title: Azure AD Connect︰版本發行歷程記錄 | Microsoft Docs
description: 本文章列出 Azure AD Connect 和 Azure AD Sync 的所有版本
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/21/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a03a03557fbb2e71ff79ff42fd9d9c72cd5907c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770495"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect︰版本發行歷程記錄
Azure Active Directory (Azure AD) 團隊會定期以新的特性和功能更新 Azure AD Connect。 並非所有新增項目都適用於所有的對象。

本文旨在協助您追蹤已發行的版本，以及了解最新版本中的變更。

此表格為相關主題的清單：

主題 |  詳細資料
--------- | --------- |
從 Azure AD Connect 升級的步驟 | [從舊版升級到最新版本](how-to-upgrade-previous-version.md) Azure AD Connect 的多種方法。
所需的權限 | 有關應用更新所需的許可權,請參閱[帳戶和權限](reference-connect-accounts-permissions.md#upgrade)。
下載| [下載 Azure AD 連線](https://go.microsoft.com/fwlink/?LinkId=615771)。

>[!NOTE]
>發佈新版本的 Azure AD Connect 是一個過程,需要幾個品質控制步驟來確保服務的操作功能,而當我們通過此過程時,將更新新版本的版本號和發佈狀態以反映最新狀態。
在此過程中,版本版本號將在次要版本號位置顯示"X",如"1.3.X.0" - 這表明本文檔中的發行說明對於以"1.3"開頭的所有版本都有效。 一旦我們完成發佈過程,發佈版本號將更新到最近發佈的版本,發佈狀態將更新為"發佈下載和自動升級"。
並非所有版本的 Azure AD Connect 都可自動升級。 發行狀態會指出版本是否可自動升級或僅供下載。 如果您的 Azure AD Connect 伺服器上已啟用自動升級，則該伺服器將會自動升級為可自動升級的最新版 Azure AD Connect。 請注意，並非所有 Azure AD Connect 組態都符合自動升級的資格。 請遵循下列連結來深入了解[自動升級](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> 從 2020 年 11 月 1 日開始,我們將開始實施一個棄用過程,即 18 個月前發佈的 Azure AD Connect 版本將被棄用。 此時,我們將通過棄用 Azure AD Connect 的所有版本(2019 年 4 月 24 日發布)和舊版本來開始此過程,並且我們將在每次發佈新版本時繼續評估舊版本的 Azure AD Connect 的棄用。
>
> 您需要確保執行最新版本的 Azure AD Connect,以獲得最佳的支援體驗。 
>
>如果您運行了已棄用版本的 Azure AD Connect,則可能沒有最新的安全修補程式、性能改進、故障排除和診斷工具以及服務增強功能,如果您需要支援,我們可能無法為您提供組織所需的服務級別。
>
>如果已啟用 Azure AD 連接進行同步,您將很快自動開始接收運行狀況通知,這些通知會在運行舊版本之一時警告您即將棄用。
>
>請參閱[本文](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version),詳細瞭解如何將 Azure AD 連接到最新版本。

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>發行狀態
04/20/2020: 發佈供下載

### <a name="fixed-issues"></a>已修正的問題
如果從 AD 克隆了**In - 組聯接**規則,並且尚未**從 AD - 組通用**規則克隆 In,則此修補程式生成修復了內部版本 1.5.20.0 中的問題。

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>發行狀態
04/09/2020: 發佈供下載

### <a name="fixed-issues"></a>已修正的問題
如果啟用了組篩選功能並使用 mS-DS-一致性 Guid 作為源錨點,則此修補程式生成修復了內部版本 1.5.18.0 的問題。

> [!IMPORTANT]
> 如果您從 AD 複製了**In - 群組加入**同步規則,並且尚未從 AD 複製**的通用**同步規則並排程升級,則作為升級的一部分完成以下步驟:
> 1. 在升級期間,取消選中選項 **「在設定完成時啟動同步過程**」。。
> 2. 編輯複製的檔案同步規則並新增以下兩個轉換:
>     - 將直接流`objectGUID`設定`sourceAnchorBinary`為 。
>     - 將表示式流`ConvertToBase64([objectGUID])`設定`sourceAnchor`為 。     
> 3. 使用`Set-ADSyncScheduler -SyncCycleEnabled $true`啟用計畫程式。



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>發行狀態
04/02/2020: 發佈供下載

### <a name="functional-changes-adsyncautoupgrade"></a>功能變更 ADSync 自動升級 

- 添加了對組物件的 mS-DS-一致性 Guid 功能的支援。 這允許您在 AD 中的林之間移動群組或將組重新連接到 Azure AD,其中 AD 組物件 ID 已更改,例如,在災難後重建 AD 伺服器時。 有關詳細資訊,請參閱[在林之間移動組](how-to-connect-migrate-groups.md)。
- mS-DS-一致性Guid屬性會自動設置在所有同步的組上,您不必執行任何操作來啟用此功能。 
- 已刪除 Get-ADSyncRun 設定檔,因為它不再使用。 
- 更改了嘗試使用 AD DS 連接器帳戶的企業管理員或域管理員帳戶以提供更多上下文時看到的警告。 
- 添加了一個新的 cmdlet 以從連接器空間中刪除物件,將刪除舊的 CSDelete.exe 工具,並將其替換為新的"刪除-ADSyncCSObject cmdlet"。 刪除 ADSyncCSObject cmdlet 將 CsObject 作為輸入。 可以使用 Get-ADSyncCSObject cmdlet 檢索此物件。

>[!NOTE]
>舊的 CSDelete.exe 工具已被刪除,並取代為新的「刪除-ADSyncCSObject」cmdlet 

### <a name="fixed-issues"></a>已修正的問題

- 修復了組回寫林/OU 選擇器中的錯誤,用於在禁用該功能后重新運行 Azure AD 連接嚮導。 
- 引入了一個新的錯誤頁,如果缺少所需的 DCOM 註冊表值,則顯示該頁,該頁帶有新的幫助連結。 資訊也會寫入日誌檔。 
- 修復了創建 Azure 活動目錄同步帳戶的問題,其中啟用目錄擴展或小靈通可能會失敗,因為該帳戶在嘗試使用之前尚未在所有服務副本中傳播。 
- 修復了同步錯誤壓縮實用程式中未正確處理代理字元的錯誤。 
- 修復了自動升級中的一個錯誤,使伺服器處於計劃程式掛起狀態。 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>發行狀態
2019/12/9:下載版本。 無法通過自動升級提供。
### <a name="new-features-and-improvements"></a>新功能和改進
- 我們更新了 Azure AD 網域服務的密碼哈希同步,以正確考慮 Kerberos 哈希中的填充。  這將在從 AAD 到 Azure AD 網域服務的密碼同步期間提供性能改進。
- 我們添加了對身份驗證代理和服務總線之間可靠會話的支援。
- 此版本強制 TLS 1.2 用於身份驗證代理和雲端服務之間的通訊。
- 我們為身份驗證代理和雲端服務之間的 Web 套接字連接添加了 DNS 緩存。
- 我們添加了從雲定位特定代理以測試代理連接功能的能力。

### <a name="fixed-issues"></a>已修正的問題
- 版本 1.4.18.0 存在一個錯誤,其中 DSSO 的 PowerShell cmdlet 使用登錄視窗憑據,而不是運行 ps 時提供的管理員憑據。 因此,無法透過 AADConnect 使用者介面在多個林中啟用 DSSO。 
- 修復了透過 AADConnect 使用者介面在所有林中同時啟用 DSSO

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>發行狀態
2019/11/08:發佈供下載。 無法通過自動升級提供。

>[!IMPORTANT]
>由於此版本的 Azure AD Connect 中的內部架構更改,如果使用 MSOnline PowerShell 管理 AD FS 信任關係設定設定,則必須將 MSOnline PowerShell 模組更新為版本 1.1.183.57 或更高版本

### <a name="fixed-issues"></a>已修正的問題

此版本修復了現有混合 Azure AD 聯接設備的問題。 此版本包含一個新的設備同步規則,可解決此問題。
請注意,此規則更改可能會導致從 Azure AD 中刪除過時的設備。 這不引起關注,因為在條件訪問授權期間,Azure AD 不會使用這些設備物件。 對於某些客戶,將通過此規則更改刪除的設備數量可能超過刪除閾值。 如果看到 Azure AD 中的設備物件的刪除超過匯出刪除閾值,建議允許刪除。 [如何允許刪除在超過刪除閾值時流動](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>發行狀態
2019/9/28:發佈用於自動升級以選擇租戶。 無法下載。

此版本修復了一個錯誤,其中某些伺服器從以前的版本自動升級到 1.4.18.0,並且遇到自助服務密碼重置 (SSPR) 和密碼寫回問題。

### <a name="fixed-issues"></a>已修正的問題

在某些情況下,自動升級到版本 1.4.18.0 的伺服器在升級完成後未重新啟用自助服務密碼重置和密碼寫回。 此自動升級版本修復了該問題,並重新啟用自助服務密碼重置和密碼寫回。

我們修復了同步錯誤壓縮實用程式中未正確處理代理字元的 Bug。

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>我們正在調查一個事件,其中某些客戶在升級到此版本的 Azure AD Connect 後遇到現有混合 Azure AD 加入設備的問題。 我們建議已部署混合 Azure AD 聯接的客戶推遲升級到此版本,直到這些問題的根本原因得到充分理解和緩解。 我們將儘快提供更多的資訊。

>[!IMPORTANT]
>使用此版本的 Azure AD 連接,某些客戶可能會看到部分或全部 Windows 設備從 Azure AD 中消失。 這不引起關注,因為在條件訪問授權期間,Azure AD 不會使用這些設備標識。 有關詳細資訊[,請參閱瞭解 Azure AD 連接 1.4.xx.x 設備消失](reference-connect-device-disappearance.md)


### <a name="release-status"></a>發行狀態
2019/9/25:僅發佈自動升級。

### <a name="new-features-and-improvements"></a>新功能和改進
- 新的故障排除工具有助於排除"使用者未同步"、"組不同步"或"組成員不同步"方案。
- 在 AAD 連線故障排除文稿中新增對國家雲的支援 
- 應通知客戶,已刪除已棄用的 MIIS_Service WMI 終結點。 任何 WMI 操作現在都應通過 PS cmdlet 完成。
- 通過重置 AZUREADSSOACC 物件上受約束的委派來提高安全性
- 添加/編輯同步規則時,如果規則中使用的任何屬性位於連接器架構中,但未添加到連接器中,則這些屬性會自動添加到連接器中。 規則影響的物件類型也是如此。 如果向連接器添加了任何內容,則將在下一個同步週期上標記連接器以進行完全導入。
- 在新的 Azure AD 連接部署中不再支援使用企業或域管理員作為連接器帳戶。 當前 AAD 連接部署使用企業或域管理員作為連接器帳戶將不受此版本的影響。
- 在同步管理員中,在規則創建/編輯/刪除時運行完全同步。 任何規則更改都會出現一個彈出視窗,通知使用者是否將運行完全導入或完全同步。
- 添加了密碼錯誤的緩解步驟,以"連接>連接>屬性連接器"
- 在連接器屬性頁上為同步服務管理器添加了棄用警告。 此警告通知使用者應通過 Azure AD 連接精靈進行更改。
- 添加了有關使用者密碼策略問題的新錯誤。
- 防止按域和 OU 篩選器對組篩選進行錯誤配置。 當輸入組的域/OU 已篩選出來並阻止使用者前進直到問題得到解決時,組篩選將顯示錯誤。
- 使用者無法再在同步服務管理員 UI 中為活動目錄域服務或 Windows Azure 活動目錄創建連接器。
- 修復了同步服務管理器中自定義 UI 控制件的可存取性。
- 為 Azure AD Connect 中的所有登錄方法啟用了六個聯合管理任務。  (以前,只有"更新 AD FS TLS/SSL 證書"任務可用於所有登錄。
- 將登錄方法從聯合更改為小靈通或 PTA 時添加了警告,指出所有 Azure AD 域和使用者都將轉換為託管身份驗證。
- 從「重置 Azure AD 和 AD FS 信任」任務中刪除了權杖簽名證書,並添加了單獨的子任務以更新這些憑證。
- 添加了一個名為"管理證書"的新聯合管理任務,該任務具有更新 AD FS 伺服器場的 TLS 或令牌簽名證書的子任務。
- 添加了一個名為"指定主伺服器"的新聯合管理子任務,允許管理員為 AD FS 伺服器場指定新的主伺服器。
- 添加了名為"管理伺服器"的新聯合管理任務,該任務具有部署 AD FS 伺服器、部署 Web 應用程式代理伺服器和指定主伺服器的子任務。
- 添加了一個名為"查看聯合配置"的新聯合管理任務,該任務顯示當前 AD FS 設置。  (由於此添加,AD FS 設置已從"查看解決方案"頁面中刪除。

### <a name="fixed-issues"></a>已修正的問題
- 對於使用者物件接管其相應的聯繫人物件具有自引用(例如,使用者是自己的管理器)的方案,已解決同步錯誤問題。
- 幫助彈出視窗現在顯示在鍵盤焦點上。
- 對於自動升級,如果任何衝突的應用從 6 小時開始運行,請終止它並繼續升級。
- 在選擇目錄擴展時,將客戶可以選擇的屬性數限制為每個物件 100 個。 這將防止在匯出期間發生錯誤,因為 Azure 每個物件最多具有 100 個擴展屬性。
- 修復了 Bug 以使 AD 連接文稿更加健壯
- 修復了 Bug 以使使用現有命名管道 WCF 服務在電腦上安裝 AADConnect 更加可靠。
- 改進了有關組策略的診斷和故障排除,這些策略不允許 ADSync 服務在最初安裝時啟動。
- 修復了 Windows 計算機的顯示名稱寫不正確的錯誤。
- 修復了 Windows 計算機的作業系統類型寫不正確的錯誤。
- 修復了非 Windows 10 計算機意外同步的 Bug。 請注意,此更改的效果是,以前同步的非 Windows-10 電腦現在將被刪除。 這不會影響任何功能,因為 Windows 電腦的同步僅用於混合 Azure AD 域聯接,該聯接僅適用於 Windows-10 設備。
- 向 ADSync PowerShell 模組添加了幾個新的(內部)cmdlet。


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>將 Azure AD Connect 從早期版本升級到 1.3.21.0 存在已知問題,其中 O365 門戶不會反映更新的版本,即使 Azure AD Connect 已成功升級。
>
> 要解決此問題,您需要導入**AdSync**模組,然後在`Set-ADSyncDirSyncConfiguration`Azure AD 連接伺服器上運行 PowerShell cmdlet。  您可以使用以下步驟:
>
>1. 在管理模式下打開 PowerShell。
>2. 執行 `Import-Module "ADSync"`。
>3. 執行 `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`。
 
### <a name="release-status"></a>發行狀態 

05/14/2019: 發佈供下載

### <a name="fixed-issues"></a>已修正的問題 

- 修復了 Microsoft Azure 活動目錄連接生成 1.3.20.0 中存在的許可權漏洞的提升。  在某些情況下,此漏洞可能允許攻擊者在特權帳戶的上下文中執行兩個 PowerShell cmdlet,並執行特權操作。  此安全更新通過禁用這些 cmdlet 來解決此問題。 有關詳細資訊,請參閱[安全更新](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)。

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>發行狀態 

04/24/2019: 發佈供下載

### <a name="new-features-and-improvements"></a>新功能和改進 

- 新增對網域刷新的支援 
- 交換郵件公用資料夾功能轉到 GA 
- 改進服務故障的精靈錯誤處理 
- 在連接器屬性頁中添加了同步服務管理員 UI 上的警告連結。 
- 統一組回寫功能現在是 GA 
- 當 DC 缺少 LDAP 控制檔時,改進的 SSPR 錯誤訊息 
- 在安裝過程中新增 DCOM 註冊表錯誤的診斷程式  
- 改進 PHS RPC 錯誤的追蹤 
- 允許來自子域的 EA 信任 
- 允許在安裝期間輸入資料庫名稱(預設名稱 ADSync)
- 升級到 ADAL 3.19.8,以取得 Ping 的 WS 信任修補程式,並新增新 Azure 實體的支援 
- 變更群組同步規則以將 samAccountName、網域與網域FQDN串流到雲中 - 索賠所需的 
- 修改後的預設同步規則處理 –[在此處](how-to-connect-fix-default-rules.md)閱讀更多內容。
- 添加了作為窗口服務運行的新代理。 此代理名為"管理代理",支援 Azure AD Connect 伺服器的更深入遠端診斷,以説明 Microsoft 工程師在打開支援案例時進行故障排除。 默認情況下,未安裝並啟用此代理。  有關如何安裝和啟用代理的詳細資訊,請參閱什麼是 Azure [AD 連接管理代理?](whatis-aadc-admin-agent.md) 
- 更新了最終使用者授權協定 (EULA) 
- 為使用 AD FS 作為登錄類型的部署添加了自動升級支援。  這還消除了作為升級過程的一部分更新 AD FS Azure AD 依賴方信任的要求。 
- 添加了 Azure AD 信任管理任務,該任務提供了兩個選項:分析/更新信任和重置信任。 
- 更改 AD FS Azure AD 相依者信任行為,以便始終使用 -支援多域交換機(包括信任和 Azure AD 功能更新)。 
- 更改了安裝新的 AD FS 伺服器場行為,以便通過刪除使用預安裝證書的選項,需要 .pfx 證書。
- 更新了安裝新的 AD FS 伺服器場工作流,以便只允許部署 1 個 AD FS 和 1 個 WAP 伺服器。  所有其他伺服器將在初始安裝後完成。 

### <a name="fixed-issues"></a>已修正的問題 

- 修復 ADSync 服務的 SQL 重新連線邏輯 
- 修復以允許使用空 SQL AOA DB 進行乾淨安裝 
- 修復 PS 權限文稿以優化 GWB 權限 
- 使用本地資料庫修復 VSS 錯誤  
- 當物件型態不在作用網域中時修復誤導性錯誤訊息 
- 更正了在伺服器上安裝 Azure AD PowerShell 可能會導致與 Azure AD 連接的程式集衝突的問題。 
- 在同步服務管理員 UI 中更新連接器認證時,修復暫存伺服器上的 PHS 錯誤。 
- 修復了一些記憶體洩漏 
- 其他自動升級修補程式 
- 匯出與未確認匯入處理的雜項修復 
- 修復了在域和 OU 篩選中處理反斜杠的錯誤 
- 修復了 ADSync 服務需要 2 分鐘以上才能停止並在升級時出現問題的問題。 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>發行狀態

2018/12/18:發佈供下載

### <a name="fixed-issues"></a>已修正的問題

此組建更新隨附於 Azure AD Connect 的非標準連接器 (例如 Generic LDAP Connector 和 Generic SQL Connector)。 如需適用之連接器的詳細資訊，請參閱[連接器版本發行歷程記錄](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history)中的 1.1.911.0 版。


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>發行狀態
2018/12/11:發佈供下載

### <a name="fixed-issues"></a>已修正的問題
此 Hotfix 組建讓使用者在啟用裝置回寫時，能為 RegisteredDevices 容器選取目標網域 (在指定的樹系內)。  在包含新的 [裝置選項] 功能的先前版本中 (1.1.819.0 – 1.2.68.0)，RegisteredDevices 容器被位置限制在樹系根，而不允許子網域。  此限制只在新部署中出現 (就地升級不受影響)。  

如果將包含更新的 [裝置選項] 功能的組建部署到新伺服器，並且啟用裝置回寫，如果您不想要容器是在樹系根中，就必須手動指定其位置。  若要這樣做，您需要停用裝置回寫並重新啟用，這可讓您在 [回寫樹系] 頁面上指定容器位置。



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>發行狀態 

2018/11/30:發佈供下載

### <a name="fixed-issues"></a>已修正的問題

此 Hotfix 修正一個衝突，當同步處理伺服器上的 MSOnline PowerShell 資源庫模組獨立存在時，可能會造成驗證錯誤。



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>發行狀態 

2018/11/19：已發行，可供下載

### <a name="fixed-issues"></a>已修正的問題

此 Hotfix 組件修正前一個組件中的迴歸，當在 Windows Server 2008/R2 上使用「ADDS 網域控制站」時，「密碼回寫」會失敗。

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>發行狀態 

2018/10/25：已發行，可供下載

 
### <a name="new-features-and-improvements"></a>新功能和改進 


- 變更屬性回寫的功能，確保裝載的語音信箱如預期般運作。  在某些情況下，Azure AD 會在回寫期間以 Null 值覆寫 msExchUcVoicemailSettings 屬性。  如果未設定雲端值，Azure AD 現在不會再清除此屬性的內部部署值。
- 在 Azure AD Connect 精靈中新增診斷功能，可調查和識別 Azure AD 的連線能力問題。 這些相同的診斷也可以直接透過 PowerShell 使用測試 AdSyncAzure 服務連接 Cmdlet 運行。 
- 在 Azure AD Connect 精靈中新增診斷功能，可調查和識別 AD 的連線能力問題。 這些相同的診斷也可以直接透過 PowerShell 使用 ADConnectivityTools PowerShell 模組中的啟動連接驗證功能運行。  如需詳細資訊，請參閱[什麼是 ADConnectivityTool PowerShell 模組？](how-to-connect-adconnectivitytools.md)
- 針對混合式 Azure Active Directory Join 和裝置回寫，新增 AD 結構描述版本前置檢查 
- 目錄擴充頁面屬性搜尋已變更為不區分大小寫。
-   新增對 TLS 1.2 的完整支援。 這個版本支援在安裝 Azure AD Connect 的電腦上，停用所有其他通訊協定，而僅啟用 TLS 1.2。  如需詳細資訊，請參閱[針對 Azure AD Connect 強制執行 TLS 1.2](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>已修正的問題   

- 已修正如果使用 SQL Always On，Azure AD Connect 升級就會失敗的錯誤 (bug)。 
- 已修正錯誤 (bug)，可正確地剖析含有正斜線的 OU 名稱。 
- 已修正在預備模式進行全新安裝時，就會停用傳遞驗證的問題。 
- 已修正在執行疑難排解工具時會無法載入 PowerShell 模組的錯誤 (bug) 
- 已修正會妨礙客戶在主機名稱的第一個字元使用數值的錯誤 (bug)。 
- 已修正 Azure AD Connect 允許無效資料分割和容器選取項目的錯誤 (bug) 
- 已修正在啟用傳統型 SSO 時的「密碼無效」錯誤訊息。 
- AD FS 信任管理的各種錯誤 (bug)修正  
- 在設定裝置回寫時的結構描述檢查，已修正為尋找 msDs-DeviceContainer 物件類別 (在 WS2012 R2 引進)

 
## <a name="118820"></a>1.1.882.0  

9/7/2018：已發行可供下載，不會發行供自動升級 

### <a name="fixed-issues"></a>已修正的問題  

如果已為 ADSync DB 設定 SQL Always On 可用性，則 Azure AD Connect 升級會失敗。 此 Hotfix 可解決此問題，並使升級成功。 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>發行狀態

8/21/2018：已發行，可供下載和自動升級。 

### <a name="new-features-and-improvements"></a>新功能和改進

- Azure AD Connect 中的 Ping Federate 整合現已正式運作。 [深入了解如何將 Azure AD 與 Ping Federate 同盟](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- 現在，每當進行更新時，Azure AD Connect 都會在 AD FS 中建立 Azure AD 信任的備份，並將其儲存在個別的檔案中，以備必要時可輕易還原。 [在 Azure AD 連線 中瞭解有關新功能和 Azure AD 信任管理的更多詳細資訊](https://aka.ms/fedtrustinaadconnect)。
- 新的疑難排解工具可協助您進行變更主要電子郵件地址和在全域通訊清單中隱藏帳戶方面的疑難排解
- Azure AD Connect 已更新，而包含最新的 SQL Server 2012 Native Client
- 當您在「變更使用者登入」工作中將使用者登入切換為 [密碼雜湊同步處理] 或 [傳遞驗證] 時，依預設會啟用 [無縫單一登入] 核取方塊。
- 已新增 Windows Server Essentials 2019 的支援
- Azure AD Connect Health 代理程式已更新為最新版本 3.1.7.0
- 在升級期間，如果安裝程式偵測到預設同步規則的變更，將會先對管理員顯示警告，才覆寫修改過的規則。 這可讓使用者採取更正動作再繼續作業。 舊的行為：如果有任何修改過的預設規則，則會直接以手動升級覆寫這些規則，而不會對使用者顯示任何警告，並直接停用同步排程器而不通知使用者。 新的行為：使用者會在覆寫修改過的預設同步規則之前看到提示的警告。 使用者將可選擇停止升級程序，並在採取更正動作後再繼續作業。
- 提供更完善的 FIPS 合規性問題處理、針對 FIPS 相容環境中產生的 MD5 雜湊提供錯誤訊息，並以文件的連結提供此問題的因應措施。
- UI 更新可改善精靈中的同盟工作，這些工作現已可在個別的子群組中進行同盟。 
- 所有其他的同盟工作現在都分組在單一子功能表下，以方便使用。
- 全新設計的 ADSyncConfig Posh Module (AdSyncConfig.psm1) 具有來自舊版 ADSyncPrep.psm1 (可能於近期淘汰) 的新式 AD 權限功能

### <a name="fixed-issues"></a>已修正的問題 

- 修復 AAD Connect 伺服器在升級到 .NET 4.7.2 後 CPU 使用率高的 Bug
- 已修正會間歇性對自動解決的 SQL 死結問題產生錯誤訊息的 Bug
- 已修正 Sync Rules Editor 和 Sync Service Manager 的數個協助工具問題  
- 已修正 Azure AD Connect 無法取得登錄設定資訊的 Bug
- 已修正在使用者繼續使用/返回精靈時會產生問題的 Bug
- 修復了 Bug,以防止由於精靈中的多線程處理不正確而發生錯誤
- 現在，[群組同步篩選] 頁面若在解析安全性群組時發生 LDAP 錯誤，Azure AD Connect 已會傳回完整的例外狀況。  轉介例外狀況的根本原因仍未知，且將視同個別的 Bug 來解決。
-  已修正 SDK 和 NGC 金鑰 (WHfB 使用者/裝置物件的 ms-DS-KeyCredentialLink 屬性) 的權限未正確設定的 Bug。     
- 已修正 'Set-ADSyncRestrictedPermissions’ 未正確呼叫的 Bug
-  新增對 AADConnect 安裝精靈中的群組回寫授與權限的支援
- 當登入方法從「密碼雜湊同步」變更為 AD FS 時，「密碼雜湊同步」不會停用。
- 已新增 AD FS 組態中的 IPv6 位址驗證
- 已更新通知訊息，以通知有現有的組態存在。
- 裝置回寫無法偵測不受信任樹系中的容器。 此行為已更新，而會提供更明確的錯誤訊息，以及適當文件的連結
- 取消選取 OU 後，對應於該 OU 的同步處理/回寫會產生一般同步錯誤。 此行為已變更，而會產生更容易了解的錯誤訊息。

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>發行狀態

5/14/2018：已發行，可供自動升級和下載。

### <a name="new-features-and-improvements"></a>新功能和改進

新功能和改進

- 此版本包含 Azure AD Connect 中 PingFederate 整合的公開預覽。 客戶可以使用此版本，輕易且可靠地設定其 Azure Active Directory 環境，以將 PingFederate 當作同盟提供者。 若要深入了解如何使用這項新功能，請造訪我們的[線上文件](plan-connect-user-signin.md#federation-with-pingfederate)。 
- 已更新 Azure AD Connect 精靈疑難排解公用程式，現在可在其中分析更多錯誤案例，例如，連結的信箱和 AD 動態群組。 [在此](tshoot-connect-objectsync.md)深入了解如何針對公用程式進行疑難排解。
- 現在只會在 Azure AD Connect 精靈中管理裝置回寫組態。
- 已新增稱為 ADSyncTools.psm1 的新 PowerShell 模組，其可針對 SQL 連線問題和各種其他疑難排解公用程式進行疑難排解。 [在此](tshoot-connect-tshoot-sql-connectivity.md)深入了解 ADSyncTools 模組。 
- 已新增其他新工作「設定裝置選項」。 您可以使用此工作來設定下列兩項作業： 
  - **加入混合式 Azure AD**：如果您的環境具有內部部署 AD 使用量，而且您也想要從 Azure Active Directory 所提供的功能受益，您可以實作已加入混合式 Azure AD 的裝置。 這些裝置既可以加入您的內部部署 Active Directory，也可以加入 Azure Active Directory。
  - **裝置回寫**:裝置回寫用於以裝置對 AD FS(2012 R2 或更高版本)保護裝置啟用條件存取

    >[!NOTE] 
    > - 從自訂同步處理選項啟用裝置回寫的選項會呈現灰色。 
    > -  在此版本中，適用於 ADPrep 的 PowerShell 模組已被取代。



### <a name="fixed-issues"></a>已修正的問題 

- 這個版本會將 SQL Server Express 安裝更新為 SQL Server 2012 SP4，這個版本相較於其他版本，會提供數個安全性弱點的修正。  如需 SQL Server 2012 SP4 的詳細資訊，請參閱[這裡](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information)。
- 同步規則處理：如果父系同步規則不再適用，就應取消套用沒有聯結條件的輸出聯結同步規則
- 數個協助工具修正已套用至 Synchronization Service Manager UI 和 Sync Rules Editor
- Azure AD Connect 精靈：如果 Azure AD Connect 位於工作群組中，則建立 AD 連接器帳戶時會發生錯誤
- Azure AD Connect 精靈：每當 AD 網域與 Azure AD 已驗證網域有任何不符情形，就會在 Azure AD 登入頁面上顯示驗證核取方塊
- 自動升級 PowerShell 修正程式，以在嘗試自動升級之後，在某些情況下正確設定自動升級狀態。
- Azure AD Connect 精靈：已將遙測更新為擷取先前遺漏的資訊
- Azure AD Connect 精靈：當您使用**變更使用者登入**工作以從 AD FS 切換至傳遞驗證時，已進行下列變更：
    - 在我們將網域從同盟轉換為受控之前，傳遞驗證代理程式是安裝在 Azure AD Connect 伺服器上，且已啟用傳遞驗證功能。
    - 使用者無法再從同盟轉換為受控。 只會轉換網域。
- Azure AD Connect 精靈：當使用者 UPN 有 ' 特殊字元 Regex 更新可支援特殊字元時，AD FS 多網域 Regex 不正確
- Azure AD Connect 精靈：若沒有變更，則會移除偽造的「設定來源錨點屬性」訊息 
- Azure AD Connect 精靈：AD FS 支援雙重同盟案例
- Azure AD Connect 精靈：將受控網域轉換為同盟網域時，不會針對新增的網域更新 AD FS 宣告
- Azure AD Connect 精靈：在偵測已安裝的套件期間，我們找到過時的 Dirsync/Azure AD Sync/Azure AD Connect 相關產品。 我們現在將嘗試解除安裝過時的產品。
- Azure AD Connect 精靈：當傳遞驗證代理程式安裝失敗，更正錯誤訊息對應
- Azure AD Connect 精靈：已從 [網域 OU 篩選] 頁面中移除「組態」容器
- 同步處理引擎安裝：從同步處理引擎安裝 msi 移除偶爾會失敗的不必要舊版邏輯
- Azure AD Connect 精靈：修正密碼雜湊同步處理的 [選擇性功能] 頁面上的快顯說明文字
- 同步處理引擎執行階段：修正 CS 物件具有已匯入刪除和同步處理規則嘗試的案例，以重新佈建此物件。
- 同步處理引擎執行階段：將線上連線疑難排解指南的說明連結，新增至匯入錯誤的事件記錄
- 同步處理引擎執行階段：已減少同步處理排程器在列舉連接器時的記憶體使用量
- Azure AD Connect 精靈：修正問題，以解決沒有 AD 讀取權限的自訂同步處理服務帳戶
- Azure AD Connect 精靈：已改善網域和 OU 篩選選取項目的記錄功能
- Azure AD Connect 精靈：AD FS 會將預設宣告新增至為 MFA 案例建立的同盟信任
- Azure AD Connect 精靈：AD FS 部署 WAP：新增伺服器無法使用新的憑證
- Azure AD Connect 精靈：未針對網域初始化 onPremCredentials 時的 DSSO 例外狀況 
- 優先從作用中使用者物件傳送 AD distinguishedName 屬性。
- 已修正將第一個 OOB 同步規則的優先順序設定為 99 (而非 100) 時的外觀錯誤



## <a name="117510"></a>1.1.751.0
4/12/2018 狀態：已發行，僅可供下載

>[!NOTE]
>這個版本是適用於 Azure AD Connect 的 Hotfix

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 同步處理
#### <a name="fixed-issues"></a>已修正的問題
已更正中國租用戶的自動 Azure 執行個體探索偶爾會失敗的問題。  

### <a name="ad-fs-management"></a>AD FS 管理
#### <a name="fixed-issues"></a>已修正的問題

組態重試邏輯有問題，該問題會造成表示「已新增具有相同索引鍵的項目」的 ArgumentException。  這會導致所有重試作業失敗。

## <a name="117500"></a>1.1.750.0
2018/3/22 狀態：已發行，可供自動升級和下載。
>[!NOTE]
>當這個新的版本的升級完成時，會自動觸發 Azure AD 連接器的完整同步處理和完整匯入，以及 AD 連接器的完整同步處理。 由於這可能需要一些時間，視您的 Azure AD Connect 環境大小而定，所以請確定您已採取支援這項作業的必要步驟，否則請暫緩升級直到您找到方便執行這項作業的時間。

>[!NOTE]
>有部分部署晚於 1.1.524.0 組建的租用戶，其自動升級功能遭錯誤停用。 若要確定 Azure AD Connect 執行個體是否仍可自動升級，請執行下列 PowerShell Cmdlet：“Set-ADSyncAutoUpgrade -AutoupGradeState Enabled”


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>已修正的問題

* 先前，自動升級狀態設為 [暫止]，則 Set-ADSyncAutoUpgrade Cmdlet 會封鎖自動升級。 此功能現在已變更，因此不會封鎖未來組建的自動升級。
* 已將**使用者登入**頁面選項「密碼同步處理 」變更為「密碼雜湊同步處理 」。  Azure AD Connect 同步處理密碼雜湊，而不是密碼，以便與實際發生事件對應。  有關詳細資訊,請參閱[實現與 Azure AD 連接同步的密碼哈希同步](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
狀態：已發行給選定的客戶

>[!NOTE]
>當這個新的版本的升級完成時，會自動觸發 Azure AD 連接器的完整同步處理和完整匯入，以及 AD 連接器的完整同步處理。 由於這可能需要一些時間，視您的 Azure AD Connect 環境大小而定，所以請確定您已採取支援這項作業的必要步驟，否則請暫緩升級直到您找到方便執行這項作業的時間。

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>已修正的問題
* 修正計時視窗切換至下一個頁面時，在 [資料分割篩選] 頁面背景工作的問題。

* 修正在 ConfigDB 自訂動作期間造成存取違規的錯誤 (bug)。

* 修正從 SQL 復原連線逾時的錯誤 (bug)。

* 修正含 SAN 萬用字元的憑證在必要條件檢查中失敗的錯誤 (bug)。

* 修正在 Azure AD 連接器匯出期間造成 miiserver.exe 當機的錯誤 (bug)。

* 修正當執行 Azure AD Connect 精靈變更設定時，錯誤密碼嘗試登入 DC 的錯誤 (bug)。


#### <a name="new-features-and-improvements"></a>新功能和改進

* 新增「一般資料保護規定」(GDPR) 的「隱私權設定」。  如需詳細資訊，請參閱[這裡](reference-connect-user-privacy.md)的文章。

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* 應用程式遙測 - 系統管理員可以任意切換此資料類別的開啟/關閉

* Azure AD Health 資料 - 系統管理員必須瀏覽健康情況入口網站來控制其健康情況設定。
   一旦變更服務原則，代理程式會讀取並強制執行它。

* 加入裝置回寫式設定動作和頁面初始化的進度列。

* 改善 ZIP 文字 / HTML 報表中 HTML 報表與完整資料集合的一般診斷。

* 改善自動升級的可靠性，並加入其他遙測，以確保您可以判斷伺服器的健康情況。

* 限制 AD 連接器帳戶的特殊權限帳戶的可用權限。

  * 在新的安裝中，精靈會限制在建立 MSOL 帳戶後，特殊權限帳戶對 MSOL 帳戶的權限。

這些變更涵蓋：
1. 快速安裝
2. 含自動建立帳戶的自訂安裝
3. 修改了安裝程式，使其在全新安裝 Azure AD Connect 時不需要 SA 權限。

* 加入新的公用程式，可針對特定物件的同步處理問題進行疑難排解。 它位於 Azure AD Connect 精靈疑難排解其他工作的「針對物件同步處理進行疑難排解」選項底下。 目前，此公用程式會檢查下列各項：

  * 同步處理的使用者物件與 Azure AD 租用戶中使用者帳戶的 UserPrincipalName 不符。
  * 是否因為網域篩選而從同步處理篩選物件
  * 是否因為組織單位 (OU) 篩選而從同步處理篩選物件

* 加入新的公用程式來同步處理目前儲存在內部部署 Active Directory 中的特定使用者帳戶的密碼雜湊。

此公用程式不需要變更密碼。 它位於 Azure AD Connect 精靈疑難排解其他工作的「針對密碼雜湊同步處理進行疑難排解」選項底下。






## <a name="116540"></a>1.1.654.0
狀態：2017 年 12 月 12 日

>[!NOTE]
>這個版本是適用於 Azure AD Connect 的安全性相關 Hotfix

### <a name="azure-ad-connect"></a>Azure AD Connect
Azure AD Connect 1.1.654.0 版 (和更新版本) 已新增改進，以確保 Azure AD Connect 在建立 AD DS 帳戶時，會自動套用[鎖定 AD DS 帳戶的存取權](#lock)章節中所述的建議權限變更。 

- 設定 Azure AD Connect 時，安裝系統管理員可以提供現有的 AD DS 帳戶，或讓 Azure AD Connect 自動建立帳戶。 權限變更會自動套用到 Azure AD Connect 在安裝期間所建立的 AD DS 帳戶。 但權限變更並不會套用到安裝系統管理員提供的現有 AD DS 帳戶。
- 對於已從舊版 Azure AD Connect 升級至 1.1.654.0 版 (或更新版本) 的客戶，權限變更不會回溯套用至升級之前建立的現有 AD DS 帳戶。 它們只會套用到升級之後所建立的新 AD DS 帳戶。 僅當您將要進行同步的新 AD 樹系新增至 Azure AD 時，才會發生這種情況。

>[!NOTE]
>此版本所能移除的弱點，僅限於安裝流程在當中建立服務帳戶的 Azure AD Connect 新安裝中的弱點。 您應該確定現有的安裝或是您自行提供帳戶時，不會出現這項弱點。

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> 鎖定 AD DS 帳戶的存取權
透過在內部部署 AD 中實作下列權限變更，來鎖定 AD DS 帳戶的存取權：  

*   停用指定物件上的繼承
*   將特定物件上所有的 ACE 移除，除了 SELF 特有的 ACE 之外。 關於 SELF，我們需要將預設權限維持不變。
*   指派這些特定權限：

類型     | 名稱                          | 存取               | 套用至
---------|-------------------------------|----------------------|--------------|
Allow    | 系統                        | 完全控制         | 此物件  |
Allow    | 企業系統管理員             | 完全控制         | 此物件  |
Allow    | 網域管理員                 | 完全控制         | 此物件  |
Allow    | 系統管理員                | 完全控制         | 此物件  |
Allow    | 企業網域控制站 | 清單內容        | 此物件  |
Allow    | 企業網域控制站 | 讀取所有屬性  | 此物件  |
Allow    | 企業網域控制站 | 讀取權限     | 此物件  |
Allow    | 驗證的使用者           | 清單內容        | 此物件  |
Allow    | 驗證的使用者           | 讀取所有屬性  | 此物件  |
Allow    | 驗證的使用者           | 讀取權限     | 此物件  |

若要加強 AD DS 帳戶的設定，您可以執行[這個 PowerShell 指令碼](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978)。 PowerShell 指令碼會將上述權限指派給 AD DS 帳戶。

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>可加強預先存在之服務帳戶的 PowerShell 指令碼

若要使用 PowerShell 指令碼將這些設定套用到預先存在的 AD DS 帳戶 (由貴組織提供的、或由先前安裝之 Azure AD Connect 建立的)，請從上方提供的連結下載指令碼。

##### <a name="usage"></a>Usage :

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Where 

**$ObjectDN** = 必須加強權限的 Active Directory 帳戶。

**$Credential** = 具備限制 $ObjectDN 帳戶權限之必要權限的系統管理員認證。 這些權限通常會由企業或網域系統管理員所持有。 使用系統管理員帳戶的完整網域名稱以避免帳戶對應失敗。 範例：contoso.com\admin。

>[!NOTE] 
>$credential.UserName 應該為 FQDN\username 格式。 範例：contoso.com\admin 

##### <a name="example"></a>範例：

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>系統是否使用這項弱點來取得未經授權的存取？

若要查看系統是否使用這項弱點來入侵您的 Azure AD Connect 設定，您應該確認服務帳戶最後重設密碼的日期。  如果是非預期的時間戳記，就應該透過事件記錄，對該密碼重設事件採取進一步調查。

如需詳細資訊，請參閱 [Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
狀態：2017 年 10 月 27 日

>[!NOTE]
>此組建無法透過 Azure AD Connect 自動升級功能提供客戶使用。

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>已修正的問題
* 已修正 Azure AD Connect 與 Azure AD Connect Health Agent (適用於同步) 之間的版本相容性問題。 此問題會影響要執行 Azure AD Connect 就地升級到 1.1.647.0 版，但目前擁有的是健康情況代理程式 3.0.127.0 版的客戶。 升級之後，健康情況代理程式就不會再將有關 Azure AD Connect 同步處理服務的健康情況資料傳送至 Azure AD 健康情況服務。 透過此修正，就會在 Azure AD Connect 就地升級期間安裝健康情況代理程式 3.0.129.0 版。 健康情況代理程式 3.0.129.0 版與 Azure AD Connect 1.1.649.0 版 沒有相容性問題。


## <a name="116470"></a>1.1.647.0
狀態：2017 年 10 月 19 日

> [!IMPORTANT]
> Azure AD Connect 1.1.647.0 版與 Azure AD Connect Health Agent (for sync) 3.0.127.0 版之間有一個已知的相容性問題。 此問題會阻止 Health Agent 將有關 Azure AD Connect Synchronization Service 的健康情況資料 (包括物件同步處理錯誤和執行歷程記錄) 傳送至 Azure AD Health Service。 在手動將 Azure AD Connect 部署升級至 1.1.647.0 版之前，請驗證目前安裝在 Azure AD Connect 伺服器上的 Azure AD Connect Health Agent 版本。 您可以透過存*取控制面板( 新增移除程式)*, 並尋找應用程式*Microsoft Azure AD 連線執行狀況代理進行同步*。如果版本為 3.0.127.0,建議您在升級之前等待下一個 Azure AD Connect 版本可用。 如果 Health Agent 版本不是 3.0.127.0，則可繼續進行手動、就地升級。 請注意，此問題並不會影響轉移升級或全新安裝 Azure AD Connect 的客戶。
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>已修正的問題
* 已修正 Azure AD Connect 精靈中「變更使用者登入」** 工作發生的問題：

  * 當現有的 Azure AD Connect 部署**已啟用**密碼同步化，而且您正要嘗試將使用者登入方法設定為「傳遞驗證」** 時，就會發生此問題。 在套用變更之前，精靈不正確地顯示「停用密碼同步化」** 提示。 不過，在套用變更之後，密碼同步化仍維持啟用狀態。 透過此修正，精靈不再顯示此提示。

  * 根據設計，當您使用「變更使用者登入」** 工作，更新使用者登入方法時，精靈不會停用密碼同步化。 這是為了避免中斷想要保留密碼同步化的客戶，因為他們即使將啟用傳遞驗證或同盟作為其主要使用者登入方法，也想要保留密碼同步化。
  
  * 如果想要在更新使用者登入方法之後停用密碼同步化，您必須在精靈中執行「自訂同步處理設定」** 工作。 當瀏覽至 [選用功能]** 頁面時，請取消核取 [密碼同步化]** 選項。
  
  * 請注意，如果您嘗試啟用/停用無縫單一登入，也會發生相同的問題。 尤其，現有的 Azure AD Connect 部署已啟用密碼同步化，而且已將使用者登入方法設定為「傳遞驗證」**。 當使用者登入方法仍設定為 [傳遞驗證]** 時，您嘗試使用「變更使用者登入」** 工作，核取/取消核取 [啟用無縫單一登入] 選項。 在套用變更之前，精靈不正確地顯示「停用密碼同步化」** 提示。 不過，在套用變更之後，密碼同步化仍維持啟用狀態。 透過此修正，精靈不再顯示此提示。

* 已修正 Azure AD Connect 精靈中「變更使用者登入」** 工作發生的問題：

  * 當現有的 Azure AD Connect 部署**已停用**密碼同步化，而且您正要嘗試將使用者登入方法設定為「傳遞驗證」** 時，就會發生此問題。 套用變更時，精靈會同時啟用傳遞驗證和密碼同步化。 透過此修正，精靈不再啟用密碼同步化。

  * 以前，密碼同步化是啟用傳遞驗證的必要條件。 當您將使用者登入方法設定為「傳遞驗證」** 時，精靈會同時啟用傳遞驗證和密碼同步化。 最近，已移除密碼同步化作為必要條件。 在 Azure AD Connect 1.1.557.0 版中，已變更 Azure AD Connect ，以便在您將使用者登入方法設定為「傳遞驗證」** 時不啟用密碼同步化。 不過，此變更僅適用於 Azure AD Connect 安裝。 透過此修正，相同的變更也適用於「變更使用者登入」** 工作。
  
  * 請注意，如果您嘗試啟用/停用無縫單一登入，也會發生相同的問題。 尤其，現有的 Azure AD Connect 部署已停用密碼同步化，而且已將使用者登入方法設定為「傳遞驗證」**。 當使用者登入方法仍設定為 [傳遞驗證]** 時，您嘗試使用「變更使用者登入」** 工作，核取/取消核取 [啟用無縫單一登入] 選項。 套用變更時，精靈會啟用密碼同步化。 透過此修正，精靈不再啟用密碼同步化。 

* 已修正導致 Azure AD Connect 升級失敗的問題，錯誤為「無法升級同步處理服務」**。 此外，啟動同步處理服務時，不再出現事件錯誤「服務無法啟動，因為資料庫的版本比已安裝的二進位檔版本還新」**。 當執行升級的系統管理員對 Azure AD Connect 正在使用的 SQL Server 沒有 sysadmin 權限，就會發生此問題。 透過此修正，Azure AD Connect 只需要系統管理員在升級期間對 ADSync 資料庫具有 db_owner 權限。

* 已修正 Azure AD Connect 升級問題，此問題影響了已啟用[無縫單一登入](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)的客戶。 在 Azure AD Connect 升級之後，無縫單一登入在 Azure AD Connect 精靈中不正確地顯示為已停用，即使功能仍保持啟用狀態且完全正常運作。 透過此修正，現在功能在精靈中正確地顯示為已啟用。

* 已修正下列問題：導致 Azure AD Connect 精靈在 [準備好設定]** 頁面上永遠顯示「設定來源錨點」** 提示，即使未進行任何與來源錨點相關的變更。

* 在執行 Azure AD connect 的手動就地升級時，需要客戶提供對應 Azure AD 租用戶的全域管理員認證。 以前，即使全域管理員的認證屬於不同的 Azure AD 租用戶，升級仍能繼續進行。 雖然升級似乎完全成功，但是某些設定由於升級而未正確地保存。 透過這項變更，如果提供的認證與 Azure AD 租用戶不符，精靈就會阻止升級繼續進行。

* 已移除在開始手動升級時不必要地重新啟動 Azure AD Connect Health 服務的備援邏輯。


#### <a name="new-features-and-improvements"></a>新功能和改進
* 已新增邏輯，以簡化使用 Microsoft Germany Cloud 設定 Azure AD Connect 所需的步驟。 以前，您需要更新 Azure AD Connect 伺服器上的特定登錄機碼，才能正確地使用 Microsoft Germany Cloud，如本文所述。 現在，Azure AD Connect 可以根據設定期間提供的全域管理員認證，自動偵測您的租用戶是否位於 Microsoft Germany Cloud。

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 同步處理
> [!NOTE]
> 注意：同步處理服務具有 WMI 介面，可讓您開發專屬的自訂排程器。 此介面現在已被取代，而且將從 2018 年 6 月 30 日之後提供的 Azure AD Connect 版本中移除。 想要自訂同步計畫的客戶應使用[內建計畫程式](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler)。

#### <a name="fixed-issues"></a>已修正的問題
* 當 Azure AD Connect 精靈建立從內部部署 Active Directory 同步處理變更所需的 AD 連接器帳戶時，它不會正確地將讀取 PublicFolder 物件所需的權限指派給帳戶。 此問題會同時影響快速安裝和自訂安裝。 這項變更會修正此問題。

* 已修正下列問題：導致未對從 Windows Server 2016 執行的系統管理員 正確地呈現 Azure AD Connect 精靈疑難排解頁面。

#### <a name="new-features-and-improvements"></a>新功能和改進
* 使用 Azure AD Connect 精靈疑難排解頁面，針對密碼同步化進行疑難排解時，疑難排解頁面現在會傳回網域特定狀態。

* 以前，如果您嘗試啟用密碼雜湊同步處理，Azure AD Connect 並不會驗證 AD 連接器帳戶是否具有必要的權限，可同步處理來自內部部署 AD 的密碼雜湊。 現在，如果 AD 連接器帳戶沒有足夠的權限，Azure AD Connect 精靈將確認並警告您。

### <a name="ad-fs-management"></a>AD FS 管理
#### <a name="fixed-issue"></a>已修正的問題
* 已修正與使用 [ms-DS-ConsistencyGuid 作為來源錨點](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能相關的問題。 此問題會影響已將「與 AD FS 同盟」** 設定為使用者登入方法的客戶。 當您在精靈中執行「設定來源錨點」** 工作時，Azure AD Connect 會切換成使用 * ms-DS-ConsistencyGuid 作為 immutableId 的來源屬性。 進行這項變更時，Azure AD Connect 會嘗試更新 AD FS 中 ImmutableId 的宣告規則。 不過，這個步驟失敗，因為 Azure AD Connect 沒有設定 AD FS 所需的系統管理員認證。 透過此修正，現在當您執行「設定來源錨點」** 工作時，Azure AD Connect 會提示您輸入 AD FS 的系統管理員認證。



## <a name="116140"></a>1.1.614.0
狀態：2017 年 9 月 5 日

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>已知問題
* 有一個已知題，導致 Azure AD Connect 升級失敗，錯誤為「無法升級同步處理服務」**。 此外，啟動同步處理服務時，不再出現事件錯誤「服務無法啟動，因為資料庫的版本比已安裝的二進位檔版本還新」**。 當執行升級的系統管理員對 Azure AD Connect 正在使用的 SQL Server 沒有 sysadmin 權限，就會發生此問題。 Dbo 權限還不夠。

* Azure AD Connect 升級有個已知問題，會影響已啟用[無縫單一登入](how-to-connect-sso.md)的客戶。 Azure AD Connect 升級之後，即使該功能維持啟用，在精靈中也會呈現為停用。 未來的版本將提供此問題的修正。 若客戶擔憂該顯示問題，可在精靈中啟用無縫單一登入，手動修正問題。

#### <a name="fixed-issues"></a>已修正的問題
* 已修正在內部部署 AD FS 中啟用[將 ms-DS-ConsistencyGuid 當作來源錨點](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能時，導致 Azure AD Connect 無法更新宣告規則的問題。 如果您嘗試針對已將 AD FS 設為登入方法的現有 Azure AD Connect 部署啟用此功能，就會發生此問題。 問題發生的原因在於，精靈不會在嘗試更新 AD FS 中的宣告規則之前提示您輸入 AD FS 認證。
* 已修正在內部部署 AD 樹系停用 NTLM 的情況下，導致 Azure AD Connect 無法安裝的問題。 該問題是由於 Azure AD Connect 精靈在建立 Kerberos 驗證所需的資訊安全內容時，未提供完整認證所致。 這會導致 Kerberos 驗證失敗，且 Azure AD Connect 精靈會切換回 NTLM。

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 同步處理
#### <a name="fixed-issues"></a>已修正的問題
* 已修正如果未填入標記屬性，將無法建立新同步處理規則的問題。
* 已修正即使 Kerberos 可使用，Azure AD Connect 仍會連線到內部部署 AD 使用 NTLM 處理密碼同步化的問題。 如果內部部署 AD 拓撲具有從備份還原的一或多個網域控制站，便會發生此問題。
* 已修正升級後會導致出現不必要的完整同步處理步驟的問題。 一般而言，如果現成的同步處理規則有所變更，才需要在升級後執行完整的同步處理步驟。 問題原因在於變更偵測邏輯發生錯誤，當碰到同步處理規則運算式有新行字元時，會誤偵測到變更。 新行字元會插入到同步處理規則運算式，以提高可讀性。
* 已修正會導致 Azure AD Connect 伺服器在自動升級後無法正常運作的問題。 此問題會影響 Azure AD Connect 伺服器 1.1.443.0 版 (或更早的版本)。 如需此問題的詳細資訊，請參閱文章 [Azure AD Connect 無法在自動升級後正確運作](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade)。
* 已修正會導致遇到錯誤時每隔 5 分鐘重試自動升級的問題。 藉由修正，自動升級會在遇到錯誤時使用指數退避法重試。
* 已修正 Windows 應用程式事件記錄將密碼同步化事件 611 錯誤顯示為**資訊**而非**錯誤**的問題。 只要密碼同步化發生問題，都會產生事件 611。 
* 已修正未選取群組回寫所需的 OU 即允許啟用群組回寫功能的 Azure AD Connect 精靈問題。

#### <a name="new-features-and-improvements"></a>新功能和改進
* 已將疑難排解工作新增到 Azure AD Connect 精靈的 [其他工作] 之下。 客戶可以利用這項工作，對於密碼同步化的相關問題進行疑難排解，並收集一般診斷資訊。 未來，疑難排解工作將會擴大納入其他目錄同步作業的相關問題。
* Azure AD Connect 現在支援新的安裝模式，稱為**使用現有的資料庫**。 此安裝模式可讓客戶安裝指定現有 ADSync 資料庫的 Azure AD Connect。 如需這項功能的詳細資訊，請參閱文章[使用現有的資料庫](how-to-connect-install-existing-database.md)。
* 為了提升安全性，Azure AD Connect 現在預設使用 TLS1.2 連線到 Azure AD 進行目錄同步作業。 先前預設使用 TLS1.0。
* Azure AD Connect 密碼同步化代理程式啟動時，會嘗試連線到 Azure AD 已知端點進行密碼同步化。 連線成功時，會重新導向到特定端點。 以前，密碼同步化代理程式會快取區域特定的端點，直到重新啟動為止。 現在如果代理程式遇到區域特定端點的連線問題，會清除快取，並重試已知端點。 快取的區域特定端點無法繼續使用時，這項變更可確保密碼同步化可以容錯移轉至不同的區域特定端點。
* 若要同步處理內部部署 AD 樹系的變更，需要 AD DS 帳戶。 您可以 (i) 自行建立 AD DS 帳戶，並將其認證提供給 Azure AD Connect，或 (ii) 提供企業系統管理員的認證，並讓 Azure AD Connect 為您建立 AD DS 帳戶。 過去，(i) 為 Azure AD Connect 精靈的預設選項。 現在，(ii) 是預設選項。

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>新功能和改進
* 已新增 Microsoft Azure Government Cloud 和 Microsoft Cloud Germany 的支援。

### <a name="ad-fs-management"></a>AD FS 管理
#### <a name="fixed-issues"></a>已修正的問題
* AD 準備 PowerShell 模組中的初始化 ADSyncNGCKeysWriteBack cmdlet 錯誤地將 ACL 應用於設備註冊容器,因此僅繼承現有許可權。  已經過更新，因此同步服務帳戶會有正確的權限。

#### <a name="new-features-and-improvements"></a>新功能和改進
* AAD Connect 驗證 ADFS 登入工作已更新，因此會依照 Microsoft Online 驗證登入，而非僅從 ADFS 擷取權杖。
* 使用 AAD Connect 設定新的 ADFS 伺服器陣列時，要求提供 ADFS 認證的頁面已移動，因此現在會在要求使用者提供 ADFS 和 WAP 伺服器之前出現。  這可讓 AAD Connect 檢查指定的帳戶是否有正確的權限。
* 在 AAD Connect 升級期間，如果 ADFS AAD Trust 無法更新，升級不會再失敗。  如果發生這種情況，使用者將看見適當的警告訊息，並且應該透過 AAD Connect 其他工作繼續重設該信任。

### <a name="seamless-single-sign-on"></a>無縫單一登入
#### <a name="fixed-issues"></a>已修正的問題
* 已修正嘗試啟用[無縫單一登入](how-to-connect-sso.md)會導致 Azure AD Connect 精靈傳回錯誤的問題。 錯誤訊息是 *「Microsoft Azure AD Connect 驗證代理程式的設定失敗。」* 此問題會影響到已按照此篇[文章](how-to-connect-pta-upgrade-preview-authentication-agents.md)所述的步驟手動升級[傳遞驗證](how-to-connect-sso.md)的預覽版驗證代理程式的客戶。


## <a name="115610"></a>1.1.561.0
狀態：2017 年 7 月 23 日

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>已修正的問題

* 已修正導致現成同步處理規則 “Out to AD - User ImmutableId” 遭到移除的問題：

  * 升級 Azure AD Connect 時，或是使用 Azure AD Connect 精靈中的工作選項 [更新同步處理設定]** 來更新Azure AD Connect 同步處理設定時，即會發生此問題。
  
  * 此同步處理規則適用於已啟用 [ms-DS-ConsistencyGuid 作為來源錨點功能](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)的客戶。 此功能已在版本 1.1.524.0 和之後版本中引入。 移除同步處理規則之後，Azure AD Connect 就不再使用 ObjectGuid 屬性值填入內部部署 AD ms-DS-ConsistencyGuid 屬性。 它無法防止將新的使用者佈建至 Azure AD。
  
  * 只要已啟用此功能，此修正就可確保在升級期間或設定變更期間，將不再移除同步處理規則。 對於已受到此問題影響的現有客戶，此修正也可確保在升級至這個版本的 Azure AD Connect 之後，會重新新增此同步處理規則。

* 已修正導致現成同步處理規則之優先順序值小於 100 的問題：

  * 通常會將優先順序值 0-99 保留給自訂同步處理規則。 在升級期間，會更新現成同步處理規則的優先順序值，以容納同步處理規則變更。 由於發生此問題，因而可能會為現成同步處理規則指派小於 100 的優先順序值。
  
  * 此修正可防止在升級期間發生此問題。 不過，它不會針對已受到此問題影響的現有客戶還原優先順序值。 未來將提供個別的修正，以協助進行還原。

* 已修正即使已啟用 OU 型篩選，Azure AD Connect 精靈中的 [[網域和 OU 篩選] 畫面](how-to-connect-install-custom.md#domain-and-ou-filtering)還是會顯示已選取 [同步所有網域及 OU]** 選項的問題。

*   已修正導致 Synchronization Service Manager 中的 [[設定目錄分割] 畫面](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)在按一下 [重新整理]** 按鈕時傳回錯誤的問題。 錯誤訊息是「重新整理網域時發生錯誤：無法將物件從 'System.Collections.ArrayList' 類型轉換為 ‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject' 類型。」** 將新的 AD 網域新增至現有的 AD 樹系，而且您嘗試使用 [重新整理] 按鈕更新 Azure AD Connect 時，就會發生此錯誤。

#### <a name="new-features-and-improvements"></a>新功能和改進

* [自動升級功能](how-to-connect-install-automatic-upgrade.md)已擴充，能夠支援具備下列設定的客戶：
  * 您已啟用裝置回寫功能。
  * 您已啟用群組回寫功能。
  * 安裝不是快速設定或 DirSync 升級。
  * Metaverse 中的物件超過 100,000 個。
  * 您正連接到多個樹系。 快速安裝只會連接到一個樹系。
  * AD 連接器帳戶已不再是預設的 MSOL_ 帳戶。
  * 伺服器已設定為處於預備模式。
  * 您已啟用使用者回寫功能。
  
  >[!NOTE]
  >[自動升級] 功能的範圍擴充會影響使用 Azure AD Connect 組建 1.1.105.0 和之後版本的客戶。 如果您不想讓 Azure AD Connect 伺服器自動升級，則必須在 Azure AD Connect 伺服器上執行下列 Cmdlet：`Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`。 如需啟用/停用自動升級的詳細資訊，請參閱 [Azure AD Connect︰自動升級](how-to-connect-install-automatic-upgrade.md)一文。

## <a name="115580"></a>1.1.558.0
狀態：將不會發行。 這個組建中的變更隨附於版本 1.1.561.0 中。

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>已修正的問題

* 已修正導致在更新 OU 型篩選設定時，讓現成同步處理規則 “Out to AD - User ImmutableId” 遭到移除的問題。 需要有此同步處理規則，才能將 [ms-DS-ConsistencyGuid 作為來源錨點功能](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)。

* 已修正即使已啟用 OU 型篩選，Azure AD Connect 精靈中的 [[網域和 OU 篩選] 畫面](how-to-connect-install-custom.md#domain-and-ou-filtering)還是會顯示已選取 [同步所有網域及 OU]** 選項的問題。

*   已修正導致 Synchronization Service Manager 中的 [[設定目錄分割] 畫面](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)在按一下 [重新整理]** 按鈕時傳回錯誤的問題。 錯誤訊息是「重新整理網域時發生錯誤：無法將物件從 'System.Collections.ArrayList' 類型轉換為 ‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject' 類型。」** 將新的 AD 網域新增至現有的 AD 樹系，而且您嘗試使用 [重新整理] 按鈕更新 Azure AD Connect 時，就會發生此錯誤。

#### <a name="new-features-and-improvements"></a>新功能和改進

* [自動升級功能](how-to-connect-install-automatic-upgrade.md)已擴充，能夠支援具備下列設定的客戶：
  * 您已啟用裝置回寫功能。
  * 您已啟用群組回寫功能。
  * 安裝不是快速設定或 DirSync 升級。
  * Metaverse 中的物件超過 100,000 個。
  * 您正連接到多個樹系。 快速安裝只會連接到一個樹系。
  * AD 連接器帳戶已不再是預設的 MSOL_ 帳戶。
  * 伺服器已設定為處於預備模式。
  * 您已啟用使用者回寫功能。
  
  >[!NOTE]
  >[自動升級] 功能的範圍擴充會影響使用 Azure AD Connect 組建 1.1.105.0 和之後版本的客戶。 如果您不想讓 Azure AD Connect 伺服器自動升級，則必須在 Azure AD Connect 伺服器上執行下列 Cmdlet：`Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`。 如需啟用/停用自動升級的詳細資訊，請參閱 [Azure AD Connect︰自動升級](how-to-connect-install-automatic-upgrade.md)一文。

## <a name="115570"></a>1.1.557.0
狀態：2017 年 7 月

>[!NOTE]
>此組建無法透過 Azure AD Connect 自動升級功能提供客戶使用。

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>已修正的問題
* 已修正 Initialize-ADSyncDomainJoinedComputerSync Cmdlet 的問題，此問題會導致現有服務連接點物件上設定的驗證網域遭到變更，即使它仍是有效網域也一樣。 當您的 Azure AD 租用戶具有多個驗證網域可用來設定服務連接點時，就會發生此問題。

#### <a name="new-features-and-improvements"></a>新功能和改進
* 密碼回寫現在適用於透過 Microsoft Azure Government 雲端和 Microsoft Cloud Germany 進行預覽。 如需 Azure AD Connect 支援不同服務執行個體的詳細資訊，請參閱 [Azure AD Connect：執行個體的特殊考量](reference-connect-instances.md)。

* Initialize-ADSyncDomainJoinedComputerSync Cmdlet 現在有一個名為 AzureADDomain 的新選擇性參數。 這個參數可讓您指定要使用哪一個驗證網域來設定服務連接點。

### <a name="pass-through-authentication"></a>傳遞驗證

#### <a name="new-features-and-improvements"></a>新功能和改進
* 傳遞驗證所需的代理程式名稱已從 Microsoft Azure AD 應用程式 Proxy 連接器** 變更為 Microsoft Azure AD Connect 驗證代理程式**。

* 啟用傳遞驗證預設不再啟用密碼雜湊同步處理。


## <a name="115530"></a>1.1.553.0
狀態：2017 年 6 月

> [!IMPORTANT]
> 這個組建引進了結構描述和同步處理規則變更。 升級之後，Azure AD Connect 同步處理服務將會觸發完整匯入和完整同步處理步驟。 變更的詳細資料如下所述。 若要在升級之後，暫時延遲完整匯入和完整同步處理步驟，請參閱[如何延遲升級之後的完整同步處理](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)。
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 同步處理

#### <a name="known-issue"></a>已知問題
* 存在一個問題,會影響使用 Azure AD 連接同步使用[基於 OU 篩選](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)的客戶。當您瀏覽到 Azure AD 連接精靈中的[「網域」和「OU 篩選」頁](how-to-connect-install-custom.md#domain-and-ou-filtering)時,應執行以下行為:
  * 如果已啟用 OU 型篩選，即會選取 **[同步所選取的網域及 OU] 選項**。
  * 否則，會選取 [同步所有網域及 OU]**** 選項。

發生的問題是當您執行精靈時，一律會選取 **[同步所有網域及 OU] 選項**。  即使先前已設定 OU 型篩選，還是會發生此問題。 在儲存任何 AAD Connect 設定變更之前，請確定**已選取 [同步所選取的網域及 OU] 選項**，並確認已重新啟用所有需要同步處理的 OU。 否則，將會停用 OU 型篩選。

#### <a name="fixed-issues"></a>已修正的問題

* 已修正密碼回寫的問題，可讓 Azure AD 系統管理員重設內部部署中有 AD 特殊權限之使用者帳戶的密碼。 在透過具特殊權限的帳戶為 Azure AD Connect 授與重設密碼權限時，就會發生此問題。 此問題在這個版本的 Azure AD Connect 中已獲解決，方法是不允許 Azure AD 系統管理員重設內部部署中有 AD 特殊權限之任意使用者帳戶的密碼，除非該系統管理員是該帳戶的擁有者。 如需詳細資訊，請參閱[資訊安全諮詢 4033453](https://technet.microsoft.com/library/security/4033453)。

* 已修正與 [ms-DS-ConsistencyGuid 作為來源錨點](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能相關的問題，其中 Azure AD Connect 不會回寫至內部部署 AD ms-DS-ConsistencyGuid 屬性。 有多個已新增至 Azure AD Connect 的內部部署 AD 樹系，而且已選取 *[使用者識別身分存在於多個目錄之間] 選項*時，即會發生此問題。 使用這類設定時，Metaverse 中的結果同步處理規則就不會填入 sourceAnchorBinary 屬性。 sourceAnchorBinary 屬性可用來作為 ms-DS-ConsistencyGuid 屬性的來源屬性。 如此一來，就不會發生回寫至 ms-DSConsistencyGuid 屬性。 為了修正此問題，已更新下列同步處理規則，以確保一律會填入 Metaverse 中的 sourceAnchorBinary 屬性：
  * In from AD - InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * In from AD - User AccountEnabled.xml
  * In from AD - User Common.xml
  * In from AD - User Join SOAInAAD.xml

* 在過去，即使未啟用 [ms-DS-ConsistencyGuid 作為來源錨點](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能，仍會將 “Out to AD – User ImmutableId” 同步處理規則新增至 Azure AD Connect。 效果是良性的，而且不會發生 ms-DS-ConsistencyGuid 屬性的回寫。 為了避免混淆，已新增邏輯，以確保只會在啟用此功能時新增同步處理規則。

* 已修正導致密碼雜湊同步處理失敗且產生錯誤事件 611 的問題。 從內部部署 AD 中移除一或多個網域控制站之後，即會發生此問題。 在每個密碼同步處理週期結束時，由內部部署 AD 所發出的同步處理 Cookie 會包含已移除網域控制站的呼叫識別碼且 USN (更新序列號碼) 值為 0。 密碼同步處理管理員無法保存包含 USN 值為 0 的同步處理 Cookie，其會失敗並產生錯誤事件 611。 在下一個同步處理週期中，密碼同步處理管理員會重複使用最後一個保存的同步處理 Cookie，其不包含 0 的 USN 值。 這會導致重新同步處理相同的密碼變更。 透過此修正，密碼同步處理管理員會正確保存同步處理 Cookie。

* 在過去，即使已使用 Set-ADSyncAutoUpgrade Cmdlet 停用自動升級，自動升級程序仍會繼續定期檢查升級，並依賴下載的安裝程式來遵守停用。 透過此修正，自動升級程序便不再定期檢查升級。 當適用於此 Azure AD Connect 版本的升級安裝程式執行一次之後，即會自動套用此修正。

#### <a name="new-features-and-improvements"></a>新功能和改進

* 在過去，[ms-DS-ConsistencyGuid 作為來源錨點](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)功能僅適用於新的部署。 現在，它適用於現有的部署。 具體而言：
  * 若要存取此功能，請開啟 Azure AD Connect 精靈，然後選擇 [更新來源錨點]** 選項。
  * 只有使用 objectGuid 作為 sourceAnchor 屬性的現有部署才能看見此選項。
  * 設定此選項時，精靈會驗證內部部署 Active Directory 中 ms-DS-ConsistencyGuid 屬性的狀態。 如果目錄中沒有任何使用者物件設定了此屬性，精靈就會使用 ms-DS-ConsistencyGuid 作為 sourceAnchor 屬性。 如果目錄中有一或多個使用者物件設定了此屬性，則精靈會認為其他應用程式正在使用此屬性，因此，不適合將其作為 sourceAnchor 屬性，並且不允許來源錨點變更繼續執行。 如果您確定現有應用程式並沒有使用此屬性，則請連絡支援人員，以了解該如何隱藏此錯誤。

* 專屬於裝置物件上的 **userCertificate** 屬性，Azure AD Connect 現在會尋找[將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) 所需的憑證值，並且在同步處理至 Azure AD 之前篩選出其餘部分。 為了啟用此行為，已更新現成的同步處理規則 “Out to AAD - Device Join SOAInAD”。

* Azure AD Connect 現在支援 Exchange Online **cloudPublicDelegates** 屬性到內部部署 AD **publicDelegates** 屬性的回寫。 這會啟用下列案例：Exchange Online 信箱可授與 SendOnBehalfTo 權限給使用內部部署 Exchange 信箱的使用者。 為了支援此功能，已新增新的現成同步處理規則 “Out to AD – User Exchange Hybrid PublicDelegates writeback”。 只有在啟用 Exchange 混合功能時，才會將此同步處理規則新增至 Azure AD Connect。

* Azure AD Connect 現在支援從 Azure AD 同步處理 **altRecipient** 屬性。 為了支援此變更，已更新下列現成的同步處理規則以包含必要的屬性流程：
  * In from AD – User Exchange
  * Out to AAD – User ExchangeOnline
  
* Metaverse 中的 **cloudSOAExchMailbox** 屬性會指出指定的使用者是否具有 Exchange Online 信箱。 它的定義已更新，以包含其他 Exchange Online RecipientDisplayTypes 作為這類設備與會議室信箱。 為了啟用此變更，已將 cloudSOAExchMailbox 屬性的定義 (可在現成的同步處理規則 “In from AAD – User Exchange Hybrid” 下方找到) 從：

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

...更新為下列內容：

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* 已新增下列這組 X509Certificate2 相容的函式，可用來建立同步處理規則運算式，以處理 userCertificate 屬性中的憑證值：

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|選取|
    |CertKeyAlgorithmParams|CertHashString|Where|
    |||含有|

* 已引進下列結構描述變更，以允許客戶建立自訂的同步處理規則來流動群組物件的 sAMAccountName、domainNetBios 和 domainFQDN，以及使用者物件的 distinguishedName：

  * 已將下列屬性新增至 MV 結構描述中：
    * 群組：AccountName
    * 群組：domainNetBios
    * 群組：domainFQDN
    * 人員：distinguishedName

  * 已將下列屬性新增至 Azure AD Connector 結構描述中：
    * 群組：OnPremisesSamAccountName
    * 群組：NetBiosName
    * 群組：DnsDomainName
    * 使用者：OnPremisesDistinguishedName

* ADSyncDomainJoinedComputerSync Cmdlet 現在有一個名為 AzureEnvironment 的新選擇性參數。 此參數可用來指定裝載對應 Azure Active Directory 租用戶的區域。 有效值包括：
  * AzureCloud (預設值)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* 已更新的同步處理規則編輯器，可在同步處理規則建立期間，使用聯結 (而非佈建) 作為連結類型的預設值。

### <a name="ad-fs-management"></a>AD FS 管理

#### <a name="issues-fixed"></a>已修正的問題

* 下列 URL 是由 Azure AD 所引進的新 WS-同盟端點，可改善驗證中斷的復原，而且將會新增至內部部署 AD FS 信賴憑證者信任設定中：
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* 已修正導致 AD FS 針對 IssuerID 產生錯誤宣告值的問題。 如果 Azure AD 租用戶中有多個已驗證的網域，且用來產生 IssuerID 宣告之 userPrincipalName 屬性的網域尾碼深度至少有 3 層 (例如 johndoe@us.contoso.com)，就會發生此問題。 已藉由更新宣告規則所使用的 RegEx 來解決此問題。

#### <a name="new-features-and-improvements"></a>新功能和改進
* 在過去，Azure AD Connect 所提供的 ADFS 憑證管理功能僅能與透過 Azure AD Connect 管理的 ADFS 伺服器陣列搭配使用。 現在，您可以將此功能與未使用 Azure AD Connect 管理的 ADFS 伺服器陣列搭配使用。

## <a name="115240"></a>1.1.524.0
發行日期：2017 年 5 月

> [!IMPORTANT]
> 這個組建引進了結構描述和同步處理規則變更。 升級之後，Azure AD Connect 同步處理服務將會觸發完整匯入和完整同步處理步驟。 變更的詳細資料如下所述。
>
>

**已修正的問題：**

Azure AD Connect 同步處理

* 已修正導致下列情況的問題：即使客戶已使用 Set-ADSyncAutoUpgrade Cmdlet 停用此功能，Azure AD Connect 伺服器仍發生自動升級。 透過此修正，伺服器上的自動升級程序仍會定期檢查升級，但下載的安裝程式會接受自動升級組態。
* 在 DirSync 就地升級期間，Azure AD Connect 會建立 Azure AD 服務帳戶，以供 Azure AD 連接器用來與 Azure AD 同步處理。 建立帳戶之後，Azure AD Connect 會使用此帳戶向 Azure AD 進行驗證。 有時候，驗證會因為暫時性問題而失敗，這會導致 DirSync 就地升級失敗，並出錯「實行設定 AAD 同步工作時發生錯誤︰AADSTS50034︰若要登入此應用程式，必須將帳戶新增至 xxx.onmicrosoft.com 目錄」錯誤。** 為了改善 DirSync 升級的彈性，Azure AD Connect 現在會重試驗證步驟。
* 組建 443 發生問題，其導致 DirSync 就地升級成功，但未建立目錄同步作業所需的執行設定檔。 修復邏輯已包含在 Azure AD Connect 的此組建中。 當客戶升級到此組建時，Azure AD Connect 會偵測遺漏的執行設定檔並加以建立。
* 已修正導致下列情況的問題：密碼同步處理程序無法啟動，出現事件識別碼 6900 和「已新增具有相同索引鍵的項目」** 錯誤。 如果您將 OU 篩選組態更新為包含 AD 組態分割，就會發生此問題。 若要修正此問題，密碼同步處理程序現在只會從 AD 網域分割同步處理密碼變更。 系統會略過非網域分割，例如組態分割。
* 在快速安裝期間，Azure AD Connect 會建立內部部署 AD DS 帳戶，以供 AD 連接器用來與內部部署 AD 通訊。 先前建立此帳戶時，PASSWD_NOTREQD 旗標設定於 user-Account-Control 屬性上，而隨機密碼會設定於此帳戶上。 現在，Azure AD Connect 會在密碼設定於帳戶之後，明確地移除 PASSWD_NOTREQD 旗標。
* 已修正導致下列情況的問題：在內部部署 AD 結構描述中找到 mailNickname 屬性，但該屬性並未繫結至 AD 使用者物件類別時，DirSync 升級失敗，並出現「嘗試取得應用程式鎖定的 SQL Server 發生死結」** 錯誤。
* 已修正導致下列情況的問題：當系統管理員使用 Azure AD Connect 精靈更新 Azure AD Connect 同步處理組態時，裝置回寫功能自動停用。 這個問題是由針對內部部署 AD 中現有裝置回寫設定執行先決條件檢查的精靈所造成，而且檢查會失敗。 如果先前已啟用裝置回寫，此修正就會略過檢查。
* 若要設定 OU 篩選，您可以使用 Azure AD Connect 精靈或 Synchronization Service Manager。 先前，如果您使用 Azure AD Connect 精靈來設定 OU 篩選，則會包含後來建立的新 OU 以便進行目錄同步作業。 如果您不想要包含新的 OU，您必須使用 Synchronization Service Manager 設定 OU 篩選。 現在，您可以使用 Azure AD Connect 精靈達成相同的行為。
* 已修正導致下列情況的問題：Azure AD Connect 所需的預存程序建立於安裝系統管理員的結構描述之下，而不是建立於 dbo 結構描述之下。
* 已修正導致下列情況的問題：AAD Connect 伺服器事件記錄中省略 Azure AD 所傳回的 TrackingId 屬性。 如果 Azure AD Connect 收到來自 Azure AD 的重新導向訊息，且 Azure AD Connect 無法連線到所提供的端點，就會發生此問題。 在疑難排解期間，支援工程師可以使用 TrackingId 與服務端記錄相互關聯。
* 當 Azure AD 連接收到 Azure AD 的大物件錯誤時,Azure AD Connect 將生成事件 ID 6941 的事件,並顯示 *「預配的物件太大。修剪此物件上的屬性值數。* 在此同時，Azure AD Connect 也會產生誤導事件 (EventID 6900) 並出現「Microsoft.Online.Coexistence.ProvisionRetryException︰無法與 Windows Azure Active Directory 服務通訊」** 訊息。 為了避免混淆，Azure AD Connect 不再於收到 LargeObject 錯誤時產生後面的事件。
* 已修正導致下列情況的問題：Synchronization Service Manager 在嘗試更新「一般 LDAP 連接器」的組態時變得沒有反應。

**新功能/改進︰**

Azure AD Connect 同步處理
* 同步處理規則變更 – 已實作下列同步處理規則變更︰
  * 已將預設同步處理規則集更新為 **userCertificate** 和 **userSMIMECertificate** 屬性有超過 15 個值時不匯出屬性。
  * AD 屬性 **employeeID** 和 **msExchBypassModerationLink** 現在已包含在預設同步處理規則集中。
  * 已經從預設同步處理規則集中移除 AD 屬性 **photo**。
  * 已將 **preferredDataLocation** 新增至 Metaverse 結構描述和 AAD 連接器結構描述。 想要在 Azure AD 中更新任一屬性的客戶若要這樣做，可以實作自訂同步處理規則。 
  * 已將 **userType** 新增至 Metaverse 結構描述和 AAD 連接器結構描述。 想要在 Azure AD 中更新任一屬性的客戶若要這樣做，可以實作自訂同步處理規則。

* Azure AD Connect 現在會自動啟用 ConsistencyGuid 屬性的使用，以作為內部部署 AD 物件的來源錨點屬性。 此外，如果 ConsistencyGuid 屬性為空，Azure AD Connect 就會使用 objectGuid 屬性值加以填入。 這項功能僅適用於新的部署。 若要深入了解此功能，請參閱 [Azure AD Connect︰設計概念 - 使用 ms-DS-ConsistencyGuid 作為 sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 文章章節。
* 已新增疑難排解 Cmdlet Invoke-ADSyncDiagnostics，可協助診斷密碼雜湊同步處理相關問題。 如需使用此 Cmdlet 的相關資訊，請參閱[針對使用 Azure AD Connect 同步執行的密碼雜湊同步處理進行疑難排解](tshoot-connect-password-hash-synchronization.md)一文。
* Azure AD Connect 現在支援將擁有郵件功能的公用資料夾物件從內部部署 AD 同步處理到 Azure AD。 您可以在 [選用功能] 之下使用 Azure AD Connect 精靈啟用此功能。 若要瞭解有關此功能的詳細資訊，請參閱 [Office 365 目錄架構邊緣封鎖支援內部部署中擁有郵件功能的公用資料夾](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders) \(英文\) 一文。
* Azure AD Connect 要求 AD DS 帳戶從內部部署 AD 進行同步處理。 在過去，如果您使用快速模式安裝 Azure AD Connect，就可以提供企業系統管理員帳戶的認證，且 Azure AD Connect 會建立所需的 AD DS 帳戶。 不過，針對自訂安裝以及將樹系新增至現有的部署，會要求您改為提供 AD DS 帳戶。 現在，您也可以選擇在自訂安裝期間提供企業系統管理員帳戶的認證，讓 Azure AD Connect 能夠建立所需的 AD DS 帳戶。
* Azure AD Connect 現在支援 SQL AOA。 您必須在安裝 Azure AD Connect 之前啟用 SQL AOA。 在安裝期間，Azure AD Connect 會偵測所提供的 SQL 執行個體是否已啟用 SQL AOA。 如果已啟用 SQL AOA，Azure AD Connect 會進一步指出 SQL AOA 已設定為使用同步複寫或非同步複寫。 設定可用性群組接聽程式時，建議將 RegisterAllProvidersIP 屬性設定為 0。 這個建議是因為 Azure AD Connect 目前使用 SQL Native Client 來連線至 SQL，且 SQL Native Client 不支援使用 MultiSubNetFailover 屬性。
* 如果您使用 LocalDB 作為 Azure AD Connect 伺服器的資料庫，而且已達到其 10 GB 大小的限制，同步處理服務便無法再啟動。 先前，您需要在 LocalDB 上執行 ShrinkDatabase 作業，以收回足夠的 DB 空間以供同步處理服務啟動。 在那之後，您可以使用 Synchronization Service Manager 來刪除執行歷程記錄，以回收更多的 DB 空間。 現在，您可以使用 Start-ADSyncPurgeRunHistory Cmdlet 來清除 LocalDB 中的執行歷程記錄資料，以回收 DB 空間。 此外，此 Cmdlet 支援可在同步處理服務未執行時使用的離線模式 (藉由指定 -offline 參數)。 注意︰只有在同步處理服務未執行且使用的資料庫是 LocalDB 時，才能使用離線模式。
* 為了減少所需的儲存體空間，Azure AD Connect 現在會在 LocalDB/SQL 資料庫中儲存同步處理錯誤詳細資料前加以壓縮。 從舊版 Azure AD Connect 升級到此版本時，Azure AD Connect 會對現有的同步處理錯誤詳細資料執行一次性壓縮。
* 先前，在更新 OU 篩選組態之後，您必須手動執行完整匯入，以確保目錄同步作業正確包含/排除現有的物件。 現在，Azure AD Connect 會在下一個同步處理週期內自動觸發完整匯入。 此外，完整匯入只會套用至受到更新影響的 AD 連接器。 注意︰這項改進只適用於使用 Azure AD Connect 精靈進行的 OU 篩選更新。 不適用於使用 Synchronization Service Manager 進行的 OU 篩選更新。
* 先前，以群組為基礎的篩選僅支援使用者、群組和連絡人物件。 現在，以群組為基礎的篩選也支援電腦物件。
* 先前，您可以刪除連接器空間資料，而不需停用 Azure AD Connect 同步排程器。 現在，如果 Synchronization Service Manager 偵測到已啟用排程器，則會阻止刪除連接器空間資料。 此外，還會傳回警告，通知客戶有關刪除連接器空間資料可能會遺失資料。
* 先前，您必須停用 PowerShell 轉譯，Azure AD Connect 精靈才能正常執行。 此問題已部份解決。 如果您使用 Azure AD Connect 精靈來管理同步處理組態，您可以啟用 PowerShell 轉譯。 如果您使用 Azure AD Connect 精靈來管理 ADFS 組態，您必須停用 PowerShell 轉譯。



## <a name="114860"></a>1.1.486.0
發行日期︰2017 年 4 月

**已修正的問題：**
* 修正 Azure AD Connect 無法在已當地語系化的 Windows Server 版本上成功安裝的問題。

## <a name="114840"></a>1.1.484.0
發行日期︰2017 年 4 月

**已知問題:**

* 如果下列條件都成立，此 Azure AD Connect 版本將無法成功安裝：
   1. 您執行 DirSync 就地升級或全新的 Azure AD Connect 安裝。
   2. 您使用已當地語系化的 Windows Server 版本，其中伺服器上內建的「系統管理員」群組名稱不是 "Administrators"。
   3. 您使用與 Azure AD Connect 一起安裝的預設 SQL Server 2012 Express LocalDB，而不是提供自己的完整 SQL。

**已修正的問題：**

Azure AD Connect 同步處理
* 修正當一或多個連接器遺漏某個同步處理步驟的執行設定檔時，同步排程器會略過該整個同步處理步驟的問題。 例如，您已使用 Synchronization Service Manager 來手動新增某個連接器，但沒有為它建立「差異匯入」執行設定檔。 這項修正可確保同步排程器會繼續執行其他連接器的「差異匯入」。
* 修正「同步處理服務」在遇到其中一個執行步驟發生問題時，會立即停止處理執行設定檔的問題。 這項修正可確保「同步處理服務」會略過該執行步驟並繼續處理其餘步驟。 例如，您有一個「差異匯入」執行設定檔，用於具有多個執行步驟 (每個內部部署 AD 網域各有一個執行步驟) 的 AD 連接器。 即使其中一個 AD 網域有網路連線問題，「同步處理服務」也會在其他 AD 網域執行「差異匯入」。
* 修正導致在進行「自動升級」時略過 Azure AD Connector 更新的問題。
* 修正導致 Azure AD Connect 在設定時不正確地判斷伺服器是否為網域控制站的問題，此問題會進而導致 DirSync 升級失敗。
* 修正導致 DirSync 就地升級不會為 Azure AD Connector 建立任何執行設定檔的問題。
* 修正在嘗試設定「一般 LDAP 連接器」時，Synchronization Service Manager 使用者介面變成沒有反應的問題。

AD FS 管理
* 修正在已將 AD FS 主要節點移至另一部伺服器的情況下，Azure AD Connect 精靈會發生失敗的問題。

傳統型 SSO
* 修正 Azure AD Connect 精靈中的問題，也就是在進行新安裝時，如果您選擇 [密碼同步處理] 作為登入選項，[登入] 畫面將不會讓您啟用 [傳統型 SSO] 功能。

**新功能/改進︰**

Azure AD Connect 同步處理
* Azure AD Connect Sync 現在支援使用「虛擬服務帳戶」、「受控服務帳戶」及「群組受控服務帳戶」作為其服務帳戶。 這僅適用於新的 Azure AD Connect 安裝。 安裝 Azure AD Connect 時：
    * 根據預設，Azure AD Connect 精靈會建立一個「虛擬服務帳戶」，並使用它作為其服務帳戶。
    * 如果您是在網域控制站上進行安裝，Azure AD Connect 就會回復成先前的行為，也就是會建立網域使用者帳戶，並改用它作為服務帳戶。
    * 您可以透過提供下列其中一項，來覆寫預設的行為：
      * 群組受控服務帳戶
      * 受控服務帳戶
      * 網域使用者帳戶
      * 本機使用者帳戶
* 以前，如果您升級至包含連接器更新或同步處理規則變更的新 Azure AD Connect 組建，Azure AD Connect 會觸發一個完整的同步處理週期。 現在，Azure AD Connect 會選擇性地僅針對具有更新的連接器觸發「完整匯入」步驟，以及僅針對具有同步處理規則變更的連接器觸發「完整同步處理」步驟。
* 以前，「匯出刪除閾值」僅適用於透過同步排程器觸發的匯出。 現在，該功能已延伸到包含由客戶使用 Synchronization Service Manager 來手動觸發的匯出。
* 在您的 Azure AD 租用戶上，有一個指出是否已為租用戶啟用「密碼同步處理」功能的服務組態。 以前，當您有作用中伺服器和預備環境伺服器時，Azure AD Connect 很容易以不正確的方式設定服務組態。 現在，Azure AD Connect 會嘗試讓服務組態只與您的作用中 Azure AD Connect 伺服器保持一致。
* 如果內部部署 AD 未啟用「AD 資源回收筒」，Azure AD Connect 精靈現在會偵測到並傳回警告。
* 以前，如果批次中物件的組合大小超出特定閾值，「匯出至 Azure AD」就會逾時。 現在，如果遇到此問題，「同步處理服務」會重新嘗試以個別、較小的批次重新傳送物件。
* 「同步處理服務金鑰管理」應用程式已從 Windows [開始] 功能表中移除。 將會透過命令列介面使用 miiskmu.exe 來繼續支援加密金鑰的管理。 如需有關加密金鑰的資訊，請參閱[放棄 Azure AD Connect Sync 加密金鑰](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key)一文。
* 以前，如果您變更 Azure AD Connect 同步服務帳戶密碼，「同步處理服務」將會無法正確啟動，直到您放棄加密金鑰並將 Azure AD Connect 同步服務帳戶密碼重新初始化為止。 現在，不再需要此程序。

傳統型 SSO

* 設定「傳遞驗證」和「傳統型 SSO」時，Azure AD Connect 精靈已不再需要於網路上開啟連接埠 9090。 只需要開啟連接埠 443。 

## <a name="114430"></a>1.1.443.0
發行日期︰2017 年 3 月

**已修正的問題：**

Azure AD Connect 同步處理
* 修正當「Azure AD 連接器」的顯示名稱不包含指派給 Azure AD 租用戶的初始 onmicrosoft.com 網域時，會造成 Azure AD Connect 精靈發生失敗的問題。
* 修正當「同步處理服務帳戶」的密碼包含特殊字元 (例如單引號、冒號及空格) 時，會造成 Azure AD Connect 精靈在連線到 SQL Database 時發生失敗的問題。
* 修復了在暫存模式下在 Azure AD Connect 伺服器上出現的錯誤「映射具有與圖像不同的錨點」,之後您暫時排除了本地 AD 物件進行同步,然後再次包含該物件進行同步。
* 修正在您暫時將內部部署 AD 物件從同步處理作業中排除又再重新包含在同步處理作業中之後，會造成在處於預備模式的 Azure AD Connect 伺服器上發生「DN 所定位的物件是虛設項目」錯誤的問題。

AD FS 管理
* 修正在設定「替代登入識別碼」之後，Azure AD Connect 精靈不會更新 AD FS 組態並在信賴憑證者信任上設定正確宣告的問題。
* 修正 Azure AD Connect 精靈對於服務帳戶是使用 userPrincipalName 格式而非 sAMAccountName 格式來設定的 AD FS 伺服器，無法正確進行處理的問題。

傳遞驗證
* 修正在已選取「傳遞驗證」但註冊其連接器失敗的情況下，會造成 Azure AD Connect 精靈發生失敗的問題。
* 修正在已啟用「傳統型 SSO」功能的情況下，會造成 Azure AD Connect 精靈在所選登入方法上略過驗證檢查的問題。

密碼重設
* 已修正如果防火牆或 Proxy 伺服器已清除連線，可能導致 Azure AAD Connect 伺服器不會嘗試重新連線的問題。

**新功能/改進︰**

Azure AD Connect 同步處理
* Get-ADSyncScheduler Cmdlet 現在會傳回一個名為 SyncCycleInProgress 的新布林值屬性。 如果傳回的值為 true，即表示有已排定的同步處理循環正在進行。
* 用來儲存 Azure AD Connect 安裝和設定記錄的目的地資料夾已經從 %localappdata%\AADConnect 移到 %programdata%\AADConnect，以提升記錄的存取便利性。

AD FS 管理
* 添加了對更新 AD FS 伺服器場 TLS/SSL 憑證的支援。
* 新增對管理 AD FS 2016 的支援。
* 您現在可以在安裝 AD FS 時指定現有的 gMSA (群組受控服務帳戶)。
* 您現在可以為 Azure AD 信賴憑證者信任設定 SHA-256 作為簽章雜湊演算法。

密碼重設
* 已導入允許產品在具有更嚴格防火牆規則的環境中運作的增強功能。
* 已改良對 Azure 服務匯流排的連線可靠性。

## <a name="113800"></a>1.1.380.0
發行日期︰2016 年 12 月

**已修復問題:**

* 修正此組建中遺漏 Active Directory Federation Services (AD FS) 之 issuerid 宣告規則的問題。

>[!NOTE]
>此組建無法透過 Azure AD Connect 自動升級功能提供客戶使用。

## <a name="113710"></a>1.1.371.0
發行日期︰2016 年 12 月

**已知問題:**

* 此組建中遺漏 ADFS 的 issuerid 宣告規則。 如果您要讓多個網域與 Azure Active Directory (Azure AD) 同盟，就必須要有此 issuerid 宣告規則。 如果您使用 Azure AD Connect 來管理您的內部部署 ADFS 部署，則升級至此組建將會從 ADFS 組態中移除現有的 issuerid 宣告規則。 您可以在安裝/升級之後新增 issuerid 宣告規則來解決此問題。 如需有關新增 issuerid 宣告規則的詳細資料，請參閱[與 Azure AD 同盟的多網域支援](how-to-connect-install-multiple-domains.md)上的此文章。

**已修復問題:**

* 如果未開啟連接埠 9090 供輸出連線，Azure AD Connect 安裝或升級就會失敗。

>[!NOTE]
>此組建無法透過 Azure AD Connect 自動升級功能提供客戶使用。

## <a name="113700"></a>1.1.370.0
發行日期︰2016 年 12 月

**已知問題:**

* 此組建中遺漏 ADFS 的 issuerid 宣告規則。 如果您要讓多個網域與 Azure AD 同盟，就必須要有此 issuerid 宣告規則。 如果您使用 Azure AD Connect 來管理您的內部部署 ADFS 部署，則升級至此組建將會從 ADFS 組態中移除現有的 issuerid 宣告規則。 您可以在安裝/升級之後新增 issuerid 宣告規則來解決此問題。 如需有關新增 issuerid 宣告規則的詳細資料，請參閱[與 Azure AD 同盟的多網域支援](how-to-connect-install-multiple-domains.md)上的此文章。
* 連接埠 9090 必須開啟輸出以完成安裝。

**新功能:**

* 傳遞驗證 (預覽)。

>[!NOTE]
>此組建無法透過 Azure AD Connect 自動升級功能提供客戶使用。

## <a name="113430"></a>1.1.343.0
發行日期：2016 年 11 月

**已知問題:**

* 此組建中遺漏 ADFS 的 issuerid 宣告規則。 如果您要讓多個網域與 Azure AD 同盟，就必須要有此 issuerid 宣告規則。 如果您使用 Azure AD Connect 來管理您的內部部署 ADFS 部署，則升級至此組建將會從 ADFS 組態中移除現有的 issuerid 宣告規則。 您可以在安裝/升級之後新增 issuerid 宣告規則來解決此問題。 如需有關新增 issuerid 宣告規則的詳細資料，請參閱[與 Azure AD 同盟的多網域支援](how-to-connect-install-multiple-domains.md)上的此文章。

**已修正的問題：**

* 有時候，Azure AD Connect 會安裝失敗，因為無法建立密碼符合組織密碼原則所指定之複雜程度的本機服務帳戶。
* 已修正以下問題：當連接器空間中的物件同時落在某個聯結規則的範圍外以及另一個聯結規則的範圍內時，不會重新評估聯結規則。 如果有兩個或多個聯結規則的聯結條件互斥，也可能發生此問題。
* 已修正以下問題：相較於包含聯結規則的輸入同步處理規則，如果未包含聯結規則的輸入同步處理規則 (來自 Azure AD) 具有較低的優先順序值，則不會處理未包含聯結規則的輸入同步處理規則。

**改進:**

* 新增了在 Windows Server 2016 標準版或更新版本上安裝 Azure AD Connect 的支援。
* 新增了使用 SQL Server 2016 做為 Azure AD Connect 之遠端資料庫的支援。

## <a name="112810"></a>1.1.281.0
發行日期：2016 年 8 月

**已修正的問題：**

* 直到下一個同步週期完成後，才會進行同步間隔的變更。
* Azure AD Connect 精靈不接受使用者名稱以底線 (\_) 開頭的 Azure AD 帳戶。
* 如果帳戶密碼包含太多特殊字元，Azure AD Connect 精靈便無法驗證所提供的 Azure AD 帳戶。 此時會傳回「無法驗證認證。 已發生未預期的錯誤。 」錯誤訊息。
* 解除安裝預備伺服器會使 Azure AD 租用戶的密碼同步處理停用，並導致作用中伺服器的密碼同步處理失敗。
* 在使用者未儲存任何密碼雜湊的罕見情況下，密碼同步處理會失敗。
* 當 Azure AD Connect 伺服器啟用預備模式時，不會暫時停用密碼回寫。
* 當伺服器處於預備模式時，Azure AD Connect 精靈不會顯示實際的密碼同步處理和密碼回寫組態。 這些一律會顯示為停用。
* 伺服器處於預備模式時，Azure AD Connect 精靈不會保存密碼同步處理和密碼回寫的組態變更。

**改進:**

* 已更新 Start-ADSyncSyncCycle Cmdlet，可指出是否能夠順利啟動新的同步處理週期。
* 已新增 Stop-ADSyncSyncCycle Cmdlet，可終止目前正在進行中的同步處理週期和作業。
* 已更新 Stop-ADSyncScheduler Cmdlet，可終止目前正在進行中的同步處理週期和作業。
* 在 Azure AD Connect 精靈中設定[目錄擴充](how-to-connect-sync-feature-directory-extensions.md)時，現在可以選取「Teletex 字串」類型的 Azure AD 屬性。

## <a name="111890"></a>1.1.189.0
發行日期：2016 年 6 月

**已修正的問題和改進︰**

* Azure AD Connect 現在可以安裝於符合 FIPS 規範的伺服器上。
  * 針對密碼同步處理，請參閱[密碼雜湊同步處理和 FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips)。
* 已修正下列問題：NetBIOS 名稱無法解析為 Active Directory 連接器中的 FQDN。

## <a name="111800"></a>1.1.180.0
發行日期：2016 年 5 月

**新功能:**

* 如果您未先驗證網域就執行 Azure AD Connect，系統將會發出警告並協助您驗證網域。
* 已新增對 [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany)的支援。
* 已新增對最新 [Microsoft Azure Government 雲端](reference-connect-instances.md#microsoft-azure-government) 基礎結構與新的 URL 需求的支援。

**已修正的問題和改進︰**

* 對同步處理規則編輯器新增篩選，以更加容易找到同步處理規則。
* 提升刪除連接器空間時的效能。
* 修正在同一個回合中同時刪除和新增相同物件 (稱為刪除/新增) 時的問題。
* 已停用的同步處理規則不會在升級或目錄結構描述重新整理時，再重新啟用包含的物件和屬性。

## <a name="111300"></a>1.1.130.0
發行日期︰2016 年 4 月

**新功能:**

* 添加了對[目錄擴展](how-to-connect-sync-feature-directory-extensions.md)的多值屬性的支援。
* [自動升級](how-to-connect-install-automatic-upgrade.md) 已新增支援更多組態變化，以符合升級資格。
* 已新增一些適用於 [自訂排程器](how-to-connect-sync-feature-scheduler.md#custom-scheduler)的 Cmdlet。

## <a name="111190"></a>1.1.119.0
發行日期︰2016 年 3 月

**已修正的問題：**

* 請確定 Windows Server 2008 (發行前 R2) 上無法使用快速安裝，因為此作業系統不支援密碼同步處理。
* 使用自訂篩選器設定從 DirSync 升級並未如預期般運作。
* 升級至較新版本且沒有進行任何組態變更時，不應排程完整匯入/同步處理。

## <a name="111100"></a>1.1.110.0
發行日期︰2016 年 2 月

**已修正的問題：**

* 如果安裝不是位於預設的 C:\Program Files 資料夾中，則無法從舊版升級。
* 如果您安裝並在結束安裝精靈時清除 [啟動同步處理程序]****，再次執行安裝精靈將不會啟用排程器。
* 在日期/時間格式並非美國英文的伺服器上，排程器將無法正常運作。 並且會封鎖 `Get-ADSyncScheduler` 傳回正確的時間。
* 如果您是以 ADFS 做為登入選項和升級，來安裝舊版 Azure AD Connect，便無法再次執行安裝精靈。

## <a name="111050"></a>1.1.105.0
發行日期︰2016 年 2 月

**新功能:**

* [Automatic upgrade](how-to-connect-install-automatic-upgrade.md) 功能。
* 使用 Azure Multi-Factor Authentication 和 Privileged Identity Management 安裝精靈，支援全域管理。
  * 如果您使用多重要素驗證，就必須讓您的 Proxy 也允許流向 https://secure.aadcdn.microsoftonline-p.com 的流量。
  * 您必須將 https://secure.aadcdn.microsoftonline-p.com 新增至信任的網站清單，多重要素驗證才能正常運作。
* 允許在初始安裝之後變更使用者的登入方法。
* 允許在安裝精靈中使用 [網域和 OU 篩選](how-to-connect-install-custom.md#domain-and-ou-filtering)。 這也會允許連線到並非所有網域都可供使用的樹系。
* [排程器](how-to-connect-sync-feature-scheduler.md)是同步處理引擎的內建功能。

**從預覽版升級到 GA 的功能：**

* [裝置寫回](how-to-connect-device-writeback.md)。
* [目錄延伸](how-to-connect-sync-feature-directory-extensions.md)。

**新的預覽功能：**

* 新的預設同步處理週期間隔為 30 分鐘。 過去所有舊版本都是 3 小時。 已新增對變更 [排程器](how-to-connect-sync-feature-scheduler.md) 行為的支援。

**已修正的問題：**

* 驗證 DNS 網域頁面不一定都能辨識網域。
* 設定 ADFS 時，出現網域系統管理員認證提示。
* 當內部部署 AD 帳戶所在網域的 DNS 樹狀目錄與根網域不同時，安裝精靈即無法辨識這些帳戶。

## <a name="1091310"></a>1.0.9131.0
發行日期︰2015 年 12 月

**已修正的問題：**

* 您變更 Active Directory Domain Services (AD DS) 中的密碼時，密碼同步處理可能會無法作用，但是在您設定密碼時將可作用。
* 如果您有 Proxy 伺服器，在安裝期間或於組態頁面上取消升級時，向 Azure AD 進行驗證可能會失敗。
* 若您並非 SQL Server 系統管理員 (SA)，從有完整 SQL Server 執行個體的舊版 Azure AD Connect 更新將會失敗。
* 從有遠端 SQL Server 的舊版 Azure AD Connect 更新時，將顯示錯誤訊息「無法存取 ADSync SQL Database」。

## <a name="1091250"></a>1.0.9125.0
發行日期：2015 年 11 月

**新功能:**

* 可以將 ADFS 重新設定為 Azure AD 信任。
* 可以重新整理 Active Directory 結構描述並重新產生同步處理規則。
* 可以停用同步處理規則。
* 可以在同步處理規則中定義 "AuthoritativeNull" 做為新的常值。

**新的預覽功能：**

* [Azure AD 連線同步執行狀況](how-to-connect-health-sync.md)。
* 支援 [Azure AD 網域服務](../user-help/active-directory-passwords-update-your-own-password.md) 密碼同步處理。

**新的支援案例：**

* 支援多個內部部署的 Exchange 組織。 如需詳細資訊，請參閱[內含多個 Active Directory 樹系的混合式部署](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150))。

**已修正的問題：**

* 密碼同步處理問題：
  * 從範圍外移到範圍內的物件不會同步處理其密碼。 這包含 OU 及屬性篩選。
  * 選取要包含在同步處理的新 OU 不需要完整密碼同步處理。
  * 啟用已停用的使用者時，不會同步處理密碼。
  * 密碼重試佇列為無限，而且已移除 5000 個物件要淘汰的先前限制。
* 無法連接到具 Windows Server 2016 樹系功能等級的 Active Directory。
* 初始安裝後，無法變更群組篩選所使用的群組。
* 對於在啟用密碼回寫的情況下執行密碼變更的每個使用者，將無法再於 Azure AD Connect 伺服器上建立新的使用者設定檔。
* 無法在同步處理規則範圍中使用長整數值。
* 如果有無法連線的網域控制站，[裝置回寫] 核取方塊會維持停用狀態。

## <a name="1086670"></a>1.0.8667.0
發行日期：2015 年 8 月

**新功能:**

* Azure AD Connect 安裝精靈現在已進行所有 Windows Server 語言的當地語系化。
* 新增在使用 Azure AD 密碼管理時的帳戶解除鎖定支援。

**已修正的問題：**

* 如果另一位使用者繼續安裝，而不是最先開始安裝的人，則 Azure AD Connect 安裝精靈會當機。
* 如果 Azure AD Connect 的先前解除安裝作業無法將 Azure AD Connect Sync 完全解除安裝，則不可能重新安裝。
* 如果使用者不在樹系的根網域中或已使用非英文版的 Active Directory，則無法使用 Express 安裝 Azure AD Connect。
* 如果無法解析 Active Directory 使用者帳戶的 FQDN，則會顯示「無法認可結構描述」的誤導錯誤訊息。
* 如果 Active Directory 連接器上使用的帳戶已在精靈外部變更，精靈將無法進行後續的執行。
* Azure AD Connect 有時無法在網域控制站上安裝。
* 如果已加入擴充屬性，則無法啟用和停用「預備模式」。
* 因為 Active Directory 連接器上的密碼不正確，所以有些設定的密碼回寫失敗。
* 如果辨別名稱 (DN) 用於屬性篩選，則無法升級 DirSync。
* 使用密碼重設時，CPU 使用率過高。

**已移除的預覽功能：**

* 根據預覽版客戶的意見反應，已暫時移除 [ [使用者回寫](how-to-connect-preview.md#user-writeback) ] 預覽功能。 當我們處理所提供的意見反應之後，未來將會重新新增它。

## <a name="1086410"></a>1.0.8641.0
發行日期：2015 年 6 月

**Azure AD Connect 的最初發行版本。**

名稱從 Azure AD Sync 變更為 Azure AD Connect。

**新功能:**

* [快速設定](how-to-connect-install-express.md)安裝
* 可以[設定 AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* 可以[從 DirSync 升級](how-to-dirsync-upgrade-get-started.md)
* [防止意外刪除](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* 引入 [預備模式](how-to-connect-sync-staging-server.md)

**新的預覽功能：**

* [使用者回寫](how-to-connect-preview.md#user-writeback)
* [群組回寫](how-to-connect-preview.md#group-writeback)
* [裝置回寫](how-to-connect-device-writeback.md)
* [目錄擴充](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
發行日期：2015 年 5 月

**新需求︰**

* Azure AD Sync 現在需要安裝 .Net framework 4.5.1 版。

**已修正的問題：**

* Azure AD 密碼回寫出現 Azure 服務匯流排連線錯誤。

## <a name="104910413"></a>1.0.491.0413
發行日期︰2015 年 4 月

**已修正的問題和改進︰**

* 如果已啟用資源回收筒且樹系中有多個網域，則 Active Directory 連接器不會正確處理刪除。
* Azure Active Directory 連接器的匯入作業效能已改善。
* 當群組已超過成員資格限制 (預設限制已設為 50000 個物件)，則會在 Azure Active Directory 中刪除此群組。 具有新的行為，不會刪除群組，不會擲回錯誤，且不會匯出新的成員資格變更。
* 如果連接器空間已存在具有相同 DN 的分段刪除，則無法佈建新的物件。
* 在差異同步處理期間，儘管物件上沒有預備的變更，某些物件仍會被標示為同步處理中。
* 強制執行密碼同步也會移除慣用的 DC 清單。
* CSExportAnalyzer 有一些物件狀態的問題。

**新功能:**

* 聯結現在可以連接到 MV 中的「任何」物件類型。

## <a name="104850222"></a>1.0.485.0222
發行日期︰2015 年 2 月

**改進:**

* 改進匯入效能。

**已修正的問題：**

* 密碼同步處理會接受屬性篩選所用的 cloudFiltered 屬性。 經過篩選的物件不再於密碼同步處理的範圍中。
* 在拓撲有很多網域控制站的少數情況下，無法進行密碼同步處理。
* 在 Azure AD/Intune 中啟用裝置管理之後，從 Azure AD 連接器匯入時的「停止的伺服器」。
* 從相同樹系中的多個網域加入外部安全性主體 (FSP) 會造成模稜兩可的加入錯誤。

## <a name="104751202"></a>1.0.475.1202
發行日期︰2014 年 12 月

**新功能:**

* 現在支援透過以屬性為基礎的篩選進行密碼同步處理。 如需詳細資訊，請參閱[透過篩選進行密碼同步處理](how-to-connect-sync-configure-filtering.md)。
* ms-DS-ExternalDirectoryObjectID 屬性會寫回至 Active Directory。 這項功能會新增適用於 Office 365 應用程式的支援。 它使用 OAuth2 在混合交換部署中訪問連線和本地郵箱。

**已修正的升級問題︰**

* 在伺服器上可使用較新版的登入小幫手。
* 自訂安裝路徑用來安裝 Azure AD Sync。
* 無效的自訂加入準則會封鎖升級。

**其他修正︰**

* 已修正適用於 Office Pro Plus 的範本。
* 已修正以破折號開頭的使用者名稱所造成的安裝問題。
* 已修正第二次執行安裝精靈時遺失 sourceAnchor 設定的問題。
* 已修正密碼同步處理的 ETW 追蹤。

## <a name="104701023"></a>1.0.470.1023
發行日期︰2014 年 10 月

**新功能:**

* 從多個內部部署 Active Directory 至 Azure AD 的密碼同步處理。
* 所有 Windows Server 語言的當地語系化安裝 UI。

**從 AADSync 1.0 GA 升級**

如果您已安裝 Azure AD Sync，您必須進行一個額外步驟，以免您已變更任何現成可用的同步處理規則。 在升級至 1.0.470.1023 版本之後，您已修改的同步規則會重複。 針對每個已修改的同步處理規則，執行下列作業︰

1. 找出您已修改的同步處理規則並記下變更。
1. 刪除同步處理規則。
1. 找出 Azure AD Sync 建立的新同步處理規則，然後重新套用變更。

**Active Directory 帳戶的權限**

Active Directory 帳戶必須獲得其他權限，才能讀取來自 Active Directory 的密碼雜湊。 要授與的權限名為「複寫目錄變更」和「複寫所有目錄變更」。 需要有這兩個權限才能讀取密碼雜湊。

## <a name="104190911"></a>1.0.419.0911
發行日期︰2014 年 9 月

**Azure AD Sync 的最初發行版本。**

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
