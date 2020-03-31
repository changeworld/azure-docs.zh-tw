---
title: 使用 Azure 網站恢復管理 VMware/物理伺服器的移動代理
description: 使用 Azure 網站恢復服務管理移動服務代理，以便將 VMware VM 和物理伺服器的災害復原到 Azure。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256961"
---
# <a name="manage-the-mobility-agent"></a>管理行動代理程式 

使用 Azure 網站恢復將 VMware VM 和物理伺服器的災害復原設置為 Azure 時，在伺服器上設置移動代理。 移動代理協調受保護電腦、佈建服務器/橫向擴展進程伺服器之間的通信，並管理資料複製。 本文總結了部署移動代理後管理移動代理的常見任務。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>從 Azure 門戶更新移動服務

1. 在開始之前，請先確定已更新設定伺服器、向外延展處理伺服器，以及要一起部署的所有主要目標伺服器，然後才更新受保護機器上的行動服務。
2. 在入口網站中，開啟保存庫 > [複寫的項目]****。
3. 如果設定伺服器已是最新版本，您會看到一則通知，指出「有新的 Site Recovery 複寫代理程式更新可用。 按一下以安裝」的通知。

     ![[複寫的項目] 視窗](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 按一下通知，然後在 [代理程式更新]**** 中，選取您想要升級行動服務的機器。 然後按一下 **[確定]**。

     ![複寫的項目 VM 清單](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 「更新行動服務」作業會針對每個選取的機器啟動。

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>通過 Windows 伺服器上的電源外殼腳本更新移動服務

在開始之前，請先確定已更新設定伺服器、向外延展處理伺服器，以及要一起部署的所有主要目標伺服器，然後才更新受保護機器上的行動服務。

使用以下腳本通過電源外殼 Cmdlet 升級伺服器上的移動服務

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>在每個受保護的伺服器上手動更新移動服務

1. 在開始之前，請先確定已更新設定伺服器、向外延展處理伺服器，以及要一起部署的所有主要目標伺服器，然後才更新受保護機器上的行動服務。

2. 根據伺服器的作業系統[查找代理安裝程式](vmware-physical-mobility-service-overview.md#locate-installer-files)。

>[!IMPORTANT]
> 如果要將 Azure IaaS VM 從一個 Azure 區域複製到另一個 Azure 區域，請不要使用此方法。 有關所有可用選項的資訊，請參閱[我們的指南](azure-to-azure-autoupdate.md)。

3. 將安裝檔案複製到受保護的電腦上，然後運行該檔以更新移動代理。

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>用於推送移動服務安裝的更新帳戶

在部署 Site Recovery 時，為了啟用行動服務推入安裝，您已指定在已為機器啟用複寫的情況下，Site Recovery 處理伺服器用來存取機器並安裝服務的帳戶。 如果您想要更新此帳戶的認證，請依照[這些指示](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)進行操作。

## <a name="uninstall-mobility-service"></a>卸載移動服務

### <a name="on-a-windows-machine"></a>在 Windows 機器上

從 UI 或命令提示字元解除安裝。

- **從 UI**：在機器的 [控制台] 中，選取 [程式集]****。 選擇**微軟 Azure 網站恢復移動服務/主目標伺服器** > **卸載**。
- **從命令提示字元**：以機器上的系統管理員身分開啟命令提示字元視窗。 執行以下命令： 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>在 Linux 機器上
1. 在 Linux 機器上，以 **root** 使用者身分登入。
2. 在終端中，轉到 /usr/local/ASR。
3. 執行以下命令：
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>在源電腦上安裝網站恢復 VSS 提供程式

源電腦上需要 Azure 網站恢復 VSS 提供程式才能生成應用程式一致性點。 如果提供程式的安裝無法通過推送安裝成功，請按照以下給定的準則手動安裝它。

1. 打開管理釐米視窗。
2. 導航到移動服務安裝位置。 （Eg - C：程式檔 （x86）\微軟 Azure 網站恢復\代理）
3. 運行腳本InMageVSSProvider_Uninstall.cmd 。 如果服務已存在，這將卸載該服務。
4. 運行腳本 InMageVSSProvider_Install.cmd 以手動安裝 VSS 提供程式。

## <a name="next-steps"></a>後續步驟

- [為 VMware VM 設置災害復原](vmware-azure-tutorial.md)
- [為實體伺服器設定災害復原](physical-azure-disaster-recovery.md)
