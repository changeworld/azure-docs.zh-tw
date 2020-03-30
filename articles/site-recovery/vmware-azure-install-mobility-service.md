---
title: 通過推送安裝來準備源電腦以安裝移動服務，以便 VMware VM 和物理伺服器的災害復原到 Azure |微軟文檔
description: 瞭解如何準備伺服器安裝移動代理，通過推送安裝 VMware VM 和物理伺服器的災害復原到 Azure 使用 Azure 網站恢復服務。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186459"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>準備源機以推送安裝移動代理

當您使用 [Azure Site Recovery](site-recovery-overview.md) 為 VMware VM 和實體伺服器設定災害復原時，會在每個內部部署 VMware VM 和實體伺服器上安裝 [Site Recovery 行動服務](vmware-physical-mobility-service-overview.md)。  行動服務會擷取機器上的資料寫入，然後將它們轉送給 Site Recovery 處理伺服器。

## <a name="install-on-windows-machine"></a>在 Windows 機器上安裝

在您想要保護的每部 Windows 機器上，執行下列操作：

1. 確定機器與處理伺服器之間有網路連線。 如果您尚未設定個別的處理伺服器，則預設會在設定伺服器上執行。
1. 建立可供處理序伺服器存取電腦的帳戶。 帳戶應該具有本機或網域系統管理員權限。 此帳戶僅適用於推送安裝和代理程式更新。
2. 如果您未使用網域帳戶，請依照下列方式，停用本機電腦上的「遠端使用者存取控制」：
    - 在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 登錄機碼底下，新增 DWORD：**LocalAccountTokenFilterPolicy**。 將值設定為 **1**。
    -  做法是在命令提示字元執行下列命令︰  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. 在您想要保護之機器的 [Windows 防火牆] 中，選取 [允許應用程式或功能通過防火牆]****。 啟用 [檔案及印表機共用]**** 和 [Windows Management Instrumentation (WMI)]****。 針對隸屬於網域的電腦，您可以利用群組原則物件 (GPO) 進行防火牆設定。

   ![防火牆設定](./media/vmware-azure-install-mobility-service/mobility1.png)

4. 新增您在 CSPSConfigtool 中建立的帳戶。 若要這樣做，請登入您的設定伺服器。
5. 開啟 **cspsconfigtool.exe**。 它在桌面和 %程式資料%_ASR_home_svsystems_bin 資料夾中作為快捷方式提供。
6. 在 [管理帳戶]**** 索引標籤上，選取 [新增帳戶]****。
7. 加入您所建立的帳戶。
8. 輸入您為電腦啟用複寫時使用的認證。

## <a name="install-on-linux-machine"></a>在 Linux 機器上安裝

在您想要保護的每部 Linux 機器上，執行下列操作：

1. 確定 Linux 機器與處理伺服器之間有網路連線。
2. 建立可供處理序伺服器存取電腦的帳戶。 帳戶應該是來源 Linux 伺服器上的**根**使用者。 此帳戶僅適用於推送安裝和更新。
3. 檢查來源 Linux 伺服器上的 /etc/hosts 檔案，該檔案具有將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。
4. 在您要複寫的電腦上安裝最新的 openssh、openssh-server 和 openssl 套件。
5. 請確定安全殼層 (SSH) 已啟用且正在連接埠 22 上執行。
4. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證。 為此，請以**root**登錄。
5. 在 **/etc/ssh/sshd_config**檔中，查找以**密碼身份驗證**開頭的行。
6. 取消注釋行，並將值更改為 **"是**"。
7. 查找以**子系統**開頭的行，然後取消注釋該行。

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. 重新開機**sshd**服務。
9. 新增您在 CSPSConfigtool 中建立的帳戶。 若要這樣做，請登入您的設定伺服器。
10. 開啟 **cspsconfigtool.exe**。 它會是桌面上可用的捷徑，位於 %ProgramData%\home\svsystems\bin 資料夾中。
11. 在 [管理帳戶]**** 索引標籤上，選取 [新增帳戶]****。
12. 加入您所建立的帳戶。
13. 輸入您為電腦啟用複寫時使用的認證。

## <a name="anti-virus-on-replicated-machines"></a>在複寫的機器上防毒

如果您要複寫的機器正在執行作用中的防毒軟體，請務必從防毒作業中排除行動性服務安裝資料夾 (C:\ProgramData\ASR\agent**)。 這可確保複寫運作正常。

## <a name="next-steps"></a>後續步驟

安裝「行動服務」之後，在 Azure 入口網站中，選取 [+ 複寫]**** 以開始保護這些 VM。 詳細瞭解如何為[VMware VM](vmware-azure-enable-replication.md)和[物理伺服器](physical-azure-disaster-recovery.md#enable-replication)啟用複製。


