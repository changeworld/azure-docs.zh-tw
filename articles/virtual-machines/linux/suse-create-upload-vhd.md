---
title: 在 Azure 中建立及上傳 SUSE Linux VHD
description: 了解如何建立及上傳包含 SUSE Linux 作業系統的 Azure 虛擬硬碟 (VHD)。
author: gbowerman
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: 1f35adcc797e903bb44852e9ba52e1a023f51a0d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659517"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>準備適用於 Azure 的 SLES 或 openSUSE 虛擬機器


本文假設您已將 SUSE 或 openSUSE Linux 作業系統安裝到虛擬硬碟。 有多個工具可用來建立 .vhd 檔案，例如，像是 Hyper-V 的虛擬化解決方案。 如需指示，請參閱 [安裝 Hyper-V 角色及設定虛擬機器](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))。

## <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE 安裝注意事項
* 如需有關準備 Azure 之 Linux 的更多秘訣，另請參閱 [一般 Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes) 。
* Azure 不支援 VHDX 格式，只支援 **固定 VHD**。  您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。
* 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。 這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。 如果願意，您可以在資料磁碟上使用 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 請勿在作業系統磁碟上設定交換磁碟分割。 您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。  您可以在以下步驟中找到與此有關的詳細資訊。
* Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從未經處理的磁碟轉換成 VHD 時，您必須確定未經處理的磁碟大小在轉換前是 1 MB 的倍數。 如需詳細資訊，請參閱 [Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)。

## <a name="use-suse-studio"></a>使用 SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) 可讓您輕鬆建立及管理 Azure 和 Hyper-V 的 SLES 與 openSUSE 映像。 這是建議用來自訂您自己的 SLES 和 openSUSE 映像的方法。

SUSE 是建置您自己的 VHD 的替代選項，其也可在 [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)發佈 SLES 的 BYOS (自備訂用帳戶，Bring Your Own Subscription) 映像。

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>準備 Azure 的 SUSE Linux Enterprise Server
1. 在 Hyper-V 管理員的中間窗格中，選取虛擬機器。
2. 按一下 **[連接]** ，以開啟虛擬機器的視窗。
3. 註冊您的 SUSE Linux Enterprise 系統，以允許下載更新並安裝封裝。
4. 為系統更新最新的修補程式：

    ```console
    # sudo zypper update
    ```
    
5. 安裝 Azure Linux 代理程式和雲端初始化

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. 啟用 waagent & 雲端初始化以在開機時啟動

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. 更新 waagent 和雲端初始設定

    ```console
    # sudo sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sudo sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. 編輯/etc/default/grub 檔案，以確保主控台記錄會傳送到序列埠，然後以 grub2-grub2-mkconfig-o/boot/grub2/grub.cfg 來更新主要設定檔

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。
    
9. 確定/etc/fstab 檔案使用其 UUID (（uuid）參考磁片) 
         
10. 修改 udev 角色可防止產生乙太網路介面的靜態規則。 在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則可能會造成問題：

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. 建議您編輯檔案 "/etc/sysconfig/network/dhcp"，並將 `DHCLIENT_SET_HOSTNAME` 參數變更如下：

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. 在 "/etc/sudoers" 中，註解化或移除下列程式碼行 (如果存在的話)：

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。 這通常是預設值。

14. 請勿在作業系統磁碟上建立交換空間。
    
    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。 請注意，資源磁碟是 *暫存* 磁碟，可能會在 VM 取消佈建時清空。 安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

15. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```
16. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。 您現在可以將 Linux VHD 上傳至 Azure。

---
## <a name="prepare-opensuse-131"></a>準備執行 openSUSE 13.1+
1. 在 Hyper-V 管理員的中間窗格中，選取虛擬機器。
2. 按一下 **[連接]** ，以開啟虛擬機器的視窗。
3. 在 Shell 上執行命令 '`zypper lr`'。 如果此命令傳回的輸出與下列類似，則該儲存機制已如預期設定，不需進行調整 (請注意，版本號碼可能會不同)：

   | # | Alias                 | 名稱                  | 啟用 | 重新整理
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | 雲端： Tools_13 1      | 雲端： Tools_13 1      | 是     | 是
   | 2 | openSUSE_13 openSUSE_13.1_OSS     | openSUSE_13 openSUSE_13.1_OSS     | 是     | 是
   | 3 | openSUSE_13 openSUSE_13.1_Updates | openSUSE_13 openSUSE_13.1_Updates | 是     | 是

    如果命令傳回 "No repositories defined..."，則使用下列命令來新增這些儲存機制：

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    您接著可以重新執行命令 '`zypper lr`' 來驗證已新增的儲存機制。 如果有其中一個相關的更新儲存機制未啟用，請使用下列命令加以啟用：

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. 將核心更新為最新的可用版本：

    ```console
    # sudo zypper up kernel-default
    ```

    或使用所有最新的修補程式來更新系統：

    ```console
    # sudo zypper update
    ```

5. 安裝 Azure Linux 代理程式。

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 作法是，在文字編輯器中開啟 "/boot/grub/menu.lst"，並確定預設核心包含下列參數：

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。 此外，請從核心開機程式行中移除以下參數 (如果有)：

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. 建議您編輯檔案 "/etc/sysconfig/network/dhcp"，並將 `DHCLIENT_SET_HOSTNAME` 參數變更如下：

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **重要：** 在 "/etc/sudoers" 中，註解化或移除下列程式碼行 (如果存在的話)：

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。  這通常是預設值。
10. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。 請注意，資源磁碟是 *暫存* 磁碟，可能會在 VM 取消佈建時清空。 安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. 確定 Azure Linux 代理程式會在啟動時執行：

    ```console
    # sudo systemctl enable waagent.service
    ```

13. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。 您現在可以將 Linux VHD 上傳至 Azure。

## <a name="next-steps"></a>後續步驟
您現在可以開始使用您的 SUSE Linux 虛擬硬碟在 Azure 建立新的虛擬機器。 如果您是第一次將 .vhd 檔案上傳至 Azure，請參閱[從自訂磁碟建立 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。
