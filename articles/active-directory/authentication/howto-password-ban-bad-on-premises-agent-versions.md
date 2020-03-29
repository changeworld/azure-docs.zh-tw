---
title: 密碼保護代理髮布歷史記錄 - Azure 活動目錄
description: 文件版本發行和行為變更歷程記錄
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847844"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Azure AD 密碼保護代理程式版本記錄

## <a name="121250"></a>1.2.125.0

發佈日期： 2019/3/22

* 修復事件日誌消息中的輕微拼寫錯誤
* 將 EULA 協定更新為最終常規可用性版本

> [!NOTE]
> 生成 1.2.125.0 是通用版本。 再次感謝您向大家提供的產品回饋！

## <a name="121160"></a>1.2.116.0

發佈日期： 2019/3/13

* 獲取 AzureAD 密碼保護代理和獲取 AzureAD 密碼保護 DC 代理 Cmdlet 現在報告軟體版本和當前 Azure 租戶具有以下限制：
  * 軟體版本和 Azure 租戶資料僅適用于運行版本 1.2.116.0 或更高版本的 DC 代理和代理。
  * 在重新註冊（或續訂）代理或林之前，不得報告 Azure 租戶資料。
* 代理服務現在要求安裝 .NET 4.7。
  * .NET 4.7 應該已安裝在完全更新的 Windows 伺服器上。 如果不是這樣，請下載並運行[在 Windows 的 .NET 框架 4.7 離線安裝程式中找到的安裝程式](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)。
  * 在伺服器核心系統上，可能需要將 /q 標誌傳遞給 .NET 4.7 安裝程式才能成功。
* 代理服務現在支援自動升級。 自動升級使用與代理服務並行安裝的 Microsoft Azure AD 連接代理更新程式服務。 預設情況下，自動升級處於打開狀態。
* 可以使用設置-AzureAD 密碼保護代理配置 Cmdlet 啟用或禁用自動升級。 可以使用獲取 AzureAD 密碼保護代理配置 Cmdlet 查詢當前設置。
* DC 代理服務的服務二進位檔案已重命名為 AzureADPassword 保護 DCAgent.exe。
* 代理服務的服務二進位檔案已重命名為 AzureADPassword 保護代理.exe。 如果協力廠商防火牆正在使用中，則可能需要相應地修改防火牆規則。
  * 注：如果以前的代理安裝中使用了 HTTP 代理設定檔，則需要在此升級後將其重命名（從*代理服務.exe.config*到*AzureADPassword保護代理.exe.config）。*
* 所有時間限制的功能檢查都已從 DC 代理中刪除。
* 小錯誤修復和日誌記錄改進。

## <a name="12650"></a>1.2.65.0

發佈日期： 2019/2/1

變更：

* Server Core 上現在支援 DC 代理程式和 Proxy 服務。 最小作業系統要求與之前保持不變：用於 DC 代理的 Windows Server 2012 和代理的 Windows Server 2012 R2。
* Register-AzureADPasswordProtectionProxy 與 Register-AzureADPasswordProtectionForest Cmdlet 現在均支援以裝置代碼為基礎的 Azure 驗證模式。
* Get-AzureADPasswordProtectionDCAgent Cmdlet 將會忽略已損害和/或無效的服務連接點。 這會修正網域控制站有時在輸出中多次出現的錯誤 (bug)。
* Get-AzureADPasswordProtectionSummaryReport Cmdlet 將會忽略已損害和/或無效的服務連接點。 這會修正網域控制站有時在輸出中多次出現的錯誤 (bug)。
* 現在已從 %ProgramFiles%\WindowsPowerShell\Modules 註冊 Proxy PowerShell 模組。 機器的 PSModulePath 環境變數已不再修改。
* 已新增新的 Get-AzureADPasswordProtectionProxy Cmdlet，以協助在樹系或網域中探索已註冊的 Proxy。
* DC 代理程式會在 sysvol 共用中使用新的資料夾，來複寫密碼原則和其他檔案。

   舊的資料夾位置：

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   新的資料夾位置：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (進行此變更是為了避免誤判「孤立的 GPO」警告。)

   > [!NOTE]
   > 系統將不會在舊的資料夾和新的資料夾之間完成任何資料的移轉或共用。 舊版 DC 代理程式將繼續使用舊的位置，直到升級至此版本或更新版本。 一旦所有 DC 代理程式都執行版本 1.2.65.0 或更新版本之後，就能手動刪除舊的 sysvol 資料夾。

