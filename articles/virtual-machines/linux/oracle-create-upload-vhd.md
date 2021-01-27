---
title: 建立及上傳 Oracle Linux VHD
description: 了解如何建立及上傳包含 Oracle Linux 作業系統的 Azure 虛擬硬碟 (VHD)。
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 34ebc126ba4a1ae725325f8d888899af2dd72a59
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875663"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>準備用於 Azure 的 Oracle Linux 虛擬機器

本文假設您已將 Oracle Linux 作業系統安裝到虛擬硬碟。 有多個工具可用來建立 .vhd 檔案，例如，像是 Hyper-V 的虛擬化解決方案。 如需指示，請參閱 [安裝 Hyper-V 角色及設定虛擬機器](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))。

## <a name="oracle-linux-installation-notes"></a>Oracle Linux 安裝注意事項
* 如需有關準備 Azure 之 Linux 的更多秘訣，另請參閱 [一般 Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes) 。
* Hyper-V 和 Azure 支援具有 Unbreakable Enterprise Kernel (UEK) 或 Red Hat Compatible Kernel 的 Oracle Linux。
* Hyper-V 和 Azure 不支援 Oracle 的 UEK2，因為它不包含必要的驅動程式。
* Azure 不支援 VHDX 格式，只支援 **固定 VHD**。  您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。
* **需要掛接 UDF 檔案系統的核心支援。** 在 Azure 上第一次開機時，佈建組態會透過連接客體的 UDF 格式媒體傳遞至 Linux VM。 Azure Linux 代理程式必須能夠掛接 UDF 檔案系統讀取其組態並佈建 VM。
* 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。 這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。 如果願意，您可以在資料磁碟上使用 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 或 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)。
* 早於 2.6.37 的 Linux 核心版本不支援具較大 VM 大小之 Hyper-V 上的 NUMA。 這個問題主要會影響使用上游 Red Hat 2.6.32 核心的較舊發行版本，且已在 Oracle Linux 6.6 和更新版本中加以修正
* 請勿在作業系統磁碟上設定交換磁碟分割。 您可以在以下步驟中找到與此有關的詳細資訊。
* Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從未經處理的磁碟轉換成 VHD 時，您必須確定未經處理的磁碟大小在轉換前是 1 MB 的倍數。 如需詳細資訊，請參閱 [Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)。
* 確定已啟用 `Addons` 儲存機制。 編輯檔案 `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 或 `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7)，將此檔案中 **[ol6_addons]** 或 **[ol7_addons]** 下的 `enabled=0` 一行變更為 `enabled=1`。

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 和更新版本
您必須在作業系統中完成特定組態步驟，虛擬機器才能在 Azure 中執行。

1. 在 Hyper-V 管理員的中間窗格中，選取虛擬機器。
2. 按一下 **[連接]** ，以開啟虛擬機器的視窗。
3. 執行下列命令以解除安裝 NetworkManager：

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **注意：** 如果尚未安裝封裝，此命令將會失敗，並出現錯誤訊息。 這是預期行為。
4. 在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. 在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. 修改 udev 角色可防止產生乙太網路介面的靜態規則。 在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則可能會造成問題：

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. 要確保開機時會啟動網路服務，可執行以下命令：

    ```console
    # chkconfig network on
    ```

8. 執行下列命令以安裝 python-pyasn1：

    ```console
    # sudo yum install python-pyasn1
    ```

9. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要這樣做，請在文字編輯器中開啟 "/boot/grub/menu.lst"，並確定核心包含下列參數：

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。
   
   除了上述以外，我們還建議您 *移除* 下列參數：

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。
   
   如有需要，您可以保留 `crashkernel` 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。
10. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。  這通常是預設值。
11. 執行以下命令來安裝 Azure Linux 代理程式。 最新版為 2.0.15。

    ```console
    # sudo yum install WALinuxAgent
    ```

    請注意，如果 NetworkManager 和 NetworkManager-gnome 套件沒有如步驟 2 所述遭到移除，則在安裝 WALinuxAgent 套件時會將這兩個套件移除。
12. 請勿在作業系統磁碟上建立交換空間。
    
    Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。 請注意，資源磁碟是 *暫存* 磁碟，可能會在 VM 取消佈建時清空。 安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。 您現在可以將 Linux VHD 上傳至 Azure。

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 和更新版本
**Oracle Linux 7 中的變更**

準備適用於 Azure 的 Oracle Linux 7 虛擬機器會與 Oracle Linux 6 極為類似，不過，其中有幾個重要差異值得注意：

* Azure 支援具有 Unbreakable Enterprise Kernel (UEK) 或 Red Hat Compatible Kernel 的 Oracle Linux。 建議使用具有 UEK 的 Oracle Linux。
* NetworkManager 封裝不會再與 Azure Linux 代理程式發生衝突。 依預設會安裝此封裝，建議您不要將它移除。
* GRUB2 現已作為預設的開機載入器使用，因此我們已變更編輯核心參數的程序 (如下所示)。
* XFS 現為預設的檔案系統。 如有需要，您仍可使用 ext4 檔案系統。

**組態步驟**

1. 在 Hyper-V 管理員中，選取虛擬機器。
2. 按一下 [連接] ，以開啟虛擬機器的主控台視窗。
3. 在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. 在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. 修改 udev 角色可防止產生乙太網路介面的靜態規則。 在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則可能會造成問題：

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. 要確保開機時會啟動網路服務，可執行以下命令：

    ```console
    # sudo chkconfig network on
    ```

7. 執行下列命令以安裝 python-pyasn1 封裝：

    ```console
    # sudo yum install python-pyasn1
    ```

8. 執行下列命令，以清除目前的 yum 中繼資料並安裝任何更新：

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要執行這個動作，請在文字編輯器中開啟 "/etc/default/grub" 並編輯 `GRUB_CMDLINE_LINUX` 參數，例如：

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。 也會關閉具有 Unbreakable Enterprise Kernel 的 Oracle Linux 7 中對 NIC 的命名慣例。 除了上述以外，我們還建議您 *移除* 下列參數：

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。
   
   如有需要，您可以保留 `crashkernel` 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。
10. 在您參照上述完成編輯 "/etc/default/grub" 之後，請執行下列命令以重建 grub 組態：

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。  這通常是預設值。
12. 安裝 Azure Linux 代理程式與相依性：

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. 安裝雲端初始化以處理布建

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. 交換設定不會在作業系統磁片上建立交換空間。

    先前，Azure Linux 代理程式已在 Azure 上布建虛擬機器後，使用連接至虛擬機器的本機資源磁片自動設定交換空間。 不過，這現在由雲端初始處理，您 **不** 能使用 Linux 代理程式來格式化資源磁片建立分頁檔，適當地修改下列參數 `/etc/waagent.conf` ：

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
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


15. 執行下列命令，以取消佈建虛擬機器，並準備將它佈建於 Azure 上：

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

16. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。 您現在可以將 Linux VHD 上傳至 Azure。

## <a name="next-steps"></a>後續步驟
您現在可以開始使用您的 Oracle Linux .vhd 在 Azure 中建立新的虛擬機器。 如果您是第一次將 .vhd 檔案上傳至 Azure，請參閱[從自訂磁碟建立 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。