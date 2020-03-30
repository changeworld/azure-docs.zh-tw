---
title: 解決本地 Azure AD 密碼保護故障
description: 瞭解如何為本地活動目錄域服務環境解決 Azure AD 密碼保護問題
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
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263643"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>故障排除：本地 Azure AD 密碼保護

部署 Azure AD 密碼保護之後，可能需要進行疑難排解。 本文將深入說明以協助您了解一些常見的疑難排解步驟。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 代理無法在目錄中找到代理

此問題的主要症狀是 DC 代理管理事件日誌中的 30017 事件。

此問題的通常原因是代理尚未註冊。 如果代理已註冊，則由於 AD 複寫延遲，可能會出現一些延遲，直到特定 DC 代理能夠看到該代理。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 代理無法與代理通信

此問題的主要症狀是 DC 代理管理事件日誌中的 30018 事件。 此問題可能有幾個可能的原因：

1. DC 代理位於網路的隔離部分，不允許與註冊代理進行網路連接。 只要其他 DC 代理可以與代理通信以便從 Azure 下載密碼原則，此問題可能是良性的。 下載後，這些策略將由隔離的 DC 通過複製 sysvol 共用中的策略檔獲得。

1. 代理主機正在阻止對 RPC 終結點映射器終結點（埠 135）的訪問

   Azure AD 密碼保護代理安裝程式會自動創建允許訪問埠 135 的 Windows 防火牆入站規則。 如果以後刪除或禁用此規則，則 DC 代理將無法與代理服務通信。 如果內置 Windows 防火牆已禁用以替代其他防火牆產品，則必須配置該防火牆以允許訪問埠 135。

1. 代理主機阻止對代理服務偵聽的 RPC 終結點（動態或靜態）的訪問

   Azure AD 密碼保護代理安裝程式會自動創建 Windows 防火牆入站規則，該規則允許訪問 Azure AD 密碼保護代理服務偵聽的任何入站埠。 如果以後刪除或禁用此規則，則 DC 代理將無法與代理服務通信。 如果內置 Windows 防火牆已禁用以替代其他防火牆產品，則必須配置該防火牆以允許訪問 Azure AD 密碼保護代理服務偵聽的任何入站埠。 如果代理服務已配置為偵聽特定的靜態 RPC 埠（使用`Set-AzureADPasswordProtectionProxyConfiguration`Cmdlet），則此配置可能會更加具體。

1. 代理主機未配置為允許網域控制站登錄到電腦。 此行為通過"從網路訪問此電腦"使用者許可權分配進行控制。 必須授予林中的所有網域控制站此許可權。 此設置通常受限於更大的網路強化工作。

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>代理服務無法與 Azure 通信

1. 確保代理電腦與[部署要求](howto-password-ban-bad-on-premises-deploy.md)中列出的終結點具有連接。

1. 確保林和所有代理伺服器都針對同一 Azure 租戶進行註冊。

   您可以通過運行`Get-AzureADPasswordProtectionProxy`和`Get-AzureADPasswordProtectionDCAgent`PowerShell Cmdlet 來檢查此要求，然後`AzureTenant`比較每個返回項的屬性。 為了正確操作，報告的所有 DC 代理和代理伺服器中的租戶名稱必須相同。

   如果 Azure 租戶註冊不匹配條件確實存在，則可以根據需要運行`Register-AzureADPasswordProtectionProxy`和/或`Register-AzureADPasswordProtectionForest`PowerShell Cmdlet 來解決此問題，確保對所有註冊使用來自同一 Azure 租戶的憑據。

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 代理無法加密或解密密碼原則檔

Azure AD 密碼保護依賴于 Microsoft 金鑰分發服務提供的加密和解密功能。 加密或解密失敗可能表現為各種症狀，並有多種潛在原因。

1. 確保 KDS 服務在域中的所有 Windows Server 2012 和更高版本的網域控制站上啟用並正常工作。

   預設情況下，KDS 服務的服務啟動模式配置為手動（觸發啟動）。 此配置意味著用戶端首次嘗試使用服務時，會按需啟動該服務。 Azure AD 密碼保護可使用此預設服務啟動模式。

   如果 KDS 服務啟動模式已配置為禁用，則必須修復此配置，然後才能正常工作。

   此問題的簡單測試是通過服務管理 MMC 主控台或使用其他管理工具（例如，從命令提示主控台運行"網路啟動 kdsvc"）手動啟動 KDS 服務。 KDS 服務預期已成功啟動並保持運行。

   KDS 服務無法啟動的最常見根本原因是 Active Directory 網域控制站物件位於預設網域控制站 OU 之外。 KDS 服務不支援此配置，並且不是 Azure AD 密碼保護施加的限制。 此條件的解決方法是將網域控制站物件移動到預設網域控制站 OU 下的位置。

