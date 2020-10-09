---
title: 如何將資料科學虛擬機器升級為 Ubuntu 18.04
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解如何從 CentOS 和 Ubuntu 16.04 升級至最新的 Ubuntu 18.04 資料科學虛擬機器。
keywords: 深度學習、AI、資料科學工具、資料科學虛擬機器、team data 科學流程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: d57de4d52ccf3a029a8dd1350635fb65dd3ac829
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828446"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>將資料科學虛擬機器升級為 Ubuntu 18.04

如果您有執行較舊版本（例如 Ubuntu 16.04 或 CentOS）的資料科學虛擬機器，您應該將 DSVM 遷移至 Ubuntu 18.04。 遷移將可確保您取得最新的作業系統修補程式、驅動程式、預先安裝的軟體和程式庫版本。 本檔將告訴您如何從舊版 Ubuntu 或 CentOS 遷移。 

## <a name="prerequisites"></a>必要條件

- 熟悉 SSH 和 Linux 命令列

## <a name="overview"></a>概觀

有兩種可能的遷移方式：

- 就地遷移，也稱為「相同伺服器」的遷移。 這種遷移會升級現有的 VM，而不會建立新的虛擬機器。 就地遷移是從 Ubuntu 16.04 遷移至 Ubuntu 18.04 的較簡單方式。
- 並存遷移，也稱為「伺服器間」的遷移。 此遷移會將資料從現有的虛擬機器傳輸至新建立的 VM。 並行遷移是從 Centos 遷移至 Ubuntu 18.04 的方式。 如果您覺得舊的安裝變得不必要，您可能會想要在 Ubuntu 版本之間進行升級的並行遷移。

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>如果您需要復原，請將 VM 快照集

在 Azure 入口網站中，使用搜尋列尋找 **快照** 集功能。 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]":::

1. 選取 [ **新增**]，這會將您帶到 [ **建立快照** 集] 頁面。 選取虛擬機器的訂用帳戶和資源群組。 針對 [ **區域**]，選取目標儲存體所在的相同區域。 選取 DSVM 儲存體磁片和其他備份選項。 **標準 HDD** 是適用于此備份案例的適當儲存體類型。

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]":::

2. 所有詳細資料都填滿並通過驗證之後，請選取 [ **審核 + 建立** ] 以驗證和建立快照集。 當快照集成功完成時，您會看到一則訊息，指出部署已完成。

## <a name="in-place-migration"></a>就地遷移

