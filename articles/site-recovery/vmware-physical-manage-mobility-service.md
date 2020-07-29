---
title: 使用 Azure Site Recovery 管理 VMware/實體伺服器的行動代理程式
description: 使用 Azure Site Recovery 服務管理行動服務代理程式，以進行 VMware Vm 和實體伺服器至 Azure 的嚴重損壞修復。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79256961"
---
# <a name="manage-the-mobility-agent"></a>管理行動代理程式 

當您使用 Azure Site Recovery 進行 VMware Vm 和實體伺服器至 Azure 的嚴重損壞修復時，您會在伺服器上設定行動代理程式。 行動代理程式會協調受保護機器、設定伺服器/相應放大進程伺服器和管理資料複寫之間的通訊。 本文摘要說明在部署行動代理程式之後管理其一般工作。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>更新 Azure 入口網站的行動服務

1. 在開始之前，請先確定已更新設定伺服器、向外延展處理伺服器，以及要一起部署的所有主要目標伺服器，然後才更新受保護機器上的行動服務。
2. 在入口網站中，開啟保存庫 > [複寫的項目]****。
3. 如果設定伺服器已是最新版本，您會看到一則通知，指出「有新的 Site Recovery 複寫代理程式更新可用。 按一下以安裝」的通知。

     ![[複寫的項目] 視窗](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 按一下通知，然後在 [代理程式更新]**** 中，選取您想要升級行動服務的機器。 然後按一下 [確定] 。

     ![複寫的項目 VM 清單](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 「更新行動服務」作業會針對每個選取的機器啟動。

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>透過 Windows server 上的 powershell 腳本更新行動服務

在開始之前，請先確定已更新設定伺服器、向外延展處理伺服器，以及要一起部署的所有主要目標伺服器，然後才更新受保護機器上的行動服務。

使用下列腳本，透過 power shell Cmdlet 升級伺服器上的行動服務

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>在每部受保護的伺服器上手動更新行動服務

1. 在開始之前，請先確定已更新設定伺服器、向外延展處理伺服器，以及要一起部署的所有主要目標伺服器，然後才更新受保護機器上的行動服務。

2. 根據伺服器的作業系統，[尋找代理程式安裝程式](vmware-physical-mobility-service-overview.md#locate-installer-files)。

>[!IMPORTANT]
> 如果您要將 Azure IaaS VM 從一個 Azure 區域複寫到另一個，請不要使用此方法。 如需所有可用選項的相關資訊，請參閱[我們的指導](azure-to-azure-autoupdate.md)方針。

3. 將安裝檔案複製到受保護的電腦上，然後執行它來更新行動代理程式。

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>行動服務的推入安裝所使用的更新帳戶

在部署 Site Recovery 時，為了啟用行動服務推入安裝，您已指定在已為機器啟用複寫的情況下，Site Recovery 處理伺服器用來存取機器並安裝服務的帳戶。 如果您想要更新此帳戶的認證，請依照[這些指示](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)進行操作。

## <a name="uninstall-mobility-service"></a>卸載行動服務

### <a name="on-a-windows-machine"></a>在 Windows 機器上

從 UI 或命令提示字元解除安裝。

- **從 UI**：在機器的 [控制台] 中，選取 [程式集]****。 選取 [ **Microsoft Azure Site Recovery 行動服務/主要目標伺服器**] [  >  **卸載**]。
- **從命令提示字元**：以機器上的系統管理員身分開啟命令提示字元視窗。 執行下列命令： 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>在 Linux 機器上
1. 在 Linux 機器上，以 **root** 使用者身分登入。
2. 在終端機中，移至/Usr/local/asr。
3. 執行下列命令：
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>在來源電腦上安裝 Site Recovery VSS 提供者

來源電腦上需要 Azure Site Recovery VSS 提供者，才能產生應用程式一致性點。 如果安裝程式未透過推入安裝成功，請遵循下列指定的指導方針手動安裝。

1. 開啟 [管理] cmd 視窗。
2. 流覽至行動服務安裝位置。 （例如 C:\Program Files （x86） \Microsoft Azure Site Recovery\agent）
3. 執行腳本 InMageVSSProvider_Uninstall .cmd。 這會卸載服務（如果已存在）。
4. 執行腳本 InMageVSSProvider_Install .cmd，以手動方式安裝 VSS 提供者。

## <a name="next-steps"></a>後續步驟

- [設定 VMware Vm 的嚴重損壞修復](vmware-azure-tutorial.md)
- [為實體伺服器設定災害復原](physical-azure-disaster-recovery.md)
