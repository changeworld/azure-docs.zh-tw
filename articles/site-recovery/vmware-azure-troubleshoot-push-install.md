---
title: 使用 Azure 網站恢復 f 對移動服務推送安裝進行故障排除
description: 使用 Azure 網站恢復啟用災害復原複製時，排除移動服務安裝錯誤。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953784"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>故障排除移動服務推送安裝 

安裝行動服務是啟用複寫期間的關鍵步驟。 此步驟的成功與否僅取決於是否符合必要條件，以及使用支援的設定。 您在行動服務安裝期間最常面臨的失敗是因為以下原因：

* [認證/權限錯誤](#credentials-check-errorid-95107--95108)
* [登入失敗](#login-failures-errorid-95519-95520-95521-95522)
* [連線錯誤](#connectivity-failure-errorid-95117--97118)
* [檔案及印表機共用錯誤](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI 失敗](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [不支援的作業系統](#unsupported-operating-systems)
* [不支援的開機組態](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS 安裝失敗](#vss-installation-failures)
* [裝置名稱位於 GRUB 設定而非裝置 UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM 磁碟區](#lvm-support-from-920-version)
* [重新啟動警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

當您啟用複寫時，Azure Site Recovery 會嘗試推送您虛擬機器上的安裝行動服務代理程式。 在這個過程中，組態伺服器會嘗試與虛擬機器連線，並複製代理程式。 若要啟用成功的安裝，請遵循下面所列的逐步疑難排解指引。

## <a name="credentials-check-errorid-95107--95108"></a>認證檢查 (錯誤碼：95107 & 95108)

* 確認啟用複寫期間所選擇的使用者帳戶是否**有效且精確**。
* Azure Site Recovery 需要**根**帳戶或具備**系統管理員權限**的使用者帳戶，才能執行推送安裝。 否則，來源機器上將會封鎖推送安裝。
  * 針對 Windows (**錯誤 95107**)，請確認使用者帳戶是否具有來源機器上的系統管理存取權 (本機或網域)。
  * 如果您未使用網域帳戶，必須停用本機電腦上的遠端使用者存取控制。
    * 若要停用遠端使用者存取控制，請在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 登錄機碼之下，新增 DWORD：LocalAccountTokenFilterPolicy。 將值設定為 1。 若要執行此步驟，請從命令提示字元處執行下列命令：

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * 針對 Linux (**錯誤 95108**)，您必須選擇根帳戶，才能成功安裝行動代理程式。 此外，SFTP 服務應執行。 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證：
    1. 以 root 的身分登入。
    2. 前往 /etc/ssh/sshd_config 檔案，找到以 PasswordAuthentication 開頭這一行。
    3. 取消該行的註解，並將值變更為 yes。
    4. 找到以 Subsystem 開頭這一行，並取消其註解。
    5. 重新啟動 sshd 服務。

如果您想要修改所選使用者帳戶的認證，請遵循[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)所提供的指示。

## <a name="insufficient-privileges-failure-errorid-95517"></a>許可權失敗不足（錯誤 ID： 95517）

如果選擇安裝行動代理程式的使用者沒有系統管理員權限，將不會允許設定伺服器/相應放大處理序伺服器將行動代理程式軟體複製到來源機器。 因此，此錯誤是存取遭拒失敗的結果。 請確定使用者帳戶具備系統管理員權限。

如果您想要修改所選使用者帳戶的認證，請遵循[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)所提供的指示。

## <a name="insufficient-privileges-failure-errorid-95518"></a>許可權失敗不足（錯誤 ID： 95518）

當主域和工作站之間在嘗試登錄到源電腦時建立域信任關係失敗時，移動代理安裝將失敗，錯誤 ID 95518。 因此，請確保用於安裝移動代理的使用者帳戶具有通過源電腦的主域登錄的管理許可權。

如果您想要修改所選使用者帳戶的認證，請遵循[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)所提供的指示。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>登錄失敗（錯誤 ID： 95519， 95520， 95521， 95522）

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>使用者帳戶的憑據已被禁用（ErrorID： 95519）

啟用複寫期間選擇的使用者帳戶已經停用。 若要啟用使用者帳戶，請參閱[這篇文章](https://aka.ms/enable_login_user)，或使用實際的使用者名稱取代 *username* 以執行以下命令。
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>憑據由於多次失敗的登錄嘗試而鎖定（錯誤 ID： 95520）

多次重試存取機器失敗將會導致使用者帳戶遭到鎖定。 失敗原因可能是：

* 在組態設定期間提供的認證不正確，或
* 啟用複寫期間選擇的使用者帳戶是錯的

因此，請依照[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的指示修改選擇的認證，然後在一段時間之後重試作業。

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>源電腦上不支援登錄伺服器（ErrorID： 95521）

當來源機器上無法使用登入伺服器時，就會發生此錯誤。 無法使用登入伺服器會導致登入要求失敗，因此無法安裝行動代理程式。 若要成功登入，請確定來源機器上可以使用登入伺服器並啟動登入服務。 有關詳細說明，請參閱 KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg：當前沒有可用的登錄伺服器。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>登錄服務未在源電腦上運行（ErrorID： 95522）

來源機器上的登入服務未執行，導致登入要求失敗。 因此，無法安裝行動代理程式。 若要解決此問題，請確定來源機器上的登入服務在執行中，才能成功登入。 若要啟動登入服務，請從命令提示字元執行命令 "net start Logon"，或從工作管理員啟動 "NetLogon" 服務。

## <a name="connectivity-failure-errorid-95117--97118"></a>**連接故障（錯誤 ID： 95117 & 97118）**

設定伺服器/相應放大處理序伺服器會嘗試連線來源 VM 以安裝行動代理程式。 若因為網路連線問題而無法連線來源機器時，就會發生此錯誤。 解決方式：

* 請確定您能夠從組態伺服器偵測來源機器。 如果您已選擇在啟用複寫期間相應放大處理序伺服器，請確保您能夠從處理序伺服器偵測您的來源機器。
  * 在來源伺服器機器的命令列中，如下所示使用 Telnet 來偵測搭配 https 連接埠 (135) 的組態伺服器/相應放大處理序伺服器，以查看是否有任何網路連線問題或防火牆連接埠封鎖問題。

     `telnet <CS/ scale-out PS IP address> <135>`
* 此外，對於 **Linux VM**，
  * 請檢查是否已安裝最新的 openssh、openssh-server 和 openssl 套件。
  * 請檢查並確定安全殼層 (SSH) 已啟用且正在連接埠 22 上執行。
  * SFTP 服務應執行。 若要在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證，
    * 以 root 的身分登入。
    * 前往 /etc/ssh/sshd_config 檔案，找到以 PasswordAuthentication 開頭這一行。
    * 取消該行的註解，並將值變更為 yes
    * 找到以 Subsystem 開頭這一行，並取消其註解
    * 重新啟動 sshd 服務。
* 如果一段時間之後沒有適當的回應，連線嘗試可能會失敗，或是因為連線的主機無法回應，則建立的連線可能失敗。
* 這可能是連線/網路/網域相關的問題。 也可能是由於 DNS 名稱解析問題或 TCP 連接埠耗盡的問題。 檢查您的網域中是否有任何這類的已知問題。

## <a name="connectivity-failure-errorid-95523"></a>連接故障（錯誤 ID： 95523）

當找不到來源電腦所在的網路，或可能已被刪除或無法再使用時，就會發生此錯誤。 解決錯誤的唯一方法是確保網路存在。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>檔案及印表機共用服務檢查 (錯誤碼：95105 & 95106)

完成連線檢查之後，請確認您的虛擬機器上已啟用 [檔案及印表機共用服務]。 將行動代理程式複製到來源機器時，這些設定是必要的。

針對 **Windows 2008 R2 和先前版本**，

* 若要透過 Windows 防火牆啟用檔案與列印共用，
  * 請開啟 [控制台] -> [系統及安全性] -> [Windows 防火牆] -> 在左側窗格上，按一下 [進階設定] -> 在主控台樹狀目錄中按一下 [輸入規則]。
  * 找出 [檔案及印表機共用] (NB-Session-In) 和 [檔案及印表機共用] (Smb-in) 的規則。 針對每個規則，以滑鼠右鍵按一下該規則，然後按一下 [啟用規則]****。
* 若要透過群組原則啟用檔案共用，
  * 移至 [開始]，輸入 gpmc.msc 並搜尋。
  * 在 [導覽] 窗格中，開啟下列資料夾：本機電腦原則、使用者設定、系統管理範本、Windows 元件，以及網路共用。
  * 在 [詳細資料] 窗格中，按兩下 [防止使用者共用其設定檔內的檔案]****。 若要停用群組原則設定，並讓使用者能夠共用檔案，請按一下 [停用]。 按一下 [確定] 以儲存變更。 要瞭解更多資訊，請參閱[啟用或禁用群組原則的檔共用](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

對於**更高版本**，請按照安裝移動服務中提供的說明來啟用[VMware VM 和物理伺服器的災害復原](vmware-azure-install-mobility-service.md)，以啟用檔和印表機共用。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows 管理檢測 （WMI） 配置檢查（錯誤代碼：95103）

完成檔案和印表機服務檢查之後，請為私人、公用和網域設定檔啟用通過防火牆的 WMI 服務。 若要在來源機器上完成遠端執行，這些設定是必要的。 若要啟用：

* 移至 [控制台]，按一下 [安全性]，然後按一下 [Windows 防火牆]。
* 按一下 [變更設定]，然後按一下 [例外狀況] 索引標籤。
* 在 [例外狀況] 視窗中，選取 Windows Management Instrumentation (WMI) 的核取方塊，以啟用通過防火牆的 WMI 流量。 

您也可以在命令提示字元啟用通過防火牆的 WMI 流量。 請使用下列 `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` 命令
您可以在下列文件中找到其他 WMI 疑難排解文件。

* [基本 WMI 測試](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 故障排除](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [解決 WMI 腳本和 WMI 服務的問題](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>不支援的作業系統

失敗的另一個最常見原因可能是由於不支援的作業系統。 請確定您在可成功安裝行動服務的受支援作業系統/核心版本上。 請避免使用私人的修補程式。
若要檢視 Azure Site Recovery 所支援的作業系統和核心版本的清單，請參閱我們的[支援矩陣文件](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>不支援的引導磁片配置（ErrorID：95309、95310、95311）

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>啟動和系統磁碟分割/卷不是同一個磁片（ErrorID： 95309）

9.20 之前的版本不支援開機和系統磁碟分割/磁碟區位於不同磁碟的設定。 從 [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)開始支援這樣的設定。 請使用最新版本以取得此支援。

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>引導磁片不可用（錯誤 ID： 95310）

無法保護不含開機磁碟的虛擬機器。 這是為了確保在容錯移轉作業期間順利復原虛擬機器。 缺少開機磁碟會導致無法在容錯移轉之後啟動機器。 確定虛擬機器包含開機磁碟，然後重試此作業。 另請注意，不支援同部電腦上有多個開機磁碟。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>源電腦上存在多個引導磁片（錯誤 ID： 95311）

具有多個開機磁碟的虛擬機器不是[支援的組態](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>多個磁片上的系統磁碟分割（錯誤 ID： 95313）

9.20 之前的版本不支援開機磁碟分割或磁碟區位於多個磁碟的設定。 從 [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)開始支援這樣的設定。 請使用最新版本以取得此支援。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>啟用保護失敗，因為 GRUB 配置中提及的設備名稱，而不是 UUID（ErrorID：95320）

**可能的原因：** </br>
GRUB 設定檔（"/引導/grub/menu/menu.lst"、"/引導/grub/grub.cfg"、"/引導/grub2/grub.cfg"或"/等/預設/grub"）可能包含參數**根**的值，並**恢復**為實際設備名稱而不是 UUID。 Site Recovery 會強制執行 UUID 方法，因為裝置名稱可能會經由 VM 重新開機而變更，而且 VM 可能不會提出容錯移轉時的相同名稱而造成問題。 例如： </br>


- 下面這一行來自 GRUB 檔案 **/boot/grub2/grub.cfg**。 <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- 以下行來自 GRUB 檔 **/啟動/grub/menu.lst**
  *內核 /引導/vmlinuz-3.0.101-63 預設**根_/dev/sda2** **恢復_/dev/sda1**初始_靜默崩潰內核_256M-：128M 顯示 vga_0x314*

請觀察上述的粗體字串，其中 GRUB (而非 UUID) 具有 "root" 和 "resume" 參數的實際裝置名稱。
 
**修正方式：**<br>
裝置名稱應該取代為對應的 UUID。<br>


1. 通過執行命令">"查找\<設備的 UUID。 例如：<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. 現在，將設備名稱替換為其 UUID，其格式為"root_UUID=\<UUID>"。 例如，如果我們將設備名稱替換為 UUID，用於根和恢復參數，上述檔"/引導/grub2/grub.cfg"、"//引導/grub2/grub.cfg"或"/等/預設/grub：則檔中的行類似。 <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. 重新啟動保護

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>安裝移動服務完成，警告重新開機（ErrorID： 95265 & 95266）

Site Recovery 行動服務具有許多元件，其中一個名叫篩選器驅動程式。 篩選器驅動程式只會在系統重新啟動時被載入系統記憶體。 這代表對篩選器驅動程式的修正只能在新篩選器驅動程式載入時生效，而那只會在系統重新啟動時發生。

**請注意**，這只是警告，現有的複寫將繼續運作，即使在新代理程式更新之後也一樣。 您可以選擇隨時重新開機，您想要獲得新的篩選器驅動程式的好處，但如果不重新開機舊篩選器驅動程式繼續工作。 因此，在更新且不重新啟動的情況下，除了篩選器驅動程式之外，**行動服務中其他增強功能和修正的優點都會生效**。 因此，儘管建議，每次升級後都不必重新開機。 有關何時強制重新開機的資訊，請設置 Azure 網站恢復中的"服務"更新中的[移動代理升級部分之後的"重新開機源電腦](https://aka.ms/v2a_asr_reboot)"。

> [!TIP]
>有關在維護時段內計畫升級的最佳做法，請參閱 Azure 網站恢復中的服務更新中[的最新作業系統/內核版本支援](https://aka.ms/v2a_asr_upgrade_practice)。

## <a name="lvm-support-from-920-version"></a>從 9.20 版開始支援 LVM

9.20 之前的版本只有資料磁碟支援 LVM。 /boot 應該位於磁碟分割區，而不是 LVM 磁碟區。

從 [9.20 版本](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)開始支援 [LVM 上的 OS 磁碟](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。 請使用最新版本以取得此支援。

## <a name="insufficient-space-errorid-95524"></a>空間不足（錯誤 ID： 95524）

將行動代理程式複製到來源機器時，至少需要 100 MB 的可用空間。 因此，請確保來源機器具有必要的可用空間，然後重試作業。

## <a name="vss-installation-failures"></a>VSS 安裝失敗

VSS 安裝是行動代理程式安裝的一部分。 此服務是用於產生應用程式一致復原點的程序之中。 在 VSS 安裝期間發生失敗的原因有許多種。 若要識別確切的錯誤，請參閱 **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**。 幾個常見錯誤及其解決步驟，將於下列小節中說明。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 錯誤 -2147023170 [0x800706BE] - 結束代碼 511

當防毒軟體阻止 Azure 網站恢復服務的操作時，主要可以看到此問題。 若要解決此問題：

1. 排除[這裡](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)所提到的所有資料夾。
2. 遵循您的防毒軟體提供者所發佈的指導方針，以解除 Windows 中對 DLL 註冊的封鎖。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 錯誤 7 [0x7] - 結束代碼 511

這是執行階段錯誤，且是因記憶體不足以安裝 VSS 所導致。 請提升磁碟空間以成功完成此作業。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 錯誤 -2147023824 [0x80070430] - 結束代碼 517

此錯誤會在 Azure Site Recovery VSS 提供者服務[被標記為要進行刪除](https://msdn.microsoft.com/library/ms838153.aspx) \(英文\) 的情況下發生。 請嘗試執行下列命令列來在來源電腦上手動安裝 VSS

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 錯誤 -2147023841 [0x8007041F] - 結束代碼 512

此錯誤會在 Azure Site Recovery VSS 提供者服務資料庫[已鎖定](https://msdn.microsoft.com/library/ms833798.aspx)的情況下發生。請嘗試執行下列命令列來在來源電腦上手動安裝 VSS

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

如果發生故障，請檢查是否有任何防毒程式或其他服務卡在"開始"狀態。 這可能會保留資料庫服務上的鎖。 這將導致安裝 VSS 提供程式失敗。 確保沒有服務處於"啟動"狀態，然後重試上述操作。

### <a name="vss-exit-code-806"></a>VSS 結束代碼 806

此錯誤會在用於安裝的使用者帳戶沒有執行 CSScript 命令之權限的情況下發生。 請為該使用者帳戶提供必要權限以執行指令碼，並重試該作業。

### <a name="other-vss-errors"></a>其他 VSS 錯誤

請嘗試執行下列命令列來在來源電腦上手動安裝 VSS 提供者服務

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS 錯誤 - 0x8004E00F

由於 DCOM 中的問題，在安裝移動代理時通常會遇到此錯誤，並且 DCOM 處於嚴重狀態。

使用以下過程確定錯誤的原因。

**檢查安裝日誌**

1. 打開位於 c：\程式資料\ASRSetupLogs_ASRUnifiedAgent安裝程式.log 的安裝日誌。
2. 存在以下錯誤指示此問題：

    正在取消註冊現有應用程式... 創建目錄物件 獲取應用程式的集合 

    錯誤：

    - 錯誤代碼： -2147164145 [0x8004E00F]
    - 結束代碼： 802

若要解決此問題：

請與[Microsoft Windows 平臺團隊](https://aka.ms/Windows_Support)聯繫，以獲得解決 DCOM 問題的説明。

解決 DCOM 問題時，使用以下命令手動重新安裝 Azure 網站恢復 VSS 提供程式：
 
**C：\程式檔 （x86）\微軟 Azure 網站恢復\代理>"C：\程式檔 （x86）\微軟 Azure 網站恢復\代理\InMageVSSProvider_Install.cmd**
  
如果應用程式一致性對於災害復原要求並不重要，您可以繞過 VSS 提供程式安裝。 

要繞過 Azure 網站恢復 VSS 提供程式安裝並手動安裝 Azure 網站恢復 VSS 提供程式後安裝：

1. 安裝移動服務。 
   > [!Note]
   > 
   > 安裝將在"安裝後配置"步驟失敗。 
2. 要繞過 VSS 安裝，
   1. 打開位於以下位置的 Azure 網站恢復移動服務安裝目錄：
   
      C：\程式檔 （x86）\微軟 Azure 網站恢復\代理
   2. 修改 Azure 網站恢復 VSS 提供程式安裝腳本**nMageVSSProvider_Install**和**InMageVSSProvider_Uninstall.cmd，** 以便始終通過添加以下行成功：
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. 手動重新運行移動代理安裝。 
4. 當安裝成功並移動到下一步時，**配置**，刪除您添加的行。
5. 要安裝 VSS 提供程式，請以管理員身份打開命令提示符並運行以下命令：
   
    **C：\程式檔 （x86）\微軟 Azure 網站恢復\代理> ._InMageVSSProvider_Install.cmd**

9. 驗證 ASR VSS 提供程式是否作為服務安裝在 Windows 服務中，並打開元件服務 MMC 以驗證是否列出了 ASR VSS 提供程式。
10. 如果 VSS 提供程式安裝繼續失敗，請使用 CX 解決 CAPI2 中的許可權錯誤。

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>VSS 提供程式安裝失敗，因為叢集服務在非群集電腦上啟用

此問題會導致 Azure 網站恢復移動代理安裝在 ASAzure 網站恢復 R VSS 提供程式安裝步驟期間失敗，原因是 COM+ 出現問題，無法安裝 VSS 提供程式。
 
### <a name="to-identify-the-issue"></a>確定問題

在位於佈建服務器上的日誌 C：_程式資料\ASRSetupLogs_上傳日誌\<日期時間>UA_InstallLogFile.log 中，您將發現以下異常：

COM+ 無法與 Microsoft 分散式交易協調器通信（HRESULT 的例外情況：0x8004E00F）

若要解決此問題：

1.  驗證此電腦是否為非群集電腦，以及未使用群集元件。
3.  如果未使用元件，請從電腦中刪除群集元件。

## <a name="drivers-are-missing-on-the-source-server"></a>源伺服器上缺少驅動程式

如果移動代理安裝失敗，請檢查 C：_ProgramData_ASRSetupLogs 下的日誌，以確定某些控制集中是否缺少某些必需的驅動程式。
 
若要解決此問題：
  
1. 使用登錄編輯程式（如 regedit.msc）打開註冊表。
2. 打開HKEY_LOCAL_MACHINE_SYSTEM 節點。
3. 在 SYSTEM 節點中，找到控制項集。
4. 打開每個控制項集並驗證是否存在以下 Windows 驅動程式：

   - 阿塔皮
   - Vmbus
   - 斯托爾特
   - 斯托夫斯克
   - intelide
 
重新安裝任何丟失的驅動程式。

## <a name="next-steps"></a>後續步驟

[了解如何](vmware-azure-tutorial.md)為 VMware VM 設定災害復原。
