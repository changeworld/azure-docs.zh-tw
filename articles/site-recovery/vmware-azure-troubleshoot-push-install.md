---
title: 使用 Azure 網站恢復對行動服務推送安裝進行故障排除
description: 使用 Azure 網站恢復啟用災難復原複製時,排除行動服務安裝錯誤。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656314"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>容錯排除行動服務推送安裝

移動服務安裝是啟用複製的關鍵步驟。 此步驟的成功取決於滿足先決條件和使用支援的配置。 在行動服務安裝期間,您可能面臨的最常見故障是由於:

* [認證/權限錯誤](#credentials-check-errorid-95107--95108)
* [登入失敗](#login-failures-errorid-95519-95520-95521-95522)
* [連線錯誤](#connectivity-failure-errorid-95117--97118)
* [檔案及印表機共用錯誤](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows 管理偵測 (WMI) 故障](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [不支援的作業系統](#unsupported-operating-systems)
* [不支援的開機設定](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [陰影複製服務 (VSS) 安裝失敗](#vss-installation-failures)
* [裝置名稱位於 GRUB 設定而非裝置 UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [邏輯卷管理員 (LVM) 卷片](#lvm-support-from-920-version)
* [重新啟動警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

啟用複製時,Azure 網站恢復將嘗試在虛擬機器(VM)上安裝行動服務代理。 作為此過程的一部分,配置伺服器嘗試與虛擬機器連接並複製代理。 要啟用成功安裝,請遵循分步故障排除指南。

## <a name="credentials-check-errorid-95107--95108"></a>認證檢查 (錯誤碼：95107 & 95108)

驗證啟用複製期間選擇的使用者帳戶是否有效且準確。 Azure 網站恢復需要具有**管理員許可權**的**根**帳戶或使用者帳戶執行推送安裝。 否則,推送安裝將在源電腦上被阻止。

對於 Windows(**錯誤 95107),** 驗證使用者帳戶在源電腦上具有管理存取許可權,以及本地帳戶或網域帳戶。 如果您不使用域帳戶,則需要在本地電腦上禁用遠端使用者訪問控制。

* 要手動添加禁用遠端使用者存取控制的註冊表項,請執行:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * 新增`DWORD`:`LocalAccountTokenFilterPolicy`
  * 將值設定為`1`

* 要新增註冊表項,從命令提示符中,執行以下命令:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

對於 Linux(**錯誤 95108),** 您必須選擇成功安裝行動服務代理的**根**帳戶。 此外,應運行 SSH 檔案傳輸協定 (SFTP) 服務。 要在_sshd_config_檔案中啟用 SFTP 子系統和密碼身份驗證,請執行以下操作:

1. 以**root**身份登錄。
1. 跳到 _/etc/ssh/sshd_config檔_,`PasswordAuthentication`找到以 開頭的行。
1. 取消註解行,並將值更改為`yes`。
1. 查找以`Subsystem`開頭的行,然後取消註釋行。
1. 重新啟動 `sshd` 服務。

如果要修改所選使用者帳戶的憑據,請按照[這些說明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)操作。

## <a name="insufficient-privileges-failure-errorid-95517"></a>權限失敗不足(錯誤代碼: 95517)

當選擇安裝行動代理的使用者沒有管理員許可權時,不允許配置伺服器/橫向擴展進程伺服器將行動代理軟體複製到源電腦。 此錯誤是訪問被拒絕失敗的結果。 請確定使用者帳戶具備系統管理員權限。

如果要修改所選使用者帳戶的憑據,請按照[這些說明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)操作。

## <a name="insufficient-privileges-failure-errorid-95518"></a>權限失敗不足(錯誤代碼: 95518)

當主域和工作站之間的域信任關係建立在嘗試登錄到源計算機時失敗時,移動代理安裝將失敗,錯誤 ID 95518。 確保用於安裝行動代理的使用者帳戶具有透過源電腦的主域登錄的管理許可權。

如果要修改所選使用者帳戶的憑據,請按照[這些說明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)操作。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>登入失敗(錯誤 ID: 95519, 95520, 95521, 95522)

本節介紹憑據和登錄錯誤消息。

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>使用者帳戶的認證已被禁用(ErrorID: 95519)

啟用複製期間選擇的使用者帳戶已禁用。 要啟用使用者帳戶,請參閱[本文](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser)或運行以下命令,將文本_使用者名_替換為實際使用者名。

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>認證的登入失敗的登入鎖定(錯誤 ID: 95520)

多次重試存取機器失敗將會導致使用者帳戶遭到鎖定。 失敗原因可能是：

* 配置設置期間提供的憑據不正確。
* 啟用複製期間選擇的使用者帳戶是錯誤的。

按照[這些說明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)修改所選憑據,然後重試操作。

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>來源電腦上不提供登入伺服器(ErrorID: 95521)

當登錄伺服器在源電腦上不可用時,將發生此錯誤。 如果登入伺服器不可用,登錄請求將失敗,無法安裝行動代理。 要成功登錄,請確保登錄伺服器在源電腦上可用,並啟動 Netlogon 服務。 有關詳細資訊,請參閱[Windows 登入方案](/windows-server/security/windows-authentication/windows-logon-scenarios)。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>登入服務未在源電腦上執行(ErrorID: 95522)

登錄服務未在源電腦上運行,並導致登錄請求失敗。 無法安裝行動代理。 要解決錯誤,請使用以下方法之一在源電腦上啟動`Netlogon`服務:

* 要從指令`Netlogon`提示符啟動服務,執行`net start Netlogon`命令 。
* 從任務管理器開始`Netlogon`服務。

## <a name="connectivity-failure-errorid-95117--97118"></a>連線故障(錯誤 ID: 95117 & 97118)

配置伺服器/橫向擴展進程伺服器嘗試連接到源 VM 以安裝行動代理。 當源電腦由於網路連接問題而無法訪問時,將發生此錯誤。

若要解決此錯誤：

* 確保可以從配置伺服器 ping 源電腦。 如果在啟用複製期間選擇了橫向擴展進程伺服器,請確保可以從進程伺服器 ping 源電腦。

* 從源伺服器電腦命令行中,用於`Telnet`ping HTTPS 埠 135 上的配置伺服器或橫向擴展進程伺服器,如下命令所示。 此命令檢查是否有任何網路連接問題或防火牆埠阻塞問題。

  `telnet <CS/ scale-out PS IP address> <135>`

* 此外,對於 Linux VM:
  * 檢查是否安裝了最新的 OpenSSH、OpenSSH 伺服器和 OpenSSL 套件。
  * 請檢查並確定安全殼層 (SSH) 已啟用且正在連接埠 22 上執行。
  * SFTP 服務應執行。 要在_sshd_config_檔案中啟用 SFTP 子系統和密碼身份驗證,請執行以下操作:

    1. 以**root**身份登錄。
    1. 跳到 _/etc/ssh/sshd_config_檔,`PasswordAuthentication`找到以 開頭的行。
    1. 取消註解行,並將值更改為`yes`。
    1. 查找以`Subsystem`開頭的行,然後取消註釋行
    1. 重新啟動 `sshd` 服務。

* 如果在一段時間后沒有適當的回應,或者由於連接的主機無法回應而已建立的連接失敗,則連接嘗試可能會失敗。
* 這可能是連接/網路/域相關問題。 這也可能因為 DNS 名稱解決了問題或 TCP 埠耗盡問題。 檢查您的網域中是否有任何這類的已知問題。

## <a name="connectivity-failure-errorid-95523"></a>連線故障(錯誤代碼: 95523)

當源電腦所在的網路找不到、可能已刪除或不再可用時,將發生此錯誤。 解決錯誤的唯一方法是確保網路存在。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>檔案及印表機共用服務檢查 (錯誤碼：95105 & 95106)

在連接檢查後,驗證虛擬機器上是否已啟用檔和印表機共用服務。 這些設置需要將行動代理複製到源電腦。

對於**Windows 2008 R2 與以前的版本**:

* 若要透過 Windows 防火牆啟用檔案與列印共用，
  1. 開啟**控制面板** > **系統與安全** > **視窗防火牆**。 在左側窗格中,在控制台樹中選擇 **「輸入規則」的「進階設定** > **Inbound Rules**」 。。
  1. 找出 [檔案及印表機共用] (NB-Session-In) 和 [檔案及印表機共用] (Smb-in) 的規則。
  1. 針對每個規則，以滑鼠右鍵按一下該規則，然後按一下 [啟用規則]****。

* 要使用群組策略啟用檔案分享,可以:
  1. 轉到 **「開始」,** 鍵`gpmc.msc`入 和搜尋。
  1. 在瀏覽窗格中,開啟以下資料夾:**本地電腦政策** > **使用者設定** > **管理樣本** > **Windows 元件** > **網路分享**。
  1. 在 [詳細資料] 窗格中，按兩下 [防止使用者共用其設定檔內的檔案]****。

     要關閉組策略設定,並啟用使用者共用檔案的能力,請選擇「**禁用**」。

  1. 選取 [ **確定** ] 以儲存變更。

  要瞭解更多資訊,請參閱[啟用或關閉群組政策的檔案共享](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

要為更高版本的 Windows 或 Linux 啟用檔案和印表機分享,請按照[安裝行動服務中的說明進行 VMware VM 和實體伺服器的災難恢復](vmware-azure-install-mobility-service.md)。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows 管理偵測 (WMI) 設定檢查(錯誤代碼:95103)

檢查檔和印表機服務後,通過防火牆為私有、公共和域配置檔啟用 WMI 服務。 若要在來源機器上完成遠端執行，這些設定是必要的。

要開啟 WMI:

1. 跳至**控制面板** > **安全**並選擇**Windows 防火牆**。
1. 選擇 **「更改設定**」,然後選擇「**例外」** 選項卡。
1. 在 **"例外"** 視窗中,選擇 Windows 管理檢測 (WMI) 的複選框,以啟用通過防火牆的 WMI 流量。

還可以通過命令提示符通過防火牆啟用 WMI 流量,並具有以下命令:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

您可以在下列文件中找到其他 WMI 疑難排解文件。

* [基本 WMI 測試](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 故障排除](/windows/win32/wmisdk/wmi-troubleshooting)
* [解決 WMI 文稿和 WMI 服務的問題](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>不支援的作業系統

失敗的另一個常見原因可能是操作系統不受支援。 使用受支援的作業系統和內核版本成功安裝行動服務。 避免使用專用修補程式。

要檢視 Azure 網站恢復支援的作業系統和核心版本的清單,請參閱[支援矩陣文件](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>不支援的開機磁碟配置(ErrorID:95309、95310、95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>啟動與系統分割區/捲不是同一個磁碟(ErrorID: 95309)

在 9.20 版本之前,不同磁碟上的啟動和系統分區/卷是不受支援的配置。 從[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)開始,此配置受支援。

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>開機磁碟不可用(錯誤 ID: 95310)

無法保護沒有引導磁碟的虛擬機器。 引導磁碟可確保在故障轉移操作期間虛擬機順利恢復。 缺少引導磁碟會導致故障轉移後無法啟動計算機。 確保虛擬機包含引導磁碟並重試該操作。 此外,不支援同一台電腦上的多個引導磁碟。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>來源電腦上存在多個開機磁碟(錯誤 ID: 95311)

具有多個開機磁碟的虛擬機器不是[支援的設定](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>多個磁碟上的系統分割區(錯誤 ID: 95313)

在 9.20 版本之前,多個磁碟上的根分區或卷設置是不受支援的配置。 從[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)開始,此配置受支援。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>啟用保護失敗,因為 GRUB 設定中提及的設備名稱,而不是 UUID(ErrorID:95320)

### <a name="possible-cause"></a>可能的原因

大統一引導載入程式 (GRUB) 設定檔 _(/boot/grub/menu.lst,_ _/引導/grub/grub.cfg,_ _/引導/grub2/grub.cfg,_ 或 _/etc/預設/grub)_ 可能包含參數**根**的值,並作為實際設備名稱**恢復**,而不是通用唯一標識符 (UUID)。 網站恢復要求 UUID 方法,因為設備名稱可能會在 VM 重新啟動時更改。 例如,VM 在故障轉移時可能不會以相同名稱連線,從而導致問題。

例如：

- 以下行來自 GRUB 檔 _/引導/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 以下行來自 GRUB 檔 _/引導/grub/選單.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB 線路包含參數**root**和**繼續**的實際設備名稱,而不是 UUID。

### <a name="how-to-fix"></a>如何修復

裝置名稱應該取代為對應的 UUID。

1. 通過執行命令`blkid \<device name>`查找設備的 UUID。

   例如：

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 現在,將裝置名稱替換為其 UUID`root=UUID=\<UUID>`的格式,如 。 例如,如果我們將設備名稱替換為 UUID,用於根和恢復參數,在檔 _/boot/grub2/grub.cfg、/boot/grub2/grub.cfg_或 _/etc/default/grub_中,檔中的 _/boot/grub2/grub.cfg_行如下所示:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 重新啟動保護。

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>安裝行動服務,警告重新啟動(ErrorID: 95265 & 95266)

網站恢復行動服務具有許多元件,其中一個元件稱為篩選器驅動程式。 篩選器驅動程式僅在系統重新啟動期間載入到系統記憶體中。 篩選器驅動程式修復只能在系統重新啟動時載入新的篩選器驅動程式時才能實現。

> [!IMPORTANT]
> 這是一個警告,即使新代理更新後,現有複製也會起作用。 您可以選擇隨時重新啟動,您想要獲得新的篩選器驅動程式的好處,但如果不重新啟動,舊的篩選器驅動程式將繼續工作。 因此,在更新後沒有重新啟動,除了篩選器驅動程式,**其他增強和修復在行動服務的好處得到實現**。 儘管建議,但每次升級后重新啟動並不是強制性的。 有關何時強制重新啟動的資訊,請設置 Azure 網站恢復中的"服務更新"中的[「移動服務升級後重新啟動](service-updates-how-to.md#reboot-after-mobility-service-upgrade)」部分。

> [!TIP]
>有關在維護時段內計畫升級的最佳做法,請參閱 Azure 網站恢復中服務更新中[的最新作業系統/內核支援](service-updates-how-to.md#support-for-latest-operating-systemskernels)。

## <a name="lvm-support-from-920-version"></a>從 9.20 版開始支援 LVM

在 9.20 版本之前,僅支援數據磁碟的邏輯卷管理器 (LVM)。 分區`/boot`應位於磁碟分區上,而不是 LVM 卷上。

從[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)開始[,LVM 上的 OS 磁碟](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)受支援。

## <a name="insufficient-space-errorid-95524"></a>空間不足(錯誤 ID: 95524)

將行動代理複製到源電腦時,至少需要 100 MB 可用空間。 確保源電腦具有所需的可用空間量,然後重試操作。

## <a name="vss-installation-failures"></a>VSS 安裝失敗

捲影複製服務 (VSS) 安裝是行動代理安裝的一部分。 此服務用於生成應用程式一致的恢復點。 在 VSS 安裝期間發生失敗的原因有許多種。 要辨識確切的錯誤,請參閱_C:_程式資料\ASRSetupLogs_ASRUnifiedAgent安裝程式.log_。 以下部分將突出顯示一些常見錯誤和解決方法步驟。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 錯誤 -2147023170 [0x800706BE] - 結束代碼 511

當防病毒軟體阻止 Azure 網站恢復服務的操作時,最常看到此問題。

若要解決此問題：

1. 檢視[防病毒程式中的資料夾排除](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)清單。
1. 按照防病毒供應商發佈的準則取消阻止在 Windows 中註冊 DLL。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 錯誤 7 [0x7] - 結束代碼 511

此錯誤是由於記憶體不足而導致的運行時錯誤,無法安裝 VSS。 增加磁碟空間,以便成功完成此操作。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 錯誤 -2147023824 [0x80070430] - 結束代碼 517

此錯誤會在 Azure Site Recovery VSS 提供者服務[被標記為要進行刪除](/previous-versions/ms838153(v=msdn.10)) \(英文\) 的情況下發生。 嘗試透過執行以下指令在源電腦上手動安裝 VSS:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 錯誤 -2147023841 [0x8007041F] - 結束代碼 512

當 Azure 網站恢復 VSS 提供程式服務資料庫[被鎖定](/previous-versions/ms833798(v=msdn.10))時,將發生此錯誤。嘗試透過從指令提示符執行以下指令,在源電腦上手動安裝 VSS:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

發生故障時,請檢查是否有任何防病毒程式或其他服務處於 **「啟動」** 狀態。 處於 **「啟動」** 狀態的進程可以保留資料庫服務上的鎖。 這將導致安裝 VSS 提供程式失敗。 確保沒有服務處於 **「啟動」** 狀態,然後重試上述操作。

### <a name="vss-exit-code-806"></a>VSS 結束代碼 806

當用於安裝的使用者帳戶沒有執行命令的許可權時,將發生此`CSScript`錯誤。 請為該使用者帳戶提供必要權限以執行指令碼，並重試該作業。

### <a name="other-vss-errors"></a>其他 VSS 錯誤

試著透過從指令提示符執行以下指令,在源電腦上手動安裝 VSS 提供者服務:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS 錯誤 ─ 0x8004E00F

此錯誤通常在安裝移動代理期間發生,因為行動代理處於`DCOM`中,並且`DCOM`處於嚴重狀態。

使用以下過程確定錯誤的原因。

### <a name="examine-the-installation-logs"></a>檢查安裝紀錄

1. 打開位於_C:\程式資料\ASRSetupLogs_ASRUnifiedAgent安裝程式.log_的安裝日誌。
2. 存在以下錯誤指示此問題:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

若要解決此問題：

請與[Microsoft Windows 平台團隊](https://aka.ms/Windows_Support)聯繫,以獲得解決 DCOM 問題。

解決 DCOM 問題時,使用指令提示符中的以下命令手動重新安裝 Azure 網站恢復 VSS 提供者:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

如果應用程式一致性對於災難恢復要求並不重要,您可以繞過 VSS 提供程式安裝。

要繞過 Azure 網站復原 VSS 提供程式安裝並手動安裝 Azure 網站復原 VSS 提供者後安裝:

1. 安裝行動服務。 安裝將步驟中失敗:**安裝後設定**。
1. 要繞過 VSS 安裝,
   1. 開啟位於以下位置的 Azure 網站恢復行動服務安裝目錄:

      _C:\程式檔 (x86)\微軟 Azure 網站恢復\代理_

   1. 修改 Azure 網站恢復 VSS 提供程式安裝文稿_InMageVSSProvider_Install_和_InMageVSSProvider_Uninstall.cmd,_ 以便始終透過新增以下行成功:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. 手動安裝行動代理。
1. 您安裝成功並移動到下一步時,**設定**,刪除您添加的行。
1. 要安裝 VSS 提供者,請以管理員身份開啟命令提示符並執行以下指令:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. 驗證 Azure 網站恢復 VSS 提供程式是否作為服務在 Windows 服務中安裝。 開啟元件服務 MMC 以確認已列出 VSS 提供程式。
1. 如果 VSS 提供程式安裝繼續失敗,請與技術支援一起解決加密應用程式程式設計介面 (CAPI2) 中的許可權錯誤。

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>VSS 提供程式安裝失敗,因為在非叢集電腦上啟用叢集服務

此問題會導致 Azure 網站恢復行動代理安裝在 Azure 網站恢復 VSS 提供程式安裝期間失敗。 失敗的原因是存在阻止 VSS 提供`COM+`程式安裝 的問題。

### <a name="to-identify-the-issue"></a>確定問題

在位於設定伺服器上的紀錄中 _,C:\程式資料\ASRSetupLogs_上傳的\<Logs日期時間>UA_InstallLogFile.log,_ 你會發現以下異常:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

若要解決此問題：

1. 驗證此電腦是否為非群集計算機,以及未使用群集元件。
1. 如果未使用元件,請從電腦中刪除群組。

## <a name="drivers-are-missing-on-the-source-server"></a>來源伺服器上缺少驅動程式

如果移動代理安裝失敗,請檢查_C:_ProgramData_ASRSetupLogs_下的日誌,以確定某些控制集中是否缺少某些必需的驅動程式。

若要解決此問題：

1. 使用註冊表編輯器(如`regedit.msc`)打開註冊表。
1. 打開`HKEY_LOCAL_MACHINE\SYSTEM`節點。
1. 在節點`SYSTEM`中,找到控制集。
1. 開啟每個控制器及認證是否存在以下 Windows 驅動程式:

   * 阿塔皮
   * Vmbus
   * 斯托爾特
   * 斯托夫斯克
   * 英特爾

1. 重新安裝任何丟失的驅動程式。

## <a name="next-steps"></a>後續步驟

[詳細瞭解如何](vmware-azure-tutorial.md)為 VMware VM 設置災難恢復。