1. 不相容的 KDS 加密緩衝區格式從 Windows 伺服器 2012 R2 更改為 Windows 伺服器 2016

   Windows Server 2016 引入了 KDS 安全修補程式，該修補程式修改了 KDS 加密緩衝區的格式;這些緩衝區有時無法在 Windows 伺服器 2012 和 Windows 伺服器 2012 R2 上解密。 相反的方向是好的 - 在 Windows 伺服器 2012 和 Windows Server 2012 R2 上加密的 KDS 緩衝區將始終在 Windows Server 2016 和更高版本上成功解密。 如果 Active Directory 域中的網域控制站運行這些作業系統的組合，則可能會報告偶爾出現 Azure AD 密碼保護解密失敗。 鑒於安全修補程式的性質，並且由於 Azure AD 密碼保護 DC 代理在給定時間對哪個網域控制站進行加密資料，因此無法準確預測這些故障的時間或症狀。

   微軟正在調查此問題的修復程式，但尚未提供 ETA。 同時，除了在 Active Directory 域中不運行這些不相容作業系統的組合之外，沒有解決此問題的解決方法。 換句話說，您應該只運行 Windows Server 2012 和 Windows Server 2012 R2 網域控制站，或者只應運行 Windows Server 2016 和網域控制站以上。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>正在接受弱密碼，但不應

此問題可能有多種原因。

1. 您的 DC 代理正在運行已過期的公共預覽軟體版本。 請參閱[公共預覽 DC 代理軟體已過期](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)。

1. 您的 DC 代理無法下載策略或無法解密現有策略。 檢查上述主題中可能存在的原因。

