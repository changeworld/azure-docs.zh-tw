---
title: Azure AD Connect：版本發行歷程記錄 | Microsoft Docs
description: 本文列出 Azure AD Connect 和 Azure AD 同步的所有版本。
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fccbb84647ae9e47afc7bb36eeca97bb41a0d1d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604065"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect：版本發行歷程記錄
Azure Active Directory (Azure AD) 團隊會定期以新的特性和功能更新 Azure AD Connect。 並非所有新增項目都適用於所有的對象。

本文旨在協助您追蹤已發行的版本，以及了解最新版本中的變更。



此表格為相關主題的清單：

主題 |  詳細資料
--------- | --------- |
從 Azure AD Connect 升級的步驟 | [從舊版升級到最新版本](how-to-upgrade-previous-version.md) Azure AD Connect 的多種方法。
所需的權限 | 如需套用更新所需權限的詳細資訊，請參閱[帳戶和權限](reference-connect-accounts-permissions.md#upgrade)。
下載| [下載 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)。

>[!NOTE]
>發行新版 Azure AD Connect 是需要數個品質控制步驟來確保服務作業功能的程序，而當進行此程序時，新版本的版本號碼及發行狀態將會更新，以反映最新狀態。
當進行此程序時，發行版本號碼將會以 "X" 顯示在次要版本號碼位置，如 "1.3.X.0" 中所示 - 這表示此文件中版本資訊適用於開頭為 "1.3." 的所有版本。 完成發行程序之後，發行版本號碼將會更新為最近發行的版本，而發行狀態將會更新為「已發行，可供下載和自動升級」。
並非所有版本的 Azure AD Connect 都可自動升級。 發行狀態會指出版本是否可自動升級或僅供下載。 如果您的 Azure AD Connect 伺服器上已啟用自動升級，則該伺服器將會自動升級為可自動升級的最新版 Azure AD Connect。 請注意，並非所有 Azure AD Connect 組態都符合自動升級的資格。 請遵循下列連結來深入了解[自動升級](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> 自 2020 年 11 月 1 日起，將開始實作淘汰程序。根據此程序，超過 18 個月前發行的 Azure AD Connect 版本將會被淘汰。 屆時，我們將開始此程序，淘汰版本為 1.3.20.0 (於 2019 年 4 月 24 日發行) 及更舊版本的所有 Azure AD Connect 版本，然後我們會繼續在每次有新版本發行時，評估是否要淘汰舊版的 Azure AD Connect。
>
> 您必須確定執行的是最新版的 Azure AD Connect，以便獲得最佳的支援體驗。 
>
>如果執行的是已淘汰的 Azure AD Connect 版本，您可能沒有最新的安全性修正、效能提升、疑難排解和診斷工具，以及服務增強功能，且如果需要支援，我們可能無法提供組織所需的服務等級。
>
>如果啟用了 Azure AD Connect 進行同步，您將會立即自動開始收到 Health 通知，以在執行其中一個舊版時，警告該版本即將淘汰。
>
>請參閱[本文](./how-to-upgrade-previous-version.md)，以深入了解如何將 Azure AD Connect 升級至最新版本。
>
>如需已淘汰版本的版本歷程記錄資訊，請參閱 [Azure AD Connect 版本發行歷程記錄](reference-connect-version-history-archive.md)

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>發行狀態
07/29/2020：已發行以供下載

### <a name="functional-changes"></a>功能性變更
這是錯誤修正版本。 此版本中沒有任何功能變更。

### <a name="fixed-issues"></a>已修正的問題

- 修正系統管理員無法啟用「無縫單一登入」的問題，如果 AZUREADSSOACC 電腦帳戶已經存在於「Active Directory」中。
- 修正了在 V2 API delta 匯入期間，針對透過 health 入口網站修復的衝突物件，造成暫存錯誤的問題。
- 修正匯入/匯出設定中停用的自訂規則已匯入為已啟用的問題。

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>發行狀態
07/10/2020：已發行以供下載

### <a name="functional-changes"></a>功能性變更
此版本包含將現有 Azure AD Connect 伺服器的設定匯出至的功能的公開預覽。在安裝新的 Azure AD Connect 伺服器以建立源伺服器的複本時，可使用的 JSON 檔案。

這項新功能的詳細描述可在[本文](./how-to-connect-import-export-config.md)中找到

### <a name="fixed-issues"></a>已修正的問題
- 修正在升級期間，當地語系化組建上的本機資料庫大小有誤警告的錯誤（bug）。
- 修正了帳戶名稱/功能變數名稱交換的應用程式事件中會有錯誤的錯誤（bug）。
- 修正了 Azure AD Connect 會無法安裝在 DC 上的錯誤，並提供錯誤「找不到成員」。


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>發行狀態
05/07/2020：已發行可供下載

### <a name="fixed-issues"></a>已修正的問題
此 Hotfix 組建會修正如果只選取孫系容器，從精靈 UI 中不正確選取了未選取網域的問題。


>[!NOTE]
>此版本包含新的 Azure AD Connect 同步 V2 端點 API。  這個新的 V2 端點目前為公開預覽狀態。  需要此版本或更新版本才能使用新的 V2 端點 API。  不過，只安裝此版本並不會啟用 V2 端點。 除非啟用 V2 端點，否則您會繼續使用 V1 端點。  您必須遵循 [Azure AD Connect 同步 V2 端點 API (公開預覽)](how-to-connect-sync-endpoint-api-v2.md) (英文) 下的步驟執行，才能將其啟用並加入公開預覽。  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>發行狀態
2020/04/23：已發行可供下載

### <a name="fixed-issues"></a>已修正的問題
此 Hotfix 組建會修正組建 1.5.20.0 中引進的問題，其中使用 MFA 的租用戶系統管理員無法啟用 DSSO。

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>發行狀態
04/20/2020：已發行可供下載

### <a name="fixed-issues"></a>已修正的問題
此 Hotfix 組建會修正如果已複製 **In from AD - Group Join** 規則且尚未複製 **In from AD - Group Common** 規則，組建 1.5.20.0 中的問題。

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>發行狀態
2020/04/09：已發行可供下載

### <a name="fixed-issues"></a>已修正的問題
- 此 Hotfix 組建會修正如果已啟用 [群組篩選] 功能並使用 mS-DS-ConsistencyGuid 作為來源錨點，組建 1.5.18.0 的相關問題。
- 已修正 ADSyncConfig PowerShell 模組中的問題，其中用於所有 Set-ADSync* 權限 Cmdlet 的叫用 DSACLS 命令會導致下列其中一個錯誤：
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> 如果已複製 **In from AD - Group Join** 同步規則且尚未複製 **In from AD - Group Common** 同步規則，並想要進行升級，請在升級過程中完成下列步驟：
> 1. 在升級期間，取消核取 [在設定完成時開始同步處理程序] 選項。
> 2. 編輯複製的聯結同步規則，然後新增下列兩個轉換：
>     - 將直接流程 `objectGUID` 設定為 `sourceAnchorBinary`。
>     - 將運算式流程 `ConvertToBase64([objectGUID])` 設定為 `sourceAnchor`。     
> 3. 使用 `Set-ADSyncScheduler -SyncCycleEnabled $true` 啟用排程器。



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>發行狀態
2020/04/02：已發行可供下載

### <a name="functional-changes-adsyncautoupgrade"></a>ADSyncAutoUpgrade 功能性變更 

- 已新增群組物件的 mS-DS-ConsistencyGuid 功能支援。 這可供在樹系之間移動群組，或將 AD 中的群組重新連線到 AD 群組 objectID 已變更的 Azure AD，例如災害後重建 AD 伺服器時。 如需詳細資訊，請參閱[在樹系之間移動群組](how-to-connect-migrate-groups.md)。
- 所有同步的群組上都會自動設定 mS-DS-ConsistencyGuid 屬性，您不需要執行任何動作即可啟用此功能。 
- 已移除 Get-ADSyncRunProfile，因為已不再使用。 
- 已變更在嘗試使用企業管理員或網域管理員帳戶作為 AD DS 連接器帳戶時所看到的警告，以提供更多內容。 
- 已新增 Cmdlet 來從連接器空間移除物件，舊的 CSDelete.exe 工具會使用新的 Remove-ADSyncCSObject Cmdlet 來移除和取代。 Remove-ADSyncCSObject Cmdlet 接受 CsObject 作為輸入。 可使用 Get-ADSyncCSObject Cmdlet 擷取這個物件。

>[!NOTE]
>舊 CSDelete.exe 工具已使用新 Remove-ADSyncCSObject Cmdlet 來移除和取代 

### <a name="fixed-issues"></a>已修正的問題

- 已修正在停用群組回寫樹系/OU 選擇器之後，重新執行 Azure AD Connect 精靈時出現在此功能的 Bug。 
- 已引進包含新說明連結的新錯誤頁面，如果遺漏必要的 DCOM 登錄值，就會顯示此頁面。 資訊也會寫入記錄檔。 
- 已修正建立 Azure Active Directory 同步處理帳戶時的問題，其中啟用目錄延伸模組或 PHS 可能會失敗，因為帳戶尚未傳播到所有服務複本就嘗試使用。 
- 已修正未正確處理 Surrogate 字元的同步錯誤壓縮公用程式中 Bug。 
- 已修正自動升級時讓伺服器處於排程器暫停狀態的 Bug。 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>發行狀態
2019/12/9：已發行，可供下載。 無法透過自動升級取得。
### <a name="new-features-and-improvements"></a>新功能和改進
- 我們已更新 Azure AD Domain Services 的密碼雜湊同步，以在 Kerberos 雜湊中適當考慮填補。  這可在從 Azure AD 到 Azure AD Domain Services 的密碼同步處理期間，提供效能改進。
- 我們已新增對驗證代理程式與服務匯流排之間可靠工作階段的支援。
- 此版本會實施 TLS 1.2，以在驗證代理程式與雲端服務之間進行通訊。
- 我們已新增驗證代理程式與雲端服務之間 WebSocket 連線的 DNS 快取。
- 我們已新增從雲端指定特定代理程式目標的功能，以測試代理程式連線。

### <a name="fixed-issues"></a>已修正的問題
- 1\.4.18.0 版有一個 Bug，其中 DSSO 的 PowerShell Cmdlet 使用登入視窗認證，而不是執行 ps 時所提供的管理員認證。 因此，無法透過 Azure AD Connect 使用者介面在多個樹系中啟用 DSSO。 
- 已透過 Azure AD Connect 使用者介面在所有樹系中同時啟用 DSSO 的修正

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>發行狀態
2019/11/08：已發行，可供下載。 無法透過自動升級取得。

>[!IMPORTANT]
>由於此版本 Azure AD Connect 中的內部架構變更，如果您使用 MSOnline PowerShell 管理 AD FS 信任關係設定，則必須將 MSOnline PowerShell 模組更新為 1.1.183.57 版或更高版本

### <a name="fixed-issues"></a>已修正的問題

此版本會修正已使用混合式 Azure AD 加入的現有裝置問題。 此版本包含可更正此問題的新裝置同步規則。
請注意，這項規則變更可能會導致從 Azure AD 刪除淘汰的裝置。 這不會造成問題，因為 Azure AD 不會在條件式存取授權期間使用這些裝置物件。 對於某些客戶，透過此規則變更刪除的裝置數目可能會超過刪除閾值。 如果您看到在 Azure AD 中刪除的裝置物件數目超過匯出刪除閾值，則建議允許刪除作業進行。 [如何在超過刪除閾值時允許刪除作業進行](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>發行狀態
2019/9/28：已發行，可供自動升級來選取租用戶。 未提供下載。

此版本會修正一個 Bug，其中從舊版自動升級至 1.4.18.0 的某些伺服器會遇到自助式密碼重設 (SSPR) 和密碼回寫問題。

### <a name="fixed-issues"></a>已修正的問題

在某些情況下，自動升級至 1.4.18.0 版的伺服器在完成升級之後並未啟用自助式密碼重設和密碼回寫。 此自動升級版本會修正該問題，並重新啟用自助式密碼重設和密碼回寫。

我們已修正未正確處理 Surrogate 字元的同步錯誤壓縮公用程式中 Bug。

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>我們正在對一個事件展開調查，其中某些客戶在升級至此版本的 Azure AD Connect 之後，其現有的混合式 Azure AD 聯結裝置發生問題。 建議已部署混合式 Azure AD Join 的客戶延遲升級至此版本，直到我們完全了解這些問題的根本原因並降低風險為止。 我們將盡快提供詳細資訊。

>[!IMPORTANT]
>使用此版本的 Azure AD Connect 時，某些客戶可能會看到部分或所有的 Windows 裝置從 Azure AD 消失。 這不會造成問題，因為 Azure AD 不會在條件式存取授權期間使用這些裝置身分識別。 如需詳細資訊，請參閱[了解 Azure AD Connect 1.4.xx.x 裝置消失](reference-connect-device-disappearance.md)


### <a name="release-status"></a>發行狀態
2019/9/25：已發行，僅供自動升級。

### <a name="new-features-and-improvements"></a>新功能和改進
- 新的疑難排解工具可協助針對「使用者未同步」、「群組未同步」或「群組成員未同步」案例進行疑難排解。
- 在 Azure AD Connect 疑難排解腳本中新增國家雲端的支援。
- 客戶應該會收到通知，指出現已移除 MIIS_Service 的已淘汰 WMI 端點。 所有 WMI 作業現在都應透過 PS Cmdlet 來完成。
- 藉由重設 AZUREADSSOACC 物件的限制委派來改善安全性。
- 新增/編輯同步處理規則時，如果規則中使用了連接器架構中未新增至連接器的任何屬性，則會自動將這些屬性新增至連接器。 這也適用於規則所影響的物件類型。 如有任何項目新增至連接器，則連接器將會標示為在下一個同步週期完整匯入。
- 新的 Azure AD Connect 部署不再支援使用企業或網域管理員作為連接器帳戶。 使用企業或網域系統管理員作為連接器帳戶的目前 Azure AD Connect 部署，將不會受到此版本影響。
- 在同步處理管理員中，會在建立/編輯/刪除規則時執行完整同步。 任何規則變更都會出現一個快顯視窗來通知使用者是否要執行完整匯入或完整同步。
- 已將密碼錯誤的風險降低步驟新增至「連接器 > 屬性 > 連線能力」頁面。
- 已在連接器屬性頁面中新增同步服務管理員的淘汰警告。 此警告會通知使用者應透過 Azure AD Connect 精靈進行變更。
- 已在使用者的密碼原則問題中新增錯誤。
- 防止錯誤設定依網域和 OU 篩選進行群組篩選。 若已篩選出所輸入群組的網域/OU，則群組篩選會顯示錯誤，讓使用者無法繼續進行，直到問題解決為止。
- 使用者無法再於 Synchronization Service Manager UI 中建立 Active Directory Domain Services 或 Windows Azure Active Directory 的連接器。
- 已修正 Synchronization Service Manager 中自訂 UI 控制項的可存取性。
- 已針對 Azure AD Connect 中的所有登入方法啟用六個同盟管理工作  (先前只有「更新 AD FS TLS/SSL 憑證」工作可用於所有登入)。
- 已新增警告，指出當登入方法從同盟變更為 PHS 或 PTA 時，所有 Azure AD 網域和使用者都會轉換成受控驗證。
- 已從「重設 Azure AD 和 AD FS 信任」工作中移除權杖簽署憑證，並新增個別的子工作來更新這些憑證。
- 已新增稱為「管理憑證」的新同盟管理工作，其中包含為 AD FS 伺服器陣列更新 TLS 或權杖簽署憑證的子工作。
- 已新增稱為「指定主要伺服器」的新同盟管理子工作，其可讓系統管理員為 AD FS 伺服器陣列指定新的主要伺服器。
- 已新增稱為「管理伺服器」的新同盟管理工作，其中包含部署 AD FS 伺服器、部署 Web 應用程式 Proxy 伺服器和指定主要伺服器的子工作。
- 已新增稱為「檢視同盟設定」的新同盟管理工作來顯示目前 AD FS 設定  (由於這項新增，AD FS 設定已從 [檢閱方案] 頁面中移除)。

### <a name="fixed-issues"></a>已修正的問題
- 已解決當接管其對應連絡人物件的使用者物件具有自我參考時 (例如使用者是自己的管理員)，所發生的同步錯誤問題。
- 說明快顯視窗現在會在鍵盤焦點上顯示。
- 針對自動升級，如有任何衝突的應用程式執行 6 小時，請將其終止並繼續升級。
- 選取目錄延伸模組時，將客戶可選取的屬性數目限制為每個物件 100 個。 這會防止在匯出期間發生錯誤，因為 Azure 的每個物件最多可有 100 個延伸模組屬性。
- 修正了錯誤，讓 AD 連接腳本更健全。
- 修正了使用現有的具名管道 WCF 服務，讓 Azure AD Connect 安裝在電腦上的 bug 更健全。
- 針對不允許 ADSync 服務在一開始安裝時啟動的群組原則，已改善其診斷和疑難排解。
- 已修正未正確寫入 Windows 電腦顯示名稱的 Bug。
- 修正未正確寫入 Windows 電腦 OS 類型的 Bug。
- 已修正非 Windows 10 電腦意外同步的 Bug。 請注意，這項變更的影響是現在會刪除先前已同步非 Windows 10 電腦。 這不會影響任何功能，因為 Windows 電腦的同步只會用於混合式 Azure AD 網域加入，而這僅適用於 Windows 10 裝置。
- 已在 ADSync PowerShell 模組中新增數個新的 (內部) Cmdlet。


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>將 Azure AD Connect 從舊版升級至1.3.21.0 有一個已知的問題，也就是 Microsoft 365 入口網站不會反映更新版本，即使 Azure AD Connect 成功升級也是一樣。
>
> 若要解決此問題，您必須匯入 **AdSync** 模組，然後在 `Set-ADSyncDirSyncConfiguration` Azure AD Connect 伺服器上執行 PowerShell Cmdlet。  您可使用下列步驟：
>
>1. 以系統管理員模式開啟 PowerShell。
>2. 執行 `Import-Module "ADSync"`。
>3. 執行 `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`。
 
### <a name="release-status"></a>發行狀態 

2019/05/14：已發行可供下載

### <a name="fixed-issues"></a>已修正的問題 

- 已修正 Microsoft Azure Active Directory Connect 組建 1.3.20.0 中存在的權限提高弱點。  在某些情況下，此弱點可能會讓攻擊者在特殊權限帳戶內容中執行兩個 PowerShell Cmdlet，並執行特殊權限動作。  此安全性更新會藉由停用這些 Cmdlet 來解決此問題。 如需詳細資訊，請參閱[安全性更新](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)。


## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
