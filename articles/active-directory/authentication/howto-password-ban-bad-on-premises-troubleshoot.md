---
title: 針對內部部署 Azure AD 密碼保護進行疑難排解
description: 瞭解如何針對內部部署 Active Directory Domain Services 環境 Azure AD 密碼保護進行疑難排解
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25199aeb7a3ed6332e74ad05835a8c4fca763c00
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88116456"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>疑難排解：內部部署 Azure AD 密碼保護

部署 Azure AD 密碼保護之後，可能需要進行疑難排解。 本文將深入說明以協助您了解一些常見的疑難排解步驟。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 代理程式找不到目錄中的 proxy

此問題的主要徵兆是 DC 代理程式管理事件記錄檔中的30017事件。

此問題的常見原因是尚未註冊 proxy。 如果 proxy 已註冊，則可能會因為 AD 複寫延遲而有一些延遲，直到特定的 DC 代理程式能夠看到該 proxy 為止。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 代理程式無法與 proxy 通訊

此問題的主要徵兆是 DC 代理程式管理事件記錄檔中的30018事件。 這個問題可能有數個可能的原因：

1. DC 代理程式位於網路的隔離部分，不允許網路連線到已註冊的 proxy (s) 。 只要其他 DC 代理程式可以與 proxy (s) 進行通訊，才能從 Azure 下載密碼原則，此問題可能是良性的。 一旦下載之後，隔離的 DC 就會透過 sysvol 共用中的原則檔案複寫來取得這些原則。

1. Proxy 主機電腦封鎖對 RPC 端點對應程式端點的存取 (埠 135) 

   Azure AD 密碼保護 Proxy 安裝程式會自動建立 Windows 防火牆輸入規則，以允許存取埠135。 如果稍後刪除或停用此規則，DC 代理程式將無法與 Proxy 服務進行通訊。 如果已停用內建 Windows 防火牆代替另一個防火牆產品，您必須將該防火牆設定為允許存取埠135。