* DC 代理程式和 Proxy 服務現在將會偵測並刪除其個別服務連接點的已損害複本。
* 每個 DC 代理程式都將在其網域中，針對 DC 代理程式和 Proxy 服務連接點，定期刪除已損害和過時的服務連接點。 如果它的活動訊號時間戳記超過七天，則會將 DC 代理程式和 Proxy 服務連接點視為過時。
* DC 代理程式現在將視需要來更新樹系憑證。
* Proxy 服務現在將視需要來更新 Proxy 憑證。
* 密碼驗證演算法的更新：會先將全域禁用密碼清單和客戶專屬的禁用密碼清單 (若已設定) 組合在一起，然後再進行密碼驗證。 如果指定的密碼包含來自全域和客戶專屬清單的權杖，則該密碼現在可能會遭到拒絕 (失敗或僅限稽核)。 事件記錄檔文件已更新以反映此資訊；請參閱[監視 Azure AD 密碼保護](howto-password-ban-bad-on-premises-monitor.md)。
* 效能和穩健性修正
* 改進記錄功能

> [!WARNING]
> 限時功能：此版本 (1.2.65.0) 中的 DC 代理程式服務將自 2019 年 9 月 1 日起停止處理密碼驗證要求。  舊版 (請參閱下列清單) 中的 DC 代理程式服務將自 2019 年 7 月 1 日起停止處理。 所有版本中的 DC 代理程式服務都會在到達這些截止日期之前的兩個月內，將 10021 事件記錄至系統管理事件記錄檔。 所有限時的限制都將從即將發行的 GA 版本中移除。 Proxy 代理程式服務在所有版本中都不是限時的，但仍應升級至最新版本，以利用所有後續的錯誤 (bug) 修正和其他改良。

## <a name="12250"></a>1.2.25.0

發行日期：2018/11/01

修正：

* DC 代理程式和 Proxy 服務應該不會再因為憑證信任失敗而失敗。
* DC 代理程式和 Proxy 服務有適用於 FIPS 合規電腦的其他修正程式。
* Proxy 服務現在將可在只有 TLS 1.2 的網路環境中正常運作。
* 次要的效能和穩健性修正
* 改進記錄功能

變更：

* 現在 Proxy 服務的最低必要作業系統層級是 Windows Server 2012 R2。 DC 代理程式服務的最低必要作業系統層級維持為 Windows Server 2012。
* Proxy 服務現在需要 .NET 版本 4.6.2。
* 密碼驗證演算法採用擴充字元正規化資料表。 這可能會導致在舊版中接受的密碼遭到拒絕。

## <a name="12100"></a>1.2.10.0

發行日期：2018/8/17

修正：

* Register-AzureADPasswordProtectionProxy 與 Register-AzureADPasswordProtectionForest 現在支援多重要素驗證
* Register-AzureADPasswordProtectionProxy 需要網域中有 WS2012 或更新版本的網域控制站，避免發生加密錯誤。
* 啟動時向 Azure 要求新的密碼原則，使用 DC 代理程式服務較可靠。
* DC 代理程式服務會在必要時，每小時向 Azure 要求新的密碼原則，但現在能在隨機選取的開始時間進行。
* 在升階之前於伺服器上安裝為複本時，DC 代理程式服務不會再會導致新的 DC 公告無限期延遲。
* DC 代理程式服務現在允許 [啟用 Windows Server Active Directory 上的密碼保護] 組態設定
* 當升級至未來的版本時，DC 代理程式與 Proxy 安裝程式現在支援就地升級。

> [!WARNING]
> 不支援從 1.1.10.3 版就地升級，而且會造成安裝錯誤。 若要升級至 1.2.10 版或更新版本，您必須先將 DC 代理程式與 Proxy 服務軟體完全解決安裝，再從頭開始安裝新版本。 需要重新註冊 Azure AD 密碼保護 Proxy 服務。  不需要重新註冊樹系。

> [!NOTE]
> 就地升級 DC 代理程式軟體需要重新開機。

* DC 代理程式和 Proxy 服務現在支援在設定為僅使用 FIPS 相容演算法的伺服器上執行。
* 次要的效能和穩健性修正
* 改進記錄功能

## <a name="11103"></a>1.1.10.3

發行日期：2018/6/15

初始公開預覽版本

## <a name="next-steps"></a>後續步驟

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
