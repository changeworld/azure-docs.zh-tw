---
title: 在 Azure 中建立及上傳 Ubuntu Linux VHD
description: 了解如何建立及上傳包含 Ubuntu Linux 作業系統的 Azure 虛擬硬碟 (VHD)。
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: abd357808cd0213e92eaba478fb861110bcf9f39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84666718"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>準備適用於 Azure 的 Ubuntu 虛擬機器


Ubuntu 現在會在發佈正式的 Azure Vhd 以供下載 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) 。 如果您需要針對 Azure 建置您專用的 Ubuntu 映像，而不使用下面的手動程序，建議您視需要從使用這些已知可運作的 VHD 並加以自訂來開始建置。 最新的映像版本一律可以在下列位置找到︰

* Ubuntu 16.04/Xenial： [ubuntu-16.04-server-cloudimg-amd64-disk1 .vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic： [Bionic-server-cloudimg-amd64](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>必要條件
本文假設您已將 Ubuntu Linux 作業系統安裝到虛擬硬碟。 有多個工具可用來建立 .vhd 檔案，例如，像是 Hyper-V 的虛擬化解決方案。 如需指示，請參閱 [安裝 Hyper-V 角色及設定虛擬機器](https://technet.microsoft.com/library/hh846766.aspx)。

**Ubuntu 安裝注意事項**

* 如需有關準備 Azure 之 Linux 的更多秘訣，另請參閱 [一般 Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes) 。
* Azure 不支援 VHDX 格式，只支援 **固定 VHD**。  您可以使用 Hyper-v 管理員或 Cmdlet，將磁片轉換成 VHD 格式 `Convert-VHD` 。
* 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。 這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。 如果願意，您可以在資料磁碟上使用 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 請不要在 OS 磁片上設定交換磁碟分割或 cloud-init。 您可以設定雲端初始化布建代理程式，以在暫存資源磁片上建立交換檔或交換磁碟分割。 您可以在以下步驟中找到與此有關的詳細資訊。
* Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從未經處理的磁碟轉換成 VHD 時，您必須確定未經處理的磁碟大小在轉換前是 1 MB 的倍數。 如需詳細資訊，請參閱 [Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)。

## <a name="manual-steps"></a>手動步驟
> [!NOTE]
> 在嘗試為 Azure 建立您自己的自訂 Ubuntu 映射之前，請考慮改為使用預先建立和測試的映射 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) 。
> 
> 

1. 在 Hyper-V 管理員的中間窗格中，選取虛擬機器。

2. 按一下 **[連接]** ，以開啟虛擬機器的視窗。

3. 取代映射中的目前儲存機制，以使用 Ubuntu 的 Azure 存放庫。
   
    建議您在編輯 `/etc/apt/sources.list` 之前先進行備份：
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 16.04 和 Ubuntu 18.04：
   
        # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
        # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
        # sudo apt-get update


4. Ubuntu Azure 映射現在會使用[Azure 量身打造的核心](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)。 執行下列命令，將作業系統更新為最新的 Azure 量身打造的核心，並安裝 Azure Linux 工具（包括 Hyper-v 相依性）：

    Ubuntu 16.04 和 Ubuntu 18.04：

        # sudo apt update
        # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
        (recommended) # sudo apt full-upgrade

        # sudo reboot

5. 修改 Grub 的核心開機程式行，使其額外包含用於 Azure 的核心參數。 若要這樣做，請在文字編輯器中開啟 `/etc/default/grub`，找到名為 `GRUB_CMDLINE_LINUX_DEFAULT` 的變數 (或視需要加入它)，然後進行編輯以使其包含以下參數：
   
    ```
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    儲存並關閉此檔案，然後執行 `sudo update-grub`。 這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 技術支援團隊進行問題偵錯程序。

6. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。  這通常是預設值。

7. 安裝 cloud-init （布建代理程式）和 Azure Linux 代理程式（來賓延伸模組處理常式）。 Cloud-init 會使用 netplan，在布建期間設定系統網路設定，以及每次後續開機。

        # sudo apt update
        # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent

   > [!Note]
   >  若已安裝 `NetworkManager` 和 `NetworkManager-gnome` 套件，則 `walinuxagent` 套件可能會將它們移除。

8. 移除雲端 init 預設的配置，以及可能與 Azure 上的雲端初始化布建衝突的剩餘 netplan 成品：

        # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
        # rm -f /etc/cloud/ds-identify.cfg
        # rm -f /etc/netplan/*.yaml

9. 使用 Azure 資料來源設定雲端 init 來布建系統：

    ```
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. 將 Azure Linux 代理程式設定為依賴雲端 init 來執行布建。 如需這些選項的詳細資訊，請參閱[WALinuxAgent 專案](https://github.com/Azure/WALinuxAgent)。

        sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
        sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
        sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
        sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

        cat >> /etc/waagent.conf << EOF
        # For Azure Linux agent version >= 2.2.45, this is the option to configure,
        # enable, or disable the provisioning behavior of the Linux agent.
        # Accepted values are auto (default), waagent, cloud-init, or disabled.
        # A value of auto means that the agent will rely on cloud-init to handle
        # provisioning if it is installed and enabled, which in this case it will.
        Provisioning.Agent=auto
        EOF

11. 清理雲端 init 和 Azure Linux 代理程式執行時間成品和記錄：

        # sudo cloud-init clean --logs --seed
        # sudo rm -rf /var/lib/cloud/
        # sudo systemctl stop walinuxagent.service
        # sudo rm -rf /var/lib/waagent/
        # sudo rm -f /var/log/waagent.log

12. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    > [!NOTE]
    > 此 `sudo waagent -force -deprovision+user` 命令將會嘗試清理系統，使其適合重新布建。 `+user`選項會刪除最後布建的使用者帳戶和相關聯的資料。

    > [!WARNING]
    > 使用上述命令取消布建，並不保證會清除所有敏感性資訊的影像，而且適合轉散發。

        # sudo waagent -force -deprovision+user
        # rm -f ~/.bash_history
        # export HISTSIZE=0
        # logout

13. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。

14. Azure 只接受固定大小的 Vhd。 如果 VM 的 OS 磁片不是固定大小的 VHD，請使用 `Convert-VHD` PowerShell Cmdlet 並指定 `-VHDType Fixed` 選項。 請參閱這裡的檔 `Convert-VHD` ：[轉換-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd?view=win10-ps)。


## <a name="next-steps"></a>後續步驟
您現在可以開始使用您的 Ubuntu Linux 虛擬硬碟在 Azure 建立新的虛擬機器。 如果您是第一次將 .vhd 檔案上傳至 Azure，請參閱[從自訂磁碟建立 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。