1. Proxy 主機電腦封鎖了對 RPC 端點的存取， (由 Proxy 服務接聽的動態或靜態) 

   Azure AD 密碼保護 Proxy 安裝程式會自動建立 Windows 防火牆輸入規則，以允許存取由 Azure AD 的密碼保護 Proxy 服務所接聽的任何輸入埠。 如果稍後刪除或停用此規則，DC 代理程式將無法與 Proxy 服務進行通訊。 如果已停用內建 Windows 防火牆代替另一個防火牆產品，您必須將該防火牆設定為允許存取由 Azure AD 密碼保護 Proxy 服務所接聽的任何輸入埠。 如果 Proxy 服務已設定為使用 Cmdlet)  (接聽特定的靜態 RPC 埠，則此設定可能會更具體 `Set-AzureADPasswordProtectionProxyConfiguration` 。

1. Proxy 主機電腦未設定為允許網域控制站登入電腦。 此行為是透過 [從網路存取這台電腦] 使用者權限指派來控制。 樹系中所有網域的所有網域控制站都必須被授與此許可權。 這項設定通常會被限制為較大網路強化工作的一部分。

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy 服務無法與 Azure 通訊

1. 請確定 proxy 電腦可以連線到[部署需求](howto-password-ban-bad-on-premises-deploy.md)中列出的端點。

1. 請確定樹系和所有 proxy 伺服器都已針對相同的 Azure 租使用者註冊。

   您可以藉由執行和 PowerShell Cmdlet 來檢查此需求 `Get-AzureADPasswordProtectionProxy` `Get-AzureADPasswordProtectionDCAgent` ，然後比較 `AzureTenant` 每個傳回專案的屬性。 若要正確操作，報告的租使用者名稱在所有 DC 代理程式和 proxy 伺服器上都必須相同。

   如果 Azure 租使用者註冊不符條件存在，您可以 `Register-AzureADPasswordProtectionProxy` 視需要執行和/或 PowerShell Cmdlet 來修正此問題，並 `Register-AzureADPasswordProtectionForest` 務必使用來自相同 Azure 租使用者的認證來進行所有註冊。

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 代理程式無法加密或解密密碼原則檔案

Azure AD 密碼保護對於 Microsoft 金鑰發佈服務所提供的加密和解密功能有重要的相依性。 加密或解密失敗可能會有各種徵兆，而且有數個可能的原因。

1. 確定 KDS 根金鑰服務已在網域中的所有 Windows Server 2012 和更新版本的網域控制站上啟用和運作。

   根據預設，KDS 根金鑰服務的服務啟動模式會設定為手動 (觸發程式開始) 。 此設定表示當用戶端第一次嘗試使用此服務時，它會隨選啟動。 此預設服務啟動模式可讓 Azure AD 密碼保護正常執行。

   如果 KDS 根金鑰服務啟動模式已設定為停用，則必須先修正此設定，Azure AD 密碼保護才會正常運作。

   這個問題的簡單測試是透過服務管理 MMC 主控台手動啟動 KDS 根金鑰服務，或使用其他管理工具 (例如，從命令提示字元主控台) 執行 "net start kdssvc"。 KDS 根金鑰服務預期會順利啟動並繼續執行。

   KDS 根金鑰服務無法啟動的最常見根本原因是 Active Directory 網域控制站物件位於預設的網域控制站 OU 之外。 KDS 根金鑰服務不支援此設定，而且不是 Azure AD 密碼保護所加諸的限制。 這種情況的修正方法是將網域控制站物件移至預設網域控制站 OU 底下的位置。

1. 從 Windows Server 2012 R2 到 Windows Server 2016 的 KDS 根金鑰加密緩衝區格式變更不相容

   Windows Server 2016 引進了 KDS 根金鑰的安全性修正，修改了 KDS 根金鑰加密緩衝區的格式;這些緩衝區有時會無法在 Windows Server 2012 和 Windows Server 2012 R2 上解密。 相反的方向是 Windows Server 2012 和 Windows Server 2012 R2 上以 KDS 根金鑰加密的緩衝區，一律會在 Windows Server 2016 和更新版本上成功解密。 如果 Active Directory 網域中的網域控制站執行了這些作業系統的混合，則可能會報告密碼保護解密失敗的偶爾 Azure AD。 基於安全性修正的本質，無法精確預測這些失敗的時機或徵兆，並假設它不具決定性，Azure AD 的密碼保護 DC 代理程式會在特定時間加密資料。

   除了在您的 Active Directory 網域 (s) 中不混用這些不相容的作業系統時，此問題的因應措施並無任何因應措施。 換句話說，您應該只執行 Windows Server 2012 和 Windows Server 2012 R2 網域控制站，或只執行 Windows Server 2016 和更新版本的網域控制站。

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>DC 代理程式認為樹系尚未註冊

此問題的徵兆是記錄在 DC Agent\Admin 通道中的30016事件，其中包含：

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

此問題有兩個可能的原因。

1. 樹系尚未註冊。 若要解決此問題，請執行 Register-azureadpasswordprotectionforest 命令，如[部署需求](howto-password-ban-bad-on-premises-deploy.md)中所述。
1. 樹系已註冊，但 DC 代理程式無法解密樹系註冊資料。 在 [DC 代理程式] 底下所列的問題 #2，這種情況下的根本原因，[是無法加密或解密密碼原則](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files)檔案。 確認這個理論的簡單方法是，您只會在執行 Windows Server 2012 或 Windows Server 2012R2 網域控制站的 DC 代理程式上看到此錯誤，而在 Windows Server 2016 和更新版本的網域控制站上執行的 DC 代理程式則沒問題。 解決方法如下：將所有網域控制站升級至 Windows Server 2016 或更新版本。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>已接受弱式密碼，但不應

這個問題可能有幾個原因。

1. 您的 DC 代理程式 (s) 正在執行已過期的公開預覽軟體版本。 請參閱[公開預覽 DC 代理程式軟體已過期](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)。

1. 您的 DC 代理程式 (s) 無法下載原則或無法解密現有的原則。 請檢查上述主題中的可能原因。

1. 密碼原則強制模式仍會設為 [稽核]。 如果此設定作用中，請將它重新設定為使用 Azure AD 密碼保護入口網站來強制執行。 如需詳細資訊，請參閱作業[模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

1. 密碼原則已停用。 如果此設定有效，請使用 Azure AD 密碼保護入口網站，將它重新設定為 [啟用]。 如需詳細資訊，請參閱作業[模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

1. 您尚未在網域中的所有網域控制站上安裝 DC 代理程式軟體。 在此情況下，很容易確保遠端 Windows 用戶端在密碼變更作業期間以特定網域控制站為目標。 如果您認為已成功將 DC 代理程式軟體安裝在特定 DC 上，您可以藉由仔細檢查 DC 代理程式管理事件記錄檔來確認：不論結果為何，至少會有一個事件記錄密碼驗證的結果。 如果變更密碼的使用者沒有任何事件，則密碼變更可能是由不同的網域控制站處理。

   作為替代測試，請嘗試 setting\changing 密碼，同時登入 DC 代理程式軟體安裝所在的 DC。 不建議將這項技術用於生產 Active Directory 網域。

   雖然支援增量部署 DC 代理程式軟體受限於這些限制，但 Microsoft 強烈建議您儘快將 DC 代理程式軟體安裝在網域中的所有網域控制站上。

1. 密碼驗證演算法實際上可能會如預期般運作。 請參閱[如何評估密碼](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe 無法設定弱式 DSRM 密碼

Active Directory 一律會驗證新的目錄服務修復模式密碼，以確定它符合網域的密碼複雜性需求;這種驗證也會呼叫密碼篩選 dll （例如 Azure AD 密碼保護）。 如果新的 DSRM 密碼遭到拒絕，則會產生下列錯誤訊息：

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

當 Azure AD 密碼保護記錄 Active Directory DSRM 密碼的密碼驗證事件記錄檔事件 (s) 時，事件記錄檔訊息預期不會包含使用者名稱。 之所以會發生這種行為，是因為 DSRM 帳戶是不屬於實際 Active Directory 網域的本機帳戶。  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>網域控制站複本升級因弱式 DSRM 密碼而失敗

在 DC 升級程式期間，會將新的目錄服務修復模式密碼提交到網域中的現有 DC 進行驗證。 如果新的 DSRM 密碼遭到拒絕，則會產生下列錯誤訊息：

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

就像上述問題一樣，任何 Azure AD 密碼保護密碼驗證結果事件在此案例中將會有空白的使用者名稱。

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>由於本機系統管理員密碼弱，導致網域控制站降級失敗

目前支援將仍在執行 DC 代理程式軟體的網域控制站降階。 不過，系統管理員應了解 DC 代理程式軟體會在降級程序期間繼續強制執行目前的密碼原則。 新的本機系統管理員帳戶密碼 (指定為降階作業的一部分) 會像任何其他密碼一樣受到驗證。 Microsoft 建議您為本機系統管理員帳戶選擇安全的密碼，做為 DC 降級程式的一部分。

在降階成功後，網域控制站即已重新啟動，並重新以一般成員伺服器的形式執行，且 DC 代理程式軟體會回復為以被動模式執行。 其後您可以隨時將其解除安裝。

## <a name="booting-into-directory-services-repair-mode"></a>開機進入目錄服務修復模式

如果網域控制站開機進入目錄服務修復模式，則 DC 代理程式密碼篩選 dll 會偵測這種狀況，而不論目前作用中的原則設定為何，都會停用所有密碼驗證或強制活動。 DC 代理程式密碼篩選 dll 會將10023警告事件記錄到管理事件記錄檔，例如：

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>公開預覽 DC 代理程式軟體已過期

在 Azure AD 密碼保護公用預覽期間，DC 代理程式軟體會以硬式編碼的形式，在下列日期停止處理密碼驗證要求：

* 版本1.2.65.0 將會在 1 2019 年9月停止處理密碼驗證要求。
* 版本1.2.25.0 和先前在 1 2019 年7月停止處理密碼驗證要求。

當期限接近時，所有有時間限制的 DC 代理程式版本將會在開機時于 DC 代理程式管理事件記錄檔中發出10021事件，如下所示：

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

期限過後，所有有時間限制的 DC 代理程式版本都會在開機時于 DC 代理程式管理員事件記錄檔中發出10022事件，如下所示：

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

因為只有在初始開機時才會檢查期限，所以在行事曆期限過了一段時間後，您可能不會看到這些事件。 一旦辨識期限之後，網域控制站或較大的環境將不會有負面影響，因為所有密碼都會自動核准。

> [!IMPORTANT]
> Microsoft 建議過期的公用預覽 DC 代理程式立即升級至最新版本。

在您的環境中探索需要升級的 DC 代理程式的簡單方法，就是 `Get-AzureADPasswordProtectionDCAgent` 執行 Cmdlet，例如：

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

在本主題中，SoftwareVersion 欄位顯然是要查看的索引鍵屬性。 您也可以使用 PowerShell 篩選來篩選掉已等於或高於所需基準版本的 DC 代理程式，例如：

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD 的密碼保護 Proxy 軟體在任何版本中都沒有時間限制。 Microsoft 仍然建議在發行時，將 DC 和 proxy 代理程式升級至最新版本。 `Get-AzureADPasswordProtectionProxy`Cmdlet 可用來尋找需要升級的 Proxy 代理程式，類似于上述 DC 代理程式的範例。

如需特定升級程式的詳細資訊，請參閱[升級 DC 代理程式](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent)和[升級 Proxy 服務](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)。

## <a name="emergency-remediation"></a>緊急補救

如果發生 DC 代理程式服務造成問題的情況，可以立即將 DC 代理程式服務關閉。 DC 代理程式密碼篩選 dll 仍會嘗試呼叫非執行中服務，且會記錄警告事件 (10012、10013)，但在這段期間傳入的所有密碼都會被接受。 隨後如有需要，也可以透過 Windows 服務控制管理員，在啟動類型設為 [已停用] 的情況下設定 DC 代理程式服務。

另一個補救措施是在 Azure AD 密碼保護入口網站中，將啟用模式設為 [否]。 下載更新的原則之後，每個 DC 代理程式服務都將進入靜止模式，在此模式中會依原樣接受所有密碼。 如需詳細資訊，請參閱作業[模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

## <a name="removal"></a>移除

如果決定卸載 Azure AD 密碼保護軟體，並從網域 (s) 與樹系中清除所有相關的狀態，則可以使用下列步驟來完成此工作：

> [!IMPORTANT]
> 這些步驟務必要依序執行。 如果有任何 Proxy 服務的執行個體仍繼續執行，它將會定期重新建立其 serviceConnectionPoint 物件。 如果有任何 DC 代理程式服務的執行個體仍繼續執行，它將會定期重新建立其 serviceConnectionPoint 物件與 sysvol 狀態。

1. 從所有機器解除安裝 Proxy 軟體。 此步驟**不需要**重新開機。
2. 從所有網域控制站解除安裝 DC 代理程式軟體。 此步驟**需要**重新開機。
3. 手動移除每個網域命名內容中的所有 Proxy 服務連接點。 您可以使用下列 Active Directory PowerShell 命令來找出這些物件的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   請勿省略 $keywords 變數值結尾處的星號 ("*")。

   透過 `Get-ADObject` 命令找到的產生物件隨後可使用管線傳送到 `Remove-ADObject`，或以手動方式刪除。

4. 手動移除在每個網域命名內容中所有的 DC 代理程式連接點。 樹系中的每個網域控制站可能會有一個這些物件，這取決於軟體的部署範圍。 您可以使用下列 Active Directory PowerShell 命令來找出該物件的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   透過 `Get-ADObject` 命令找到的產生物件隨後可使用管線傳送到 `Remove-ADObject`，或以手動方式刪除。

   請勿省略 $keywords 變數值結尾處的星號 ("*")。

5. 手動移除樹系層級的組態狀態。 樹系組態狀態會保存在 Active Directory 組態命名內容中的容器內。 您可以依照下列方式加以探索和刪除：

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 手動刪除下列資料夾及其所有內容，以手動移除所有 sysvol 相關狀態：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   如有必要，也可在指定的網域控制站上從本機存取此路徑；預設位置會類似於下列路徑：

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   如果已將 sysvol 共用設定於非預設位置中，此路徑將會不同。

## <a name="next-steps"></a>後續步驟

[Azure AD 密碼保護的常見問題集](howto-password-ban-bad-on-premises-faq.md)

如需關於全域和自訂禁用密碼清單的詳細資訊，請參閱[禁用不當密碼](concept-password-ban-bad.md)一文
