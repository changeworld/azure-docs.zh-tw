---
title: 建立及上傳 Red Hat Enterprise Linux VHD 以在 Azure 中使用
description: 了解如何建立及上傳包含 Red Hat Linux 作業系統的 Azure 虛擬硬碟 (VHD)。
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: d5caacc7ebbb39a5d6d4fa3d4e9757e8e83420f9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202686"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>準備適用於 Azure 的 Red Hat 型虛擬機器
在本文中，您將學習如何準備 Red Hat Enterprise Linux (RHEL) 虛擬機器以在 Azure 中使用。 本文涵蓋的 RHEL 版本為 6.7 和 7.1。 本文章所述之準備作業使用 Hyper-V、核心為基礎之虛擬機器 (KVM) 及 VMware 等 Hypervisor。 如需參加 Red Hat 雲端存取方案之資格需求的詳細資訊，請參閱 [Red Hat 雲端存取網站](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)與[在 Azure 上執行 RHEL](https://access.redhat.com/ecosystem/ccsp/microsoft-azure)。 如需自動建立 RHEL 映射的方式，請參閱 [Azure 映射](./image-builder-overview.md)產生器。

## <a name="hyper-v-manager"></a>Hyper-V 管理員

本節說明如何使用 Hyper-v 管理員準備 [rhel 6](#rhel-6-using-hyper-v-manager) 或 [rhel 7](#rhel-7-using-hyper-v-manager) 虛擬機器。

### <a name="prerequisites"></a>先決條件
本節假設您已經從 Red Hat 網站取得 ISO 檔案並將 RHEL 映像安裝至虛擬硬碟 (VHD)。 如需有關如何使用 Hyper-V 管理員來安裝作業系統映像的詳細資訊，請參閱[安裝 Hyper-V 角色和設定虛擬機器](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))。

**RHEL 安裝注意事項**

* Azure 不支援 VHDX 格式。 Azure 只支援固定 VHD。 您可以使用 Hyper-V 管理員將磁碟轉換為 VHD 格式，或者使用 convert-vhd Cmdlet。 如果您使用 VirtualBox，請選取 [固定大小]  ，而不是預設在建立磁碟時動態配置的選項。
* Azure 支援 Gen1 (BIOS 開機) & Gen2 (UEFI 開機) 虛擬機器。
* 允許的 VHD 大小上限為 1,023 GB。
* 邏輯磁碟區管理員 (LVM) 受到支援，而且可能會在 OS 磁碟或 Azure 虛擬機器中的資料磁碟上使用。 不過，通常建議在 OS 磁碟上使用標準磁碟分割，而不是 LVM。 此練習可避免 LVM 名稱與複製的虛擬機器發生衝突，特別是為了疑難排解而需要將作業系統磁碟連結至另一部相同虛擬機器時。 另請參閱 [LVM](configure-lvm.md) 和 [RAID](configure-raid.md) 文件。
* **需要 (UDF) 檔案系統裝載國際磁碟格式的核心支援**。 在 Azure 上第一次開機時，連結至客體的 UDF 格式媒體會將佈建組態傳遞至 Linux 虛擬機器。 Azure Linux 代理程式必須能夠掛接 UDF 檔案系統，才能讀取其設定並布建虛擬機器，否則布建將會失敗！
* 請勿在作業系統磁碟上設定交換磁碟分割。 在下列步驟中可以找到與此有關的詳細資訊。

* Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從未經處理的磁碟轉換成 VHD 時，您必須確定未經處理的磁碟大小在轉換前是 1 MB 的倍數。 如需更多詳細資料，請參閱以下步驟。 另請參閱 [Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)以取得詳細資訊。

### <a name="rhel-6-using-hyper-v-manager"></a>使用 Hyper-v 管理員的 RHEL 6

1. 在 Hyper-V 管理員中，選取虛擬機器。

1. 按一下 [連接] ，以開啟虛擬機器的主控台視窗。

1. 在 RHEL 6 中，NetworkManager 可能會對 Azure Linux 代理程式造成干擾。 執行下列命令以將此套件解除安裝：

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. 移動 (或移除) udev 規則可防止產生乙太網路介面的靜態規則。 在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. 若要確保開機時會啟動網路服務，可執行下列命令：

    ```console
    # sudo chkconfig network on
    ```

1. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要執行此修改，請在文字編輯器中開啟 `/boot/grub/menu.lst`，並確定預設核心包含以下參數：

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    這也將確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。
    
    此外，我們還建議您移除下列參數：

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。  您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量。 此組態可能會對小型虛擬機器造成問題。


1. 確定已安裝安全殼層 (SSH) 伺服器，並已設定為在開機時啟動 (這通常為預設值)。 修改 /etc/ssh/sshd_config 以包含下面一行：

    ```config
    ClientAliveInterval 180
    ```

1. 執行以下命令來安裝 Azure Linux 代理程式：

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    如果 NetworkManager 和 NetworkManager-gnome 套件並未在步驟 3 中遭到移除，則在安裝 WALinuxAgent 套件時會將這兩個套件移除。

1. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁片是暫存磁片，如果虛擬機器已取消布建，它可能會清空。 在上一個步驟安裝 Azure Linux 代理程式後，請在 /etc/waagent.conf 中適當修改下列參數：

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. 執行下列命令以取消註冊訂用帳戶 (如有必要)：

    ```console
    # sudo subscription-manager unregister
    ```

1. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. 按一下 [hyper-v 管理員] 中的 [**動作**  >  **關機**]。 您現在可以將 Linux VHD 上傳至 Azure。


### <a name="rhel-7-using-hyper-v-manager"></a>使用 Hyper-v 管理員的 RHEL 7

1. 在 Hyper-V 管理員中，選取虛擬機器。

1. 按一下 [連接] ，以開啟虛擬機器的主控台視窗。

1. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. 若要確保開機時會啟動網路服務，可執行下列命令：

    ```console
    # sudo systemctl enable network
    ```

1. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要執行此修改，請在文字編輯器中開啟 `/etc/default/grub`，然後編輯 `GRUB_CMDLINE_LINUX` 參數。 例如：

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    這也可確保所有的主控台訊息都會傳送到第一個序列埠，並啟用與序列主控台的互動，以協助 Azure 支援有偵錯工具的問題。 此組太也會關閉新的 RHEL 7 對 NIC 的命名慣例。

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

1. 完成 `/etc/default/grub`的編輯之後，請執行下列命令以重建 grub 組態：

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > 如果上傳已啟用 UEFI 的 VM，則更新 grub 的命令為 `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` 。

1. 確定已安裝 SSH 伺服器，並已設定為在開機時啟動 (這通常為預設值)。 修改 `/etc/ssh/sshd_config` 以包含下面一行：

    ```config
    ClientAliveInterval 180
    ```

1. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 執行下列命令來安裝 Azure Linux 代理程式、雲端初始和其他必要的公用程式：

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. 設定雲端初始化以處理布建：

    1. 設定 waagent 以進行雲端初始化：

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > 如果您要遷移特定的虛擬機器，但不想建立一般化映射，請在設定 `Provisioning.Agent=disabled` 時設定 `/etc/waagent.conf` 。
    
    1. 設定裝載：

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. 設定 Azure datasource：

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. 若已設定，請移除現有的 cloud-init：

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. 設定雲端初始記錄：
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. 交換設定不會在作業系統磁片上建立交換空間。

    先前，Azure Linux 代理程式已在 Azure 上布建虛擬機器後，使用連接至虛擬機器的本機資源磁片自動設定交換空間。 不過，這現在由雲端初始處理，您 **不** 能使用 Linux 代理程式來格式化資源磁片建立分頁檔，適當地修改下列參數 `/etc/waagent.conf` ：

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    如果您想要掛接、格式化和建立交換，您可以：
    * 每次建立 VM 時，以雲端初始設定的形式傳遞此設定
    * 在每次建立 VM 時，使用內建至映射的雲端初始指示詞：

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```
1. 如果您要取消註冊訂用帳戶，請執行下列命令：

    ```console
    # sudo subscription-manager unregister
    ```

1. 取消佈建

    執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    > [!CAUTION]
    > 如果您要遷移特定的虛擬機器，但不想建立一般化映射，請略過取消布建步驟。 `waagent -force -deprovision`執行命令會使來源電腦無法使用，此步驟僅供建立一般化映射之用。
    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    

1. 按一下 [hyper-v 管理員] 中的 [**動作**  >  **關機**]。 您現在可以將 Linux VHD 上傳至 Azure。

### <a name="rhel-8-using-hyper-v-manager"></a>使用 Hyper-v 管理員的 RHEL 8

1. 在 Hyper-V 管理員中，選取虛擬機器。

1. 按一下 [連接] ，以開啟虛擬機器的主控台視窗。

1. 藉由執行下列命令，確定網路系統管理員服務會在開機時啟動：

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. 將網路介面設定為在開機時自動啟動，並使用 DHCP：

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 修改 grub 設定中的核心開機行，以包含適用于 Azure 的其他核心參數，並啟用序列主控台。 

    1. 移除目前的 GRUB 參數：
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. `/etc/default/grub`在文字編輯器中編輯，然後新增下列參數：

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   這也可確保所有的主控台訊息都會傳送到第一個序列埠，並啟用與序列主控台的互動，以協助 Azure 支援有偵錯工具的問題。 此組太也會關閉新的 RHEL 7 對 NIC 的命名慣例。
   
   1. 此外，我們建議您移除下列參數：

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

1. 完成 `/etc/default/grub`的編輯之後，請執行下列命令以重建 grub 組態：

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    針對已啟用 UEFI 的 VM，請執行下列命令：

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. 確定已安裝 SSH 伺服器，並已設定為在開機時啟動 (這通常為預設值)。 修改 `/etc/ssh/sshd_config` 以包含下面一行：

    ```config
    ClientAliveInterval 180
    ```

1. 執行下列命令來安裝 Azure Linux 代理程式、雲端初始和其他必要的公用程式：

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. 設定雲端初始化以處理布建：

    1. 設定 waagent 以進行雲端初始化：

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > 如果您要遷移特定的虛擬機器，但不想建立一般化映射，請在設定 `Provisioning.Agent=disabled` 時設定 `/etc/waagent.conf` 。
    
    1. 設定裝載：

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. 設定 Azure datasource：

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. 若已設定，請移除現有的 cloud-init：

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. 設定雲端初始記錄：
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. 交換設定不會在作業系統磁片上建立交換空間。

    先前，Azure Linux 代理程式已在 Azure 上布建虛擬機器後，使用連接至虛擬機器的本機資源磁片自動設定交換空間。 不過，這現在由雲端初始處理，您 **不** 能使用 Linux 代理程式來格式化資源磁片建立分頁檔，適當地修改下列參數 `/etc/waagent.conf` ：

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    如果您想要掛接、格式化和建立交換，您可以：
    * 每次建立 VM 時，以雲端初始設定的形式傳遞此設定
    * 在每次建立 VM 時，使用內建至映射的雲端初始指示詞：

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```
1. 如果您要取消註冊訂用帳戶，請執行下列命令：

    ```console
    # sudo subscription-manager unregister
    ```

1. 取消佈建

    執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    > [!CAUTION]
    > 如果您要遷移特定的虛擬機器，但不想建立一般化映射，請略過取消布建步驟。 `waagent -force -deprovision`執行命令會使來源電腦無法使用，此步驟僅供建立一般化映射之用。


1. 按一下 [hyper-v 管理員] 中的 [**動作**  >  **關機**]。 您現在可以將 Linux VHD 上傳至 Azure。


## <a name="kvm"></a>KVM

本節說明如何使用 KVM 來準備 [RHEL 6](#rhel-6-using-kvm) 或 [rhel 7](#rhel-7-using-kvm) 發行版本以上傳至 Azure。 

### <a name="rhel-6-using-kvm"></a>使用 KVM 的 RHEL 6

1. 從 Red Hat 網站下載 RHEL 6 的 KVM 映像。

1. 設定根密碼。

    產生加密的密碼，並複製命令的輸出：

    ```console
    # openssl passwd -1 changeme
    ```

    使用 guestfish 設定根密碼：

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   將根使用者的第二個欄位從 "!!" 變更為加密的密碼。

1. 在 KVM 中從 qcow2 映像建立虛擬機器。 將磁碟類型設定為 **qcow2**，再將虛擬網路介面裝置模型設定為 **virtio**。 然後啟動虛擬機器並以 root 身分登入。

1. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. 移動 (或移除) udev 規則可防止產生乙太網路介面的靜態規則。 在 Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. 若要確保開機時會啟動網路服務，可執行下列命令：

    ```console
    # chkconfig network on
    ```

1. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要進行此設定，請在文字編輯器中開啟 `/boot/grub/menu.lst`，並確定預設核心包含以下參數：

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    這也將確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。
    
    此外，我們還建議您移除下列參數：

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。


1. 將 Hyper-V 模組新增至 initramfs：  

    編輯 `/etc/dracut.conf` 檔案並新增下列內容：

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    重建 initramfs：

    ```config-conf
    # dracut -f -v
    ```

1. 解除安裝 cloud-init：

    ```console
    # yum remove cloud-init
    ```

1. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動：

    ```console
    # chkconfig sshd on
    ```

    修改 /etc/ssh/sshd_config 以包含下面一行：

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. 執行以下命令來安裝 Azure Linux 代理程式：

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁片是暫存磁片，如果虛擬機器已取消布建，則可能會清空。 當您在上一個步驟中安裝 Azure Linux 代理程式之後，請在 **/etc/waagent.conf** 中適當地修改下列參數：

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. 執行下列命令以取消註冊訂用帳戶 (如有必要)：

    ```console-conf
    # subscription-manager unregister
    ```

1. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. 在 KVM 中關閉虛擬機器。

1. 將 qcow2 映像轉換成 VHD 格式。

    > [!NOTE]
    > qemu-img >=2.2.1 的版本中已知有 Bug 會導致 VHD 的格式不正確。 此問題已在 QEMU 2.6 中修正。 建議使用 qemu-img 2.2.0 或更舊版本，或更新至 2.6 或更新版本。 參考： https://bugs.launchpad.net/qemu/+bug/1490611 。
    >

    先將映像轉換成原始格式：

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    確認原始映像的大小符合 1 MB， 否則將大小四捨五入為 1 MB︰

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    將原始磁碟轉換成固定大小的 VHD：

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    或者，使用 qemu-img convert **2.6 版 +** 包含 `force_size` 選項：

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>使用 KVM 的 RHEL 7

1. 從 Red Hat 網站下載 RHEL 7 的 KVM 映像。 此程序會使用 RHEL 7 做為範例。

1. 設定根密碼。

    產生加密的密碼，並複製命令的輸出：

    ```console
    # openssl passwd -1 changeme
    ```

    使用 guestfish 設定根密碼：

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   將根使用者的第二個欄位從 "!!" 變更為加密的密碼。

1. 在 KVM 中從 qcow2 映像建立虛擬機器。 將磁碟類型設定為 **qcow2**，再將虛擬網路介面裝置模型設定為 **virtio**。 然後啟動虛擬機器並以 root 身分登入。

1. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. 若要確保開機時會啟動網路服務，可執行下列命令：

    ```console
    # sudo systemctl enable network
    ```

1. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要進行此設定，請在文字編輯器中開啟 `/etc/default/grub`，然後編輯 `GRUB_CMDLINE_LINUX` 參數。 例如：

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   此命令也會確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。 而且也會關閉新的 RHEL 7 對 NIC 的命名慣例。 此外，我們還建議您移除下列參數：

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

1. 完成 `/etc/default/grub`的編輯之後，請執行下列命令以重建 grub 組態：

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. 將 Hyper-V 模組新增至 initramfs。

    編輯 `/etc/dracut.conf` 並加入內容：

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    重建 initramfs：

    ```config-conf
    # dracut -f -v
    ```

1. 解除安裝 cloud-init：

    ```console
    # yum remove cloud-init
    ```

1. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動：

    ```console
    # systemctl enable sshd
    ```

    修改 /etc/ssh/sshd_config 以包含下面一行：

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 執行以下命令來安裝 Azure Linux 代理程式：

    ```console
    # yum install WALinuxAgent
    ```

    啟用 waagent 服務：

    ```console
    # systemctl enable waagent.service
    ```

1. 安裝 cloud-init 請遵循「從 Hyper-v 管理員準備 RHEL 7 虛擬機器」中的步驟，步驟12：「安裝雲端初始化以處理布建」。

1. 交換設定 

    請勿在作業系統磁碟上建立交換空間。
    遵循「從 Hyper-v 管理員準備 RHEL 7 虛擬機器」、步驟13、「交換設定」中的步驟


1. 執行下列命令以取消註冊訂用帳戶 (如有必要)：

    ```console
    # subscription-manager unregister
    ```


1. 取消佈建

    請依照「從 Hyper-v 管理員準備 RHEL 7 虛擬機器」、步驟15、「取消布建」中的步驟執行

1. 在 KVM 中關閉虛擬機器。

1. 將 qcow2 映像轉換成 VHD 格式。

    > [!NOTE]
    > qemu-img >=2.2.1 的版本中已知有 Bug 會導致 VHD 的格式不正確。 此問題已在 QEMU 2.6 中修正。 建議使用 qemu-img 2.2.0 或更舊版本，或更新至 2.6 或更新版本。 參考： https://bugs.launchpad.net/qemu/+bug/1490611 。
    >

    先將映像轉換成原始格式：

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    確認原始映像的大小符合 1 MB， 否則將大小四捨五入為 1 MB︰

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    將原始磁碟轉換成固定大小的 VHD：

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    或者，使用 qemu-img convert **2.6 版 +** 包含 `force_size` 選項：

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

本節說明如何從 VMware 準備 [rhel 6](#rhel-6-using-vmware) 或 [rhel 7](#rhel-6-using-vmware)  發行版本。

### <a name="prerequisites"></a>先決條件
本節假設您已在 VMware 中安裝 RHEL 虛擬機器。 如需有關如何在 VMware 中安裝作業系統的詳細資訊，請參閱 [VMware 客體作業系統安裝指南](https://partnerweb.vmware.com/GOSIG/home.html)。

* 安裝 Linux 作業系統時，我們建議您使用標準磁碟分割而不是 LVM (這通常是許多安裝的預設設定)。 這可避免 LVM 名稱與複製的虛擬機器發生衝突，特別是為了疑難排解而需要將作業系統磁碟連結至另一部虛擬機器時。 如果願意，您可以在資料磁碟上使用 LVM 或 RAID。
* 請勿在作業系統磁碟上設定交換磁碟分割。 您可以設定 Linux 代理程式以在暫存資源磁碟上建立交換檔。 您可以在以下步驟中找到與此相關的詳細資訊。
* 建立虛擬硬碟時，請選取 [將虛擬磁碟儲存為單一檔案]  。

### <a name="rhel-6-using-vmware"></a>使用 VMware 的 RHEL 6
1. 在 RHEL 6 中，NetworkManager 可能會對 Azure Linux 代理程式造成干擾。 執行下列命令以將此套件解除安裝：

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. 在 /etc/sysconfig/ 目錄中，建立名為 **network** 、且包含下列文字的檔案：

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. 移動 (或移除) udev 規則可防止產生乙太網路介面的靜態規則。 在 Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. 若要確保開機時會啟動網路服務，可執行下列命令：

    ```console
    # sudo chkconfig network on
    ```

1. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要這樣做，請在文字編輯器中開啟 `/etc/default/grub` 並編輯 `GRUB_CMDLINE_LINUX` 參數。 例如：

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   這也將確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。 此外，我們還建議您移除下列參數：

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

1. 將 Hyper-V 模組新增至 initramfs：

    編輯 `/etc/dracut.conf` 檔案並新增下列內容：

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    重建 initramfs：

    ```config-cong
    # dracut -f -v
    ```

1. 確定已安裝 SSH 伺服器，並已設定為在開機時啟動 (這通常為預設值)。 修改 `/etc/ssh/sshd_config` 以包含下面一行：

    ```config
    ClientAliveInterval 180
    ```

1. 執行以下命令來安裝 Azure Linux 代理程式：

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁片是暫存磁片，如果虛擬機器已取消布建，則可能會清空。 在上一個步驟安裝 Azure Linux 代理程式後，請在 `/etc/waagent.conf` 中適當修改下列參數：

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. 執行下列命令以取消註冊訂用帳戶 (如有必要)：

    ```console
    # sudo subscription-manager unregister
    ```

1. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. 關閉虛擬機器，然後將 VMDK 檔案轉換成 .vhd 檔案。

    > [!NOTE]
    > qemu-img >=2.2.1 的版本中已知有 Bug 會導致 VHD 的格式不正確。 此問題已在 QEMU 2.6 中修正。 建議使用 qemu-img 2.2.0 或更舊版本，或更新至 2.6 或更新版本。 參考： https://bugs.launchpad.net/qemu/+bug/1490611 。
    >

    先將映像轉換成原始格式：

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    確認原始映像的大小符合 1 MB， 否則將大小四捨五入為 1 MB︰

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    將原始磁碟轉換成固定大小的 VHD：

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    或者，使用 qemu-img convert **2.6 版 +** 包含 `force_size` 選項：

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>使用 VMware 的 RHEL 7
1. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. 若要確保開機時會啟動網路服務，可執行下列命令：

    ```console
    # sudo systemctl enable network
    ```

1. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要執行此修改，請在文字編輯器中開啟 `/etc/default/grub`，然後編輯 `GRUB_CMDLINE_LINUX` 參數。 例如：

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   此組態也會確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。 也會關閉新的 RHEL 7 對 NIC 的命名慣例。 此外，我們還建議您移除下列參數：

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

1. 完成 `/etc/default/grub`的編輯之後，請執行下列命令以重建 grub 組態：

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. 將 Hyper-V 模組新增至 initramfs。

    編輯 `/etc/dracut.conf`，新增內容：

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    重建 initramfs：

    ```console
    # dracut -f -v
    ```

1. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。 此設定通常是預設值。 修改 `/etc/ssh/sshd_config` 以包含下面一行：

    ```config
    ClientAliveInterval 180
    ```

1. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 執行以下命令來安裝 Azure Linux 代理程式：

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. 安裝雲端初始化

    請依照「從 Hyper-v 管理員準備 RHEL 7 虛擬機器」中的步驟，步驟12，「安裝 cloud-init 來處理布建」。

1. 交換設定

    請勿在作業系統磁碟上建立交換空間。
    遵循「從 Hyper-v 管理員準備 RHEL 7 虛擬機器」、步驟13、「交換設定」中的步驟

1. 如果您要取消註冊訂用帳戶，請執行下列命令：

    ```console
    # sudo subscription-manager unregister
    ```

1. 取消佈建

    請依照「從 Hyper-v 管理員準備 RHEL 7 虛擬機器」、步驟15、「取消布建」中的步驟執行


1. 關閉虛擬機器，然後將 VMDK 檔案轉換成 VHD 格式。

    > [!NOTE]
    > qemu-img >=2.2.1 的版本中已知有 Bug 會導致 VHD 的格式不正確。 此問題已在 QEMU 2.6 中修正。 建議使用 qemu-img 2.2.0 或更舊版本，或更新至 2.6 或更新版本。 參考： https://bugs.launchpad.net/qemu/+bug/1490611 。
    >

    先將映像轉換成原始格式：

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    確認原始映像的大小符合 1 MB， 否則將大小四捨五入為 1 MB︰

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    將原始磁碟轉換成固定大小的 VHD：

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    或者，使用 qemu-img convert **2.6 版 +** 包含 `force_size` 選項：

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>Kickstart 檔案

本節說明如何使用 kickstart 檔案，從 ISO 準備 RHEL 7 發行版本。

### <a name="rhel-7-from-a-kickstart-file"></a>Kickstart 檔案中的 RHEL 7

1.  建立包含以下內容的 kickstart 檔案，然後儲存此檔案。 如需有關 kickstart 安裝的詳細資訊，請參閱 [Kickstart 安裝指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)。

    ```text
    # Kickstart for provisioning a RHEL 7 Azure VM

    # System authorization information
      auth --enableshadow --passalgo=sha512

    # Use graphical install
    text

    # Do not run the Setup Agent on first boot
    firstboot --disable

    # Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    # System language
    lang en_US.UTF-8

    # Network information
    network  --bootproto=dhcp

    # Root password
    rootpw --plaintext "to_be_disabled"

    # System services
    services --enabled="sshd,waagent,NetworkManager"

    # System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    # Partition clearing information
    clearpart --all --initlabel

    # Clear the MBR
    zerombr

    # Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    # System bootloader configuration
    bootloader --location=mbr

    # Firewall configuration
    firewall --disabled

    # Enable SELinux
    selinux --enforcing

    # Don't configure X
    skipx

    # Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    # Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    # Install latest repo update
    yum update -y

    # Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    # Install WALinuxAgent
    yum install -y WALinuxAgent

    # Unregister Red Hat subscription
    subscription-manager unregister

    # Enable waaagent at boot-up
    systemctl enable waagent

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
    cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
    #cloud-config
    # Generated by Azure cloud image build
    disk_setup:
    ephemeral0:
        table_type: mbr
        layout: [66, [33, 82]]
        overwrite: True
    fs_setup:
    - device: ephemeral0.1
        filesystem: ext4
    - device: ephemeral0.2
        filesystem: swap
    mounts:
    - ["ephemeral0.1", "/mnt"]
    - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
    EOF

    # Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    # Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    # Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    # Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

1. 將 kickstart 檔案放在可存取它的安裝系統。

1. 在 Hyper-V 管理員中建立新的虛擬機器。 在 [連線虛擬硬碟]  頁面上，選取 [稍後連接虛擬硬碟]  ，並完成 [新增虛擬機器精靈]。

1. 開啟虛擬機器設定：

    a.  將新的虛擬硬碟連結至虛擬機器。 請務必選取 [VHD 格式]  和 [固定大小]  。

    b.  將安裝 ISO 連接到 DVD 光碟機。

    c.  將 BIOS 設定成從 CD 開機。

1. 啟動虛擬機器。 當安裝指南出現時，請按 **Tab** 鍵以設定開機選項。

1. 在開機選項結尾輸入 `inst.ks=<the location of the kickstart file>` ，然後按 **Enter** 鍵。

1. 等待安裝完成。 完成後，虛擬機器將會自動關閉。 您現在可以將 Linux VHD 上傳至 Azure。

## <a name="known-issues"></a>已知問題
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>使用非 Hyper-V Hypervisor 時，初始 RAM 磁碟未包含 Hyper-V 驅動程式

在某些情況下，Linux 安裝程式可能不會在初始 RAM 磁碟 (initrd 或 initramfs) 中包含 Hyper-V 的驅動程式，除非 Linux 偵測到自己在 Hyper-V 環境中執行。

當您使用不同的虛擬化系統 (也就是 VirtualBox、Xen 等 ) 準備 Linux 映射時，可能需要重建 initrd，以確保至少有 hv_vmbus 和 hv_storvsc 核心模組可在初始 RAM 磁碟上使用。 目前至少已知在以上游 Red Hat 散發套件為基礎的系統上有此問題。

若要解決這個問題，請將 Hyper-V 模組新增至 initramfs 並加以重建︰

編輯 `/etc/dracut.conf` 檔案並新增下列內容：

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

重建 initramfs：

```console
# dracut -f -v
```

如需詳細資訊，請參閱 [重建 initramfs](https://access.redhat.com/solutions/1958)的相關資訊。

## <a name="next-steps"></a>後續步驟
* 您現在可以開始使用您的 Red Hat Enterprise Linux 虛擬硬碟在 Azure 建立新的虛擬機器。 如果您是第一次將 .vhd 檔案上傳至 Azure，請參閱[從自訂磁碟建立 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。
* 如需已通過認證可執行 Red Hat Enterprise Linux 之 Hypervisor 的詳細資訊，請參閱 [Red Hat 網站](https://access.redhat.com/certified-hypervisors)。
* 若要深入瞭解如何使用已準備好用於生產環境的 RHEL BYOS 映射，請移至 [BYOS](../workloads/redhat/byos.md)的檔頁面。