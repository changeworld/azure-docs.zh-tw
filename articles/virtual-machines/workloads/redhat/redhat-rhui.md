---
title: Red Hat Update Infrastructure | Microsoft Docs
description: 了解適用於 Microsoft Azure 中隨選 Red Hat Enterprise Linux 執行個體的 Red Hat Update Infrastructure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: aa9fd230f59b5e46576e78beb0436c85449d3c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256907"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>適用於 Azure 中隨選 Red Hat Enterprise Linux VM 的 Red Hat Update Infrastructure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) 允許雲端提供者 (例如 Azure) 鏡像 Red Hat 代管的存放庫內容、建立具有 Azure 特定內容的自訂存放庫，以及讓它可供使用者 VM 使用。

Red Hat Enterprise Linux (RHEL) 隨用隨付 (PAYG) 映像預先設定為存取 Azure RHUI。 不需要任何其他設定。 若要取得最新的更新，請在 RHEL 執行個體備妥之後執行 `sudo yum update`。 此服務包含在 RHEL PAYG 軟體費用中。

有關 Azure 中 RHEL 映像的其他資訊 (包括發佈和保留原則)，可在[這裡](./redhat-images.md)取得。

如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。

> [!IMPORTANT]
> RHUI 僅用於即用即付 （PAYG） 圖像。 對於自訂和金色圖像（也稱為自帶訂閱 （BYOS），系統需要連接到 RHSM 或衛星以接收更新。 有關詳細資訊，請參閱[紅帽文章](https://access.redhat.com/solutions/253273)。


## <a name="important-information-about-azure-rhui"></a>Azure RHUI 的重要資訊

* Azure RHUI 是支援在 Azure 中創建的所有 RHEL PAYG VM 的更新基礎結構。 這並不妨礙您向訂閱管理器或衛星或其他更新源註冊 PAYG RHEL VM，但使用 PAYG VM 這樣做將導致間接雙重計費。 有關詳細資訊，請參閱以下幾點。
* 對 Azure 代管之 RHUI 的存取，包含在 RHEL PAYG 映像價格中。 如果您將 PAYG RHEL VM 從 Azure 代管的 RHUI 取消註冊，這樣並不會將虛擬機器轉換成自備授權 (BYOL) 類型的虛擬機器。 如果您以另一個更新來源註冊相同的 VM，可能會產生「間接」__ 雙重費用。 您需要支付 Azure RHEL 軟體費用， 而且還需要支付先前已購買的 Red Hat 訂用帳戶費用。 如果始終需要使用 Azure 託管的 RHUI 以外的更新基礎結構，請考慮註冊以使用[RHEL BYOS 映射](./byos.md)。

* Azure 中 RHEL PAYG 映像 (RHEL for SAP、RHEL for SAP HANA 和 RHEL for SAP Business Applications) 會連線至專用 RHUI 通道，以維持在 SAP 認證所需的特定 RHEL 次要版本。

* 只有 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 中的 VM 能夠存取 Azure 代管的 RHUI。 如果您透過內部部署網路基礎結構為所有 VM 流量設定 Proxy，則可能需要為 RHEL PAYG VM 設定使用者定義的路由，以便存取 Azure RHUI。 如果是這種情況，則需要_為所有_RHUI IP 位址添加使用者定義的路由。


## <a name="image-update-behavior"></a>圖像更新行為

自 2019 年 4 月起，Azure 提供預設連接到擴展更新支援 （EUS） 存儲庫的 RHEL 映射，預設情況下提供連接到常規（非 EUS） 存儲庫的 RHEL 映射。 有關 RHEL EUS 的更多詳細資訊，請參閱紅帽[的版本生命週期文檔](https://access.redhat.com/support/policy/updates/errata)和[EUS 文檔](https://access.redhat.com/articles/rhel-eus)。 由於不同的映射連接到`sudo yum update`不同的存儲庫，因此 的預設行為因預配的 RHEL 映射而異。

對於完整的映射清單，請使用`az vm image list --publisher redhat --all`Azure CLI 運行。

### <a name="images-connected-to-non-eus-repositories"></a>連接到非 EUS 存儲庫的圖像

如果從連接到非 EUS 存儲庫的 RHEL 映射預配 VM，則在運行`sudo yum update`時將升級到最新的 RHEL 次要版本。 例如，如果從 RHEL 7.4 PAYG 映射預配 VM`sudo yum update`並運行 ，則最終使用 RHEL 7.7 VM（RHEL7 系列中最新的次要版本）。

連接到非 EUS 存儲庫的圖像在 SKU 中不包含次要版本號。 SKU 是 URN 中的第三個元素（映射的全名）。 例如，以下所有圖像都附加到非 EUS 存儲庫：

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

請注意，SKU 為 7-LVM 或 7-RAW。 這些映射的版本（URN 中的第四個元素）中指示次要版本。

### <a name="images-connected-to-eus-repositories"></a>連接到 EUS 存儲庫的圖像

如果從連接到 EUS 存儲庫的 RHEL 映射預配 VM，則在運行`sudo yum update`時不會升級到最新的 RHEL 次要版本。 這是因為連接到 EUS 存儲庫的圖像也版本鎖定到其特定的次要版本。

連接到 EUS 存儲庫的圖像將在 SKU 中包含一個次要版本號。 例如，以下所有圖像都附加到 EUS 存儲庫：

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS 和版本鎖定的 RHEL VM

擴展更新支援 （EUS） 存儲庫可供在預配 VM 後將其 RHEL VM 鎖定為特定 RHEL 次要版本的客戶。 您可以藉由將存放庫更新為指向「擴充更新支援」存放庫，將 RHEL 虛擬機器的版本鎖定為特定次要版本。 您還可以撤銷 EUS 版本鎖定操作。

>[!NOTE]
> RHEL 附加服務不支援 EUS。 這意味著，如果您正在安裝通常可從 RHEL Extras 通道提供的包，則在 EUS 上時將無法執行此操作。 紅帽額外產品生命週期[在這裡](https://access.redhat.com/support/policy/updates/extras/)詳細介紹。

在撰寫本文時，EUS 對 RHEL <= 7.4 的支援已經結束。 有關詳細資訊，請參閱[紅帽文檔中的"紅帽](https://access.redhat.com/support/policy/updates/errata/)企業 Linux 長支援附加元件"部分。
* RHEL 7.4 EUS 支援將于 2019 年 8 月 31 日結束
* RHEL 7.5 EUS 支援于 2020 年 4 月 30 日結束
* RHEL 7.6 EUS 支援結束于 2020 年 10 月 31 日
* RHEL 7.7 EUS 支援結束 于 2021 年 8 月 30 日結束

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>將 RHEL VM 切換到 EUS（版本鎖定到特定的次要版本）
使用以下說明將 RHEL VM 鎖定為特定的次要版本（以根身份運行）：

>[!NOTE]
> 這僅適用於有 EUS 可供使用的 RHEL 版本。 在撰寫本文時，這包括 RHEL 7.2-7.7。 如需更多詳細資料，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata) \(英文\) 頁面。

1. 停用非 EUS 存放庫：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. 新增 EUS 存放庫：
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. 鎖定`releasever`變數（以根運行）：
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 上述指示會將 RHEL 次要版本鎖定為目前次要版本。 如果您要升級並鎖定到不是最新的次要版本，請輸入特定次要版本。 例如，`echo 7.5 > /etc/yum/vars/releasever` 會將您的 RHEL 版本鎖定為 RHEL 7.5

1. 更新您的 RHEL 虛擬機器
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>將 RHEL VM 切換回非 EUS（刪除版本鎖定）
將以下內容作為根運行：
1. 刪除`releasever`檔：
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. 禁用 EUS 存儲庫：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. 配置 RHEL VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. 更新您的 RHEL 虛擬機器
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI 內容傳遞伺服器的 IP

在所有可使用 RHEL 隨選映像的地區，皆可使用 RHUI。 目前包含 [Azure 狀態儀表板](https://azure.microsoft.com/status/)頁面上所列的所有公用區域、Azure US Gov 和 Microsoft Azure 德國區域。

如果您使用網路組態來進一步限制來自 RHEL PAYG VM 的存取，請確定已允許下列 IP，如此 `yum update` 才能依據您所在的環境運作：


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI 基礎架構


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>更新虛擬機器上已過期的 RHUI 用戶端憑證

如果您使用的是較舊的 RHEL VM 映射，例如 RHEL 7.4（映射 URN： `RedHat:RHEL:7.4:7.4.2018010506`），則由於現已過期的 TLS/SSL 用戶端憑證，您將遇到與 RHUI 的連接問題。 您看到的錯誤可能看起來像 _"SSL 對等體拒絕您的證書為過期"_ 或 _"錯誤：無法檢索存儲庫的存儲庫中繼資料 （repomd.xml）：...請驗證其路徑，然後重試"。_ 若要解決這個問題，請使用下列命令來更新虛擬機器上的 RHUI 用戶端套件：

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

或者，也可以執行 `sudo yum update`，如此也會更新用戶端憑證套件 (端視您的 RHEL 版本而定)，只是會看到其他存放庫顯示「SSL 憑證已過期」錯誤。 如果更新成功，其他 RHUI 存放庫應該會恢復正常連線，以便您可以順利執行 `sudo yum update`。

如果在運行 時遇到 404 錯誤，`yum update`請嘗試以下操作刷新 yum 緩存：
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>針對 Azure RHUI 連線問題進行疑難排解
從 Azure RHEL PAYG VM 連線至 RHUI 時若發生問題，請依照下列步驟操作：

1. 檢查 Azure RHUI 端點的 VM 組態：

    1. 檢查 `/etc/yum.repos.d/rh-cloud.repo` 檔案 `[rhui-microsoft-azure-rhel*]` 區段的 `baseurl` 中是否包含對 `rhui-[1-3].microsoft.com` 的參考。 如果有此參考，您使用的便是新版 Azure RHUI。

    1. 如果它指向具有 `mirrorlist.*cds[1-4].cloudapp.net` 模式的位置，則需要進行組態更新。 您正在使用舊版 VM 快照集，請更新它以指向新版 Azure RHUI。

1. 只有 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 中的 VM 能夠存取 Azure 代管的 RHUI。

1. 如果您使用的是新設定，且已驗證 VM 是從 Azure IP 範圍連線，但仍無法連接至 Azure RHUI，請向 Microsoft 或 Red Hat 提出支援案例。

### <a name="infrastructure-update"></a>基礎結構更新

在 2016 年 9 月時，我們部署了更新的 Azure RHUI。 在 2017 年 4 月，我們關閉了舊版 Azure RHUI。 如果您在 2016 年 9 月或之後的時間使用 RHEL PAYG 映像 (或其快照集)，便會自動連線至新的 Azure RHUI。 不過，如果您的 VM 上有舊版快照集，則需要如下節所述手動更新其設定，才能存取 Azure RHUI。

新的 Azure RHUI 伺服器會透過 [Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)來部署。 在流量管理員中，不論所在區域為何，任何 VM 皆可使用單一端點 (rhui-1.microsoft.com)。

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>使用 Azure RHUI 伺服器的手動更新程序
此程序僅供參考。 RHEL PAYG 映像已設定成能夠連線至 Azure RHUI。 若要手動更新設定以使用 Azure RHUI 伺服器，請完成下列步驟：

- RHEL 6：
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- RHEL 7：
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- 對於 RHEL 8：
    1. 創建設定檔：
        ```bash
        vi rhel8.config
        ```
    1. 將以下內容添加到設定檔中：
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. 保存檔並運行以下命令：
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. 更新 VM
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>後續步驟
* 若要從 Azure Marketplace PAYG 映像建立 Red Hat Enterprise Linux VM 並使用 Azure 代管的 RHUI，請移至 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)。
* 若要深入了解 Azure 中的 Red Hat 映像，請移至[文件頁面](./redhat-images.md)。
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