1. 密碼原則強制模式仍會設為 [稽核]。 如果此配置有效，請將其重新配置為使用 Azure AD 密碼保護門戶強制執行。 有關詳細資訊，請參閱[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

1. 密碼原則已停用。 如果此配置有效，請使用 Azure AD 密碼保護門戶將其重新配置為啟用。 有關詳細資訊，請參閱[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

1. 您尚未在域中的所有網域控制站上安裝 DC 代理軟體。 在此情況下，很難確保遠端 Windows 用戶端在密碼更改操作期間針對特定網域控制站。 如果您認為自己已成功定位安裝了 DC 代理軟體的特定 DC，則可以通過仔細檢查 DC 代理管理事件日誌進行驗證：無論結果如何，將至少有一個事件來記錄密碼的結果驗證。 如果密碼更改的使用者不存在事件，則密碼更改可能由其他網域控制站處理。

   作為替代測試，請嘗試在直接登錄到安裝 DC 代理軟體的 DC 時設置更改密碼。 不建議用於生產活動目錄域。

   雖然受這些限制支援 DC 代理軟體的增量部署，但 Microsoft 強烈建議儘快將 DC 代理軟體安裝在域中的所有網域控制站上。

1. 密碼驗證演算法實際上可能按預期工作。 請參閱[如何計算密碼](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe 無法設置弱 DSRM 密碼

Active Directory 將始終驗證新的目錄服務修復模式密碼，以確保它滿足域的密碼複雜性要求;此驗證還調用密碼篩選器 dll，如 Azure AD 密碼保護。 如果新的 DSRM 密碼被拒絕，則會導致以下錯誤訊息：

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

當 Azure AD 密碼保護記錄活動目錄 DSRM 密碼的密碼驗證事件日誌事件時，預計事件日誌消息將不包含使用者名。 發生此行為是因為 DSRM 帳戶是不屬於實際活動目錄域的本地帳戶。  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>網域控制站副本升級失敗，因為 DSRM 密碼較弱

在 DC 升級過程中，新的目錄服務修復模式密碼將提交到域中的現有 DC 進行驗證。 如果新的 DSRM 密碼被拒絕，則會導致以下錯誤訊息：

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

與上述問題一樣，任何 Azure AD 密碼保護密碼驗證結果事件都將為此方案具有空使用者名。

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>由於本地管理員密碼薄弱，網域控制站降級失敗

目前支援將仍在執行 DC 代理程式軟體的網域控制站降階。 不過，系統管理員應了解 DC 代理程式軟體會在降級程序期間繼續強制執行目前的密碼原則。 新的本機系統管理員帳戶密碼 (指定為降階作業的一部分) 會像任何其他密碼一樣受到驗證。 Microsoft 建議為本地管理員帳戶選擇安全密碼，作為 DC 降級過程的一部分。

在降階成功後，網域控制站即已重新啟動，並重新以一般成員伺服器的形式執行，且 DC 代理程式軟體會回復為以被動模式執行。 其後您可以隨時將其解除安裝。

## <a name="booting-into-directory-services-repair-mode"></a>引導到目錄服務修復模式

如果網域控制站已引導到目錄服務修復模式，則 DC 代理密碼篩選器 dll 將檢測到此情況，並將導致禁用所有密碼驗證或強制活動，而不管當前處於活動狀態的策略如何配置。 DC 代理密碼篩選器 dll 將 10023 警告事件記錄到管理事件日誌，例如：

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>公共預覽 DC 代理軟體已過期

在 Azure AD 密碼保護公共預覽期間，DC 代理軟體經過硬編碼，以停止處理以下日期的密碼驗證請求：

* 版本 1.2.65.0 將于 2019 年 9 月 1 日停止處理密碼驗證請求。
* 版本 1.2.25.0 和之前停止處理密碼驗證請求在 2019 年 7 月 1 日。

隨著截止時間的臨近，所有時間限制的 DC 代理版本都將在啟動時在 DC 代理管理事件日誌中發出 10021 事件，如下所示：

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

截止時間過後，所有時間限制的 DC 代理版本將在啟動時在 DC 代理管理事件日誌中發出 10022 事件，如下所示：

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

由於僅在初始啟動時檢查截止時間，因此在日曆截止時間過後很長時間內，您才能看到這些事件。 一旦識別了截止時間，除了自動批准所有密碼之外，不會對網域控制站或更大的環境產生負面影響。

> [!IMPORTANT]
> Microsoft 建議立即將過期的公共預覽 DC 代理升級到最新版本。

在環境中發現需要升級的 DC 代理的一種簡單方法是運行`Get-AzureADPasswordProtectionDCAgent`Cmdlet，例如：

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

對於本主題，"軟體版本"欄位顯然是要查看的關鍵屬性。 您還可以使用 PowerShell 篩選來篩選出已經達到或高於所需基準版本的 DC 代理，例如：

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD 密碼保護代理軟體在任何版本中都是不受限制的。 Microsoft 仍然建議將 DC 代理和代理代理在發佈時升級到最新版本。 `Get-AzureADPasswordProtectionProxy` Cmdlet 可用於查找需要升級的代理代理，類似于上述 DC 代理示例。

有關特定升級過程的更多詳細資訊[，請參閱升級 DC 代理](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent)和[升級代理服務](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)。

## <a name="emergency-remediation"></a>緊急補救

如果發生 DC 代理程式服務造成問題的情況，可以立即將 DC 代理程式服務關閉。 DC 代理程式密碼篩選 dll 仍會嘗試呼叫非執行中服務，且會記錄警告事件 (10012、10013)，但在這段期間傳入的所有密碼都會被接受。 隨後如有需要，也可以透過 Windows 服務控制管理員，在啟動類型設為 [已停用] 的情況下設定 DC 代理程式服務。

另一個補救措施是在 Azure AD 密碼保護入口網站中，將啟用模式設為 [否]。 下載更新的原則之後，每個 DC 代理程式服務都將進入靜止模式，在此模式中會依原樣接受所有密碼。 有關詳細資訊，請參閱[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

## <a name="removal"></a>移除

如果決定卸載 Azure AD 密碼保護軟體並從域和林中清除所有相關狀態，則可以使用以下步驟完成此任務：

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

4. 手動移除在每個網域命名內容中所有的 DC 代理程式連接點。 林中每個網域控制站可能有一個這些物件，具體取決於軟體的部署範圍。 您可以使用下列 Active Directory PowerShell 命令來找出該物件的位置：

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
