---
title: 使用 Azure Site Recovery 針對行動服務推送安裝進行疑難排解
description: 針對使用 Azure Site Recovery 啟用損毀修復的複寫時，針對行動服務安裝錯誤進行疑難排解。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 8ee6449f357a578b30809bb03723ac1556e4f459
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816154"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>針對行動服務推送安裝進行疑難排解

行動服務安裝是啟用複寫的重要步驟。 此步驟的成功與否取決於符合必要條件和使用支援的設定。 在行動服務安裝期間，您可能會遇到的最常見失敗原因是：

* [認證/權限錯誤](#credentials-check-errorid-95107--95108)
* [登入失敗](#login-failures-errorid-95519-95520-95521-95522)
* [連線錯誤](#connectivity-failure-errorid-95117--97118)
* [檔案及印表機共用錯誤](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows Management Instrumentation (WMI) 失敗](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [不支援的作業系統](#unsupported-operating-systems)
* [不支援的開機設定](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [磁片區陰影複製服務 (VSS) 安裝失敗](#vss-installation-failures)
* [裝置名稱位於 GRUB 設定而非裝置 UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [邏輯磁片區管理員 (LVM) 磁片區](#lvm-support-from-920-version)
* [重新啟動警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

當您啟用複寫時，Azure Site Recovery 會嘗試在虛擬機器上安裝行動服務代理程式， (VM) 。 在此程式中，設定伺服器會嘗試連接虛擬機器並複製代理程式。 若要啟用成功的安裝，請遵循逐步疑難排解指引。

## <a name="credentials-check-errorid-95107--95108"></a>認證檢查 (錯誤碼：95107 & 95108)

確認在啟用複寫期間選擇的使用者帳戶是否有效且正確。 Azure Site Recovery 需要具有**系統管理員許可權**的**根**帳戶或使用者帳戶，才能執行推送安裝。 否則，將會封鎖來源機器上的推送安裝。

針對 Windows (**錯誤 95107**) ，請使用本機帳戶或網域帳戶，確認使用者帳戶具有來源電腦的系統管理存取權。 如果您不是使用網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。

* 若要手動新增可停用遠端使用者存取控制的登錄機碼：

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * 加入新的 `DWORD` ： `LocalAccountTokenFilterPolicy`
  * 將值設定為 `1`

* 若要新增登錄機碼，請在命令提示字元中執行下列命令：

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

針對 Linux (**錯誤 95108**) ，您必須選擇 **根** 帳戶，才能成功安裝行動服務代理程式。 此外，SSH 檔案傳輸通訊協定 (SFTP) 服務應該正在執行。 若要在 _sshd_config_ 檔案中啟用 SFTP 子系統和密碼驗證：

1. 以 **root**身份登入。
1. 移至 _/etc/ssh/sshd_config_檔案，尋找開頭為的行 `PasswordAuthentication` 。
1. 將該行取消批註，並將值變更為 `yes` 。
1. 找出開頭為的行 `Subsystem` ，然後將該行取消批註。
1. 重新啟動 `sshd` 服務。

如果您想要修改所選使用者帳戶的認證，請依照下列 [指示](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)進行。

## <a name="insufficient-privileges-failure-errorid-95517"></a>許可權不足失敗 (錯誤識別碼： 95517) 

當選擇安裝行動代理程式的使用者沒有系統管理員許可權時，設定伺服器/相應放大進程伺服器將不允許將行動代理程式軟體複製到來源機器。 此錯誤是拒絕存取失敗的結果。 請確定使用者帳戶具備系統管理員權限。

如果您想要修改所選使用者帳戶的認證，請依照下列 [指示](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)進行。

## <a name="insufficient-privileges-failure-errorid-95518"></a>許可權不足失敗 (錯誤識別碼： 95518) 

當主域信任關係在主域和工作站之間建立失敗，而嘗試登入來源機器時，行動代理程式安裝會失敗，錯誤識別碼為95518。 請確定用來安裝行動代理程式的使用者帳戶具有系統管理許可權，才能透過來源機器的主域進行登入。

如果您想要修改所選使用者帳戶的認證，請依照 [這些指示](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)進行。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>登入失敗 (錯誤識別碼：95519、95520、95521、95522) 

本節說明認證和登入錯誤訊息。

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>已停用使用者帳戶的認證 (錯誤識別碼： 95519) 

啟用複寫期間選擇的使用者帳戶已停用。 若要啟用使用者帳戶，請參閱 [這篇文章](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) ，或藉由將文字使用者名稱取代為 _實際的使用者_ 名稱來執行下列命令。

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>因為多次登入嘗試失敗，所以已鎖定認證 (錯誤識別碼： 95520) 

多次重試存取機器失敗將會導致使用者帳戶遭到鎖定。 失敗原因可能是：

* 設定設定期間提供的認證不正確。
* 啟用複寫期間選擇的使用者帳戶錯誤。

依照下列 [指示](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) 修改選擇的認證，然後再次嘗試操作。

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>來源機器上無法使用登入伺服器 (錯誤識別碼： 95521) 

當來源機器上無法使用登入伺服器時，就會發生此錯誤。 如果無法使用登入伺服器，登入要求將會失敗，而且無法安裝行動代理程式。 若要成功登入，請確定來源機器上的登入伺服器可供使用，並啟動 Netlogon 服務。 如需詳細資訊，請參閱 [Windows 登入案例](/windows-server/security/windows-authentication/windows-logon-scenarios)。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>登入服務未在來源機器上執行 (錯誤識別碼： 95522) 

登入服務未在來源機器上執行，並導致登入要求失敗。 無法安裝行動代理程式。 若要解決此錯誤，請使用下列其中一種方法來啟動 `Netlogon` 來源機器上的服務：

* 若要 `Netlogon` 從命令提示字元啟動服務，請執行命令 `net start Netlogon` 。
* 從工作管理員，啟動 `Netlogon` 服務。

## <a name="connectivity-failure-errorid-95117--97118"></a>連線能力失敗 (錯誤識別碼： 95117 & 97118) 

設定伺服器/相應放大進程伺服器會嘗試連線到來源 VM，以安裝行動代理程式。 當來源電腦因為網路連線問題而無法連線時，就會發生此錯誤。

若要解決此錯誤：

* 確定您可以從設定伺服器偵測來源電腦。 如果您在啟用複寫期間選擇相應放大進程伺服器，請確定您可以從進程伺服器偵測來源電腦。

* 從來源伺服器電腦命令列，使用 `Telnet` 來偵測 HTTPS 埠135上的設定伺服器或相應放大進程伺服器，如下列命令所示。 此命令會檢查是否有任何網路連線問題或防火牆埠封鎖問題。

  `telnet <CS/ scale-out PS IP address> <135>`

* 此外，針對 Linux VM：
  * 檢查是否已安裝最新的 OpenSSH、OpenSSH 伺服器和 OpenSSL 套件。
  * 請檢查並確定安全殼層 (SSH) 已啟用且正在連接埠 22 上執行。
  * SFTP 服務應執行。 若要在 _sshd_config_ 檔案中啟用 SFTP 子系統和密碼驗證：

    1. 以 **root**身份登入。
    1. 移至 _/etc/ssh/sshd_config_ 檔案，尋找開頭為的行 `PasswordAuthentication` 。
    1. 將該行取消批註，並將值變更為 `yes` 。
    1. 找出開頭為的行 `Subsystem` ，然後將該行取消批註
    1. 重新啟動 `sshd` 服務。

* 如果在一段時間後沒有適當的回應，或是因為連線的主機無法回應而建立的連接失敗，則連線嘗試可能會失敗。
* 這可能是連線能力/網路/網域相關的問題。 這也可能是因為 DNS 名稱正在解決問題或 TCP 埠耗盡問題。 檢查您的網域中是否有任何這類的已知問題。

## <a name="connectivity-failure-errorid-95523"></a>連線能力失敗 (錯誤識別碼： 95523) 

如果找不到來源電腦所在的網路、可能已刪除或無法再使用，就會發生此錯誤。 解決此錯誤的唯一方法是確定網路存在。

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>檢查來源電腦上網路共用資料夾的存取 (錯誤識別碼：95105、95523) 

確認虛擬機器上的網路共用資料夾是否可從進程伺服器 (PS) 使用指定的認證從遠端存取。 確認存取： 

1. 登入您的進程伺服器電腦。
2. 開啟檔案總管。 在網址列中輸入， `\\<SOURCE-MACHINE-IP>\C$` 然後按 Enter 鍵。

    ![在 PS 中開啟資料夾](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. 檔案總管會提示輸入認證。 輸入使用者名稱和密碼，然後按一下 [確定]。 <br><br/>

    ![提供認證](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > 如果來源電腦已加入網域，請提供功能變數名稱和使用者名稱做為 `<domainName>\<username>` 。 如果來源電腦在工作群組中，請只提供使用者名稱。

4. 如果連接成功，將會從進程伺服器遠端顯示來源電腦的資料夾。

    ![來源電腦的可見資料夾](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

如果連線不成功，請檢查是否符合所有必要條件。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>檔案及印表機共用服務檢查 (錯誤碼：95105 & 95106)

連接檢查之後，請確認您的虛擬機器上是否已啟用檔案和印表機共用服務。 需要這些設定，才能將行動代理程式複製到來源機器。

針對 **Windows 2008 R2 和先前版本**：

* 若要透過 Windows 防火牆啟用檔案與列印共用，
  1. 開啟**主控台**  >  **系統和安全性**  >  **Windows 防火牆**。 在左窗格中，選取 [**設定**  >  主控台樹中的 [**輸入規則**] 的 [Advanced settings]。
  1. 找出 [檔案及印表機共用] (NB-Session-In) 和 [檔案及印表機共用] (Smb-in) 的規則。
  1. 針對每個規則，以滑鼠右鍵按一下該規則，然後按一下 [啟用規則]****。

* 若要使用群組原則啟用檔案共用：
  1. 移至 [ **開始**]，輸入 `gpmc.msc` 並搜尋。
  1. 在流覽窗格中，開啟下列資料夾：**本機電腦原則**  >  **使用者**設定  >  **系統管理範本**  >  **Windows 元件**  >  的**網路共用**。
  1. 在 [詳細資料] 窗格中，按兩下 [防止使用者共用其設定檔內的檔案]****。

     若要停用群組原則設定，並啟用使用者共用檔案的能力，請選取 [ **已停用**]。

  1. 按一下 [確定]  以儲存您的變更。

  若要深入瞭解，請參閱 [啟用或停用群組原則的檔案共用](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

若要為較新版本的 Windows 或 Linux 啟用檔案及印表機共用，請依照 [安裝行動服務，以進行 VMware vm 和實體伺服器](vmware-azure-install-mobility-service.md) 的嚴重損壞修復。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) 設定檢查 (錯誤碼： 95103) 

在檔案和印表機服務檢查之後，請透過防火牆啟用私人、公用和網域設定檔的 WMI 服務。 若要在來源機器上完成遠端執行，這些設定是必要的。

若要啟用 WMI：

1. 移至**主控台**  >  **安全性**]，然後選取 [ **Windows 防火牆**]。
1. 選取 [ **變更設定** ]，然後選取 [ **例外** 狀況] 索引標籤。
1. 在 [ **例外** 狀況] 視窗中，選取 [wmi) 的 Windows Management Instrumentation (] 核取方塊，以啟用通過防火牆的 wmi 流量。

您也可以使用下列命令，從命令提示字元透過防火牆啟用 WMI 流量：

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

您可以在下列文件中找到其他 WMI 疑難排解文件。

* [基本 WMI 測試](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [WMI 疑難排解](/windows/win32/wmisdk/wmi-troubleshooting)
* [針對 WMI 腳本和 WMI 服務的問題進行疑難排解](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>不支援的作業系統

失敗的另一個常見原因是因為不支援的作業系統。 使用支援的作業系統和核心版本，以成功安裝行動服務。 避免使用私用修補程式。

若要查看 Azure Site Recovery 所支援的作業系統和核心版本清單，請參閱 [支援矩陣檔](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>不支援的開機磁片設定 (錯誤識別碼：95309、95310、95311) 

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>開機和系統磁碟分割/磁片區不是相同磁片 (錯誤識別碼： 95309) 

在9.20 版之前，在不同磁片上的開機和系統磁碟分割/磁片區是不受支援的設定。 從 [9.20 版](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)開始，就支援此設定。

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>開機磁片無法使用 (錯誤識別碼： 95310) 

無法保護不含開機磁片的虛擬機器。 開機磁片可確保在容錯移轉作業期間順暢地復原虛擬機器。 沒有開機磁片會導致在容錯移轉之後啟動電腦失敗。 請確認虛擬機器包含開機磁片，然後重試此操作。 此外，不支援在同一部電腦上有多個開機磁片。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>來源電腦上有多個開機磁片 (錯誤識別碼： 95311) 

具有多個開機磁片的虛擬機器不是 [支援](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)的設定。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>多個磁片上的系統磁碟分割 (錯誤識別碼： 95313) 

在9.20 版之前，在多個磁片上的根磁碟分割或磁片區設定是不支援的設定。 從 [9.20 版](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)開始，就支援此設定。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>啟用保護失敗，因為在 GRUB 設定中提及的裝置名稱，而不是 UUID (錯誤識別碼： 95320) 

### <a name="possible-cause"></a>可能的原因

 (_/boot/grub/menu.lst_、 _/boot/grub/grub.cfg_、 _/boot/grub2/grub.cfg_或) _/etc/default/grub_ 的 (GRUB) 設定檔的「統一開機載入器」，可能會包含參數 **根目錄** 的值，並以實際的裝置名稱（而不是通用唯一識別碼 (UUID) ）來 **繼續** 。 Site Recovery 會要求 UUID 方法，因為裝置名稱可能會在 VM 重新開機時變更。 例如，VM 可能不會在容錯移轉時以相同名稱上線，並導致問題。

例如：

- 以下是來自 GRUB 檔案 _/boot/grub2/grub.cfg_的行：

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 以下是來自 GRUB 檔案 _/boot/grub/menu.lst_的行：

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB 行包含參數 **根目錄** 和 **繼續** 的實際裝置名稱，而不是 UUID。

### <a name="how-to-fix"></a>如何修正

裝置名稱應該取代為對應的 UUID。

1. 藉由執行命令來尋找裝置的 UUID `blkid \<device name>` 。

   例如：

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 現在以像這樣的格式將裝置名稱取代為其 UUID `root=UUID=\<UUID>` 。 例如，如果我們將 _/boot/grub2/grub.cfg_、 _/boot/grub2/grub.cfg_或 _/etc/default/grub_ 中所述之 root 和 resume 參數的 UUID 取代為裝置名稱，則檔案中的程式程式碼看起來會像下面這一行：

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 重新開機保護。

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>安裝行動服務已完成，但在重新開機時出現警告 (錯誤識別碼： 95265 & 95266) 

Site Recovery 行動服務有許多元件，其中一個稱為篩選器驅動程式。 篩選器驅動程式只會在系統重新開機時載入系統記憶體中。 只有在系統重新開機時載入新的篩選器驅動程式時，才能實現篩選器驅動程式修正。

> [!IMPORTANT]
> 這是警告，即使在新的代理程式更新之後，現有的複寫仍可正常運作。 您隨時都可以選擇重新開機，以取得新篩選器驅動程式的優點，但如果您不重新開機，舊的篩選器驅動程式會繼續運作。 因此，在沒有重新開機的更新之後（除了篩選器驅動程式之外）， **行動服務中的其他增強功能和修正的優點也會被實現**。 雖然建議，但不一定要在每次升級之後重新開機。 如需何時需要重新開機的詳細資訊，請在 Azure Site Recovery 中的服務更新行動服務升級] 區段之後，設定 [重新開機](service-updates-how-to.md#reboot-after-mobility-service-upgrade) 。

> [!TIP]
>如需在維護期間排程升級的最佳作法，請參閱 Azure Site Recovery 中的服務更新中 [最新的作業系統/核心支援](service-updates-how-to.md#support-for-latest-operating-systemskernels) 。

## <a name="lvm-support-from-920-version"></a>從 9.20 版開始支援 LVM

在9.20 版之前，只支援資料磁片的邏輯磁片區管理員 (LVM) 。 `/boot`磁碟分割應位於磁碟分割，而不是 LVM 磁片區。

從 [9.20 版](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)開始，可支援 [LVM 上的 OS 磁片](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 。

## <a name="insufficient-space-errorid-95524"></a>空間不足 (錯誤識別碼： 95524) 

將行動代理程式複製到來源機器時，至少需要 100 MB 的可用空間。 請確認您的來源電腦具有所需的可用空間量，然後重試此作業。

## <a name="low-system-resources"></a>系統資源不足

此問題可能出現的錯誤識別碼為95572和95573。 當系統的可用記憶體不足，而且無法配置用於行動服務安裝的記憶體時，就會發生此問題。 確定已釋出足夠的記憶體，讓安裝順利完成。

## <a name="vss-installation-failures"></a>VSS 安裝失敗

磁片區陰影複製服務 (VSS) 安裝是行動代理程式安裝的一部分。 此服務會在程式中用來產生應用程式一致的復原點。 在 VSS 安裝期間發生失敗的原因有許多種。 若要識別確切的錯誤，請參閱 _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_。 下一節會反白顯示一些常見的錯誤和解決步驟。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 錯誤 -2147023170 [0x800706BE] - 結束代碼 511

當防毒軟體封鎖 Azure Site Recovery 服務的作業時，最常看到此問題。

若要解決此問題：

1. 檢查 [防毒程式的資料夾排除](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)清單。
1. 遵循防毒軟體提供者所發佈的指導方針，解除封鎖 Windows 中 DLL 的註冊。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 錯誤 7 [0x7] - 結束代碼 511

此錯誤是因為記憶體不足，無法安裝 VSS 所造成的執行階段錯誤。 增加磁碟空間，以順利完成此操作。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 錯誤 -2147023824 [0x80070430] - 結束代碼 517

此錯誤會在 Azure Site Recovery VSS 提供者服務[被標記為要進行刪除](/previous-versions/ms838153(v=msdn.10)) \(英文\) 的情況下發生。 請嘗試執行下列命令，以在來源電腦上手動安裝 VSS：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 錯誤 -2147023841 [0x8007041F] - 結束代碼 512

當 Azure Site Recovery VSS 提供者服務資料庫已 [鎖定](/previous-versions/ms833798(v=msdn.10))時，就會發生此錯誤。嘗試從命令提示字元執行下列命令，以在來源電腦上手動安裝 VSS：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

當發生失敗時，請檢查是否有任何防毒程式或其他服務停滯于 **啟動** 狀態。 處於 **啟動** 狀態的進程可以保留資料庫服務的鎖定。 這會導致安裝 VSS 提供者失敗。 請確認沒有任何服務處於 **啟動** 狀態，然後重試上述操作。

### <a name="vss-exit-code-806"></a>VSS 結束代碼 806

如果用於安裝的使用者帳戶沒有執行命令的許可權，就會發生此錯誤 `CSScript` 。 請為該使用者帳戶提供必要權限以執行指令碼，並重試該作業。

### <a name="other-vss-errors"></a>其他 VSS 錯誤

嘗試從命令提示字元執行下列命令，以在來源電腦上手動安裝 VSS 提供者服務：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS 錯誤-0x8004E00F

此錯誤通常發生在安裝行動代理程式期間，因為和中的問題處於 `DCOM` `DCOM` 重大狀態。

使用下列程式來判斷錯誤的原因。

### <a name="examine-the-installation-logs"></a>檢查安裝記錄檔

1. 開啟位於 _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_的安裝記錄檔。
2. 出現下列錯誤表示此問題：

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

若要解決問題：

請洽詢 [Microsoft Windows 平臺小組](https://aka.ms/Windows_Support) ，以取得解決 DCOM 問題的協助。

當 DCOM 問題解決時，請從命令提示字元使用下列命令，手動重新安裝 Azure Site Recovery VSS 提供者：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

如果應用程式一致性對您的嚴重損壞修復需求並不重要，您可以略過 VSS 提供者安裝。

若要略過 Azure Site Recovery VSS 提供者安裝，並在安裝後手動安裝 Azure Site Recovery VSS 提供者：

1. 安裝行動服務。 安裝會在下列步驟失敗： **安裝後**設定。
1. 若要略過 VSS 安裝：
   1. 開啟位於下列位置的 Azure Site Recovery 行動服務安裝目錄：

      _C:\Program Files (x86) \Microsoft Azure Site Recovery\agent_

   1. 新增下列幾行，以修改 Azure Site Recovery VSS 提供者安裝腳本 _InMageVSSProvider_Install_ 和 _InMageVSSProvider_Uninstall .cmd_ 永遠成功：

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. 手動安裝行動代理程式。
1. 當安裝成功並移至下一個步驟時，請 **設定**，移除您新增的行。
1. 若要安裝 VSS 提供者，請以系統管理員身分開啟命令提示字元，然後執行下列命令：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. 確認 Azure Site Recovery VSS 提供者已安裝為 Windows 服務中的服務。 開啟 [元件服務] MMC，確認已列出 VSS 提供者。
1. 如果 VSS 提供者安裝持續失敗，請與技術支援人員合作，以解決密碼編譯應用程式設計介面中的許可權錯誤 (CAPI2) 。

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>因為在非叢集電腦上啟用叢集服務，所以 VSS 提供者安裝失敗

此問題會導致 Azure Site Recovery 行動代理程式安裝在 Azure Site Recovery VSS 提供者安裝期間失敗。 失敗是因為發生的問題 `COM+` 會導致 VSS 提供者無法安裝。

### <a name="to-identify-the-issue"></a>找出問題

在位於 _ \<date-time> UA_InstallLogFile C:\ProgramData\ASRSetupLogs\UploadedLogs_ 的設定伺服器上的記錄檔中，您會發現下列例外狀況：

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

若要解決問題：

1. 請確認這部電腦是不是叢集的電腦，而且不會使用叢集元件。
1. 如果未使用元件，請從電腦移除叢集元件。

## <a name="drivers-are-missing-on-the-source-server"></a>來源伺服器上遺漏驅動程式

如果行動代理程式安裝失敗，請檢查 _C:\ProgramData\ASRSetupLogs_ 底下的記錄，以判斷某些控制組中是否遺漏了某些必要的驅動程式。

若要解決問題：

1. 使用登錄編輯程式（例如 `regedit.msc` ），開啟登錄。
1. 開啟 `HKEY_LOCAL_MACHINE\SYSTEM` 節點。
1. 在 `SYSTEM` 節點中，找出控制項集。
1. 開啟每個控制項集，並確認下列 Windows 驅動程式存在：

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. 重新安裝任何遺失的驅動程式。

## <a name="next-steps"></a>接下來的步驟

[深入瞭解](vmware-azure-tutorial.md) 如何設定 VMware vm 的嚴重損壞修復。