如果您要遷移較舊的 Ubuntu 版本，您可以選擇進行就地遷移。 這項遷移不會建立新的虛擬機器，而且步驟會比並存遷移的步驟少。  如果您想要進行並存遷移，因為您需要更多控制，或因為您是從不同的散發套件（例如 CentOS）進行遷移，請跳到 [並存的遷移](#side-by-side-migration) 區段。 

1. 從 Azure 入口網站中，啟動 DSVM 並使用 SSH 登入。 若要這樣做，請選取 **[連線]** 和 [ **SSH** ]，並遵循連接指示。 

1. 一旦連線到 DSVM 上的終端機會話，請執行下列升級命令：

    ```bash
    sudo do-release-upgrade
    ```

升級程式需要一些時間才能完成。 當程式結束時，程式會要求重新開機虛擬機器的許可權。 回答 **[是]**。 當系統重新開機時，您將會與 SSH 會話中斷連線。

### <a name="if-necessary-regenerate-ssh-keys"></a>如有必要，請重新產生 SSH 金鑰

> [!IMPORTANT] 
> 升級並重新啟動之後，您可能需要重新產生 SSH 金鑰。

當您的 VM 已升級並重新啟動之後，請嘗試透過 SSH 再次存取它。 IP 位址在重新開機期間可能已變更，因此請先進行確認，再嘗試連接。

如果您收到錯誤 **遠端主機識別已變更**，您必須重新產生 SSH 認證。

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]"
```

您現在應該能夠使用 SSH 進行連線。 如果您仍然遇到問題，請在 [連線 **]** 頁面中，遵循連結以針對 **SSH 連線問題進行疑難排解**。

## <a name="side-by-side-migration"></a>並存移轉

如果您要從 CentOS 進行遷移，或想要安裝全新的作業系統，您可以進行並存的遷移。 這種類型的遷移有更多步驟，但可讓您控制要執行的檔案。

以同一組上游來源套件為基礎的其他系統進行遷移應該相當簡單，例如 [常見問題/CentOS3](https://wiki.centos.org/FAQ/CentOS3)。

您可以選擇升級檔案系統的作業系統元件，並保留使用者目錄（例如 `/home` 就地）。 如果您保留舊的使用者主目錄，就會需要 GNOME/KDE 功能表和其他桌面專案的一些問題。 最簡單的方式是建立新的使用者帳戶，並在檔案系統上的其他地方掛接舊的目錄，以在遷移後參考、複製或連結使用者的資料。

### <a name="migration-at-a-glance"></a>一眼就能進行遷移

1.  如先前所述，建立現有 VM 的快照集

1.  從該快照集建立磁片

1.  建立新的 Ubuntu 資料科學虛擬機器

1.  在新的虛擬機器上重新建立使用者帳戶 (s) 

1.  將快照 VM 的磁片掛接為新資料科學虛擬機器上的資料磁片

1.  手動複製所需的資料

### <a name="create-a-disk-from-your-vm-snapshot"></a>從 VM 快照集建立磁片

如果您尚未如先前所述建立 VM 快照集，請這麼做。 

1. 在 [Azure 入口網站中，搜尋 **磁片** 並選取 [ **新增**]，這會開啟 [ **磁片** ] 頁面。

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]":::

2. 將 **訂**用帳戶、 **資源群組**和 **區域** 設定為 VM 快照集的值。 選擇要建立之磁片的 **名稱** 。

3. 選取 [ **來源類型** ] 作為 [ **快照** ]，然後選取 VM 快照集作為 **來源快照**集。 檢查並建立磁片。 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>建立新的 Ubuntu 資料科學虛擬機器

使用 [Azure 入口網站](https://portal.azure.com) 或 [ARM 範本](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-tutorial-resource-manager)建立新的 Ubuntu 資料科學虛擬機器。 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>在新的資料科學虛擬機器上重新建立使用者帳戶 (s) 

由於您只是從舊電腦複製資料，因此您必須重新建立要在新電腦上使用的任何使用者帳戶和軟體環境。

Linux 有足夠的彈性，可讓您自訂新安裝上的目錄和路徑，以遵循舊的電腦。 但一般而言，使用新式 Ubuntu 慣用的版面配置，並修改您的使用者環境和腳本以進行調整，會比較容易。

如需詳細資訊，請參閱 [快速入門：設定 Linux (Ubuntu) 的資料科學虛擬機器 ](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)。

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>將快照 VM 的磁片掛接為新資料科學虛擬機器上的資料磁片

1. 在 Azure 入口網站中，確定您的資料科學虛擬機器正在執行。

2. 在 Azure 入口網站中，移至資料科學虛擬機器的頁面。 選擇左邊滑軌上的 [ **磁片** ] 分頁。 選擇 [ **連接現有磁片**]。

3. 在 [ **磁片名稱** ] 下拉式清單中，選取您從舊 VM 的快照集建立的磁片。

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]":::

4. 選取 [ **儲存** ] 以更新您的虛擬機器。

> [!Important]
> 您的 VM 應該在您連接資料磁片時執行。 如果 VM 未執行，磁片可能會以不正確的順序新增，而導致令人困惑且可能無法啟動的系統。 如果您要新增 VM 的資料磁片，請選擇資料磁片旁的 **X** ，啟動 VM，然後再重新連接。

### <a name="manually-copy-the-wanted-data"></a>手動複製所需的資料 

1. 使用 SSH 登入您正在執行的虛擬機器。

2. 執行下列命令，確認您已從舊 VM 的快照集連接所建立的磁片：

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    結果看起來應該如下圖所示。 在映射中，磁片 `sda1` 是掛接在根目錄，而 `sdb2` 是 `/mnt` 暫存磁片。 從舊 VM 的快照集建立的資料磁片會識別為 `sdc1` ，但尚未提供，因為缺少裝載位置所證明。 您的結果可能會有不同的識別碼，但您應該會看到類似的模式。
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]":::
    
3. 若要存取資料磁片磁碟機，請建立其位置並加以掛接。 取代 `/dev/sdc1` 為傳回的適當值 `lsblk` ：

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. 現在， `/datadrive` 包含舊資料科學虛擬機器的目錄和檔案。 視需要將您想要的資料磁片磁碟機中的目錄或檔案複製到新的 VM。

如需詳細資訊，請參閱 [使用入口網站將資料磁片連接至 LINUX VM](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)。

## <a name="connect-and-confirm-version-upgrade"></a>連接並確認版本升級

無論您是執行就地或並存遷移，請確認您已成功升級。 從終端機會話中，執行： 

```bash
cat /etc/os-release
```

您應該會看到您正在執行 Ubuntu 18.04。

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]":::

版本的變更也會顯示在 Azure 入口網站中。

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="顯示 Azure 入口網站和搜尋列的螢幕擷取畫面，其中已醒目提示 [快照]":::

## <a name="next-steps"></a>下一步

- [Azure 中的資料科學與 Ubuntu 資料科學機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/linux-dsvm-walkthrough)
- [Azure 資料科學虛擬機器上包含哪些工具](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/tools-included) \(部分機器翻譯\)
