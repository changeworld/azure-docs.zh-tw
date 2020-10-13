---
title: Red Hat Update Infrastructure | Microsoft Docs
description: 了解適用於 Microsoft Azure 中隨選 Red Hat Enterprise Linux 執行個體的 Red Hat Update Infrastructure
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 3bdbd84785053b79198479305e1ca76e133a46f3
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994269"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>適用於 Azure 中隨選 Red Hat Enterprise Linux VM 的 Red Hat Update Infrastructure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) 允許雲端提供者 (例如 Azure) 鏡像 Red Hat 代管的存放庫內容、建立具有 Azure 特定內容的自訂存放庫，以及讓它可供使用者 VM 使用。

Red Hat Enterprise Linux (RHEL) 隨用隨付 (PAYG) 映像預先設定為存取 Azure RHUI。 不需要任何其他設定。 若要取得最新的更新，請在 RHEL 執行個體備妥之後執行 `sudo yum update`。 此服務包含在 RHEL PAYG 軟體費用中。

有關 Azure 中 RHEL 映像的其他資訊 (包括發佈和保留原則)，可在[這裡](./redhat-images.md)取得。

如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。

> [!IMPORTANT]
> RHUI 僅適用於隨用隨付 (PAYG) 映像。 針對自訂和黃金映像 (也稱為自備訂用帳戶 (BYOS))，系統必須連接到 RHSM 或衛星，才能接收更新。 如需詳細資訊，請參閱 [Red Hat 文章](https://access.redhat.com/solutions/253273)。


## <a name="important-information-about-azure-rhui"></a>Azure RHUI 的重要資訊

* Azure RHUI 是更新基礎結構，可支援在 Azure 中建立的所有 RHEL PAYG VM。 這不會讓您無法使用訂用帳戶管理員或衛星或是其他更新來源註冊 PAYG RHEL VM，但使用 PAYG VM 這麼做時，將會產生間接的雙重計費。 請參閱下列重點以取得詳細資料。
* 對 Azure 代管之 RHUI 的存取，包含在 RHEL PAYG 映像價格中。 如果您將 PAYG RHEL VM 從 Azure 代管的 RHUI 取消註冊，這樣並不會將虛擬機器轉換成自備授權 (BYOL) 類型的虛擬機器。 如果您以另一個更新來源註冊相同的 VM，可能會產生「間接」雙重費用。 您需要支付 Azure RHEL 軟體費用， 而且還需要支付先前已購買的 Red Hat 訂用帳戶費用。 如果您持續需要使用非 Azure 代管 RHUI 的更新基礎結構，請考慮註冊以使用 [RHEL BYOS 映像](./byos.md)。

* Azure 中 RHEL PAYG 映像 (RHEL for SAP、RHEL for SAP HANA 和 RHEL for SAP Business Applications) 會連線至專用 RHUI 通道，以維持在 SAP 認證所需的特定 RHEL 次要版本。

* 只有 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 中的 VM 能夠存取 Azure 代管的 RHUI。 如果您透過內部部署網路基礎結構為所有 VM 流量設定 Proxy，則可能需要為 RHEL PAYG VM 設定使用者定義的路由，以便存取 Azure RHUI。 如果是這種情況，則必須新增「所有」RHUI IP 位址的使用者定義路由。


## <a name="image-update-behavior"></a>映像更新行為

自 2019 年 4 月起，Azure 提供的 RHEL 映像依預設會連線到「延伸更新支援」(EUS) 存放庫，而所產生的 RHEL 映像則依預設會連線到一般 (非 EUS) 存放庫。 如需 RHEL EUS 的詳細資料，請參閱 Red Hat 的[版本生命週期文件](https://access.redhat.com/support/policy/updates/errata)和 [EUS 文件](https://access.redhat.com/articles/rhel-eus)。 `sudo yum update` 的預設行為會依據您佈建的來源 RHEL 映像而有所不同，因為不同的映像會連線到不同的存放庫。

如需完整的映像清單，請使用 Azure CLI 執行 `az vm image list --publisher redhat --all`。

### <a name="images-connected-to-non-eus-repositories"></a>連線至非 EUS 存放庫的映像

如果您從已連線至非 EUS 存放庫的 RHEL 映像佈建 VM，則當您執行 `sudo yum update` 時，將會升級至最新的 RHEL 次要版本。 例如，如果您從 RHEL 7.4 PAYG 映射布建 VM 並執行 `sudo yum update` ，您最後會得到 rhel 7.8 VM (RHEL7 系列) 的最新次要版本。

連線至非 EUS 存放庫的映像將不會在 SKU 中包含次要版本號碼。 SKU 是 URN (映像的完整名稱) 中的第三個元素。 例如，下列所有產生的映像都會附加至非 EUS 存放庫：

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

請注意，SKU 為 7-LVM 或 7-RAW。 次要版本會在這些映像的版本 (URN 中的第四個元素) 中指出。

### <a name="images-connected-to-eus-repositories"></a>連線至 EUS 存放庫的映像

如果您從已連線至 EUS 存放庫的 RHEL 映像佈建 VM，則當您執行 `sudo yum update` 時，將不會升級至最新的 RHEL 次要版本。 這是因為連線至 EUS 存放庫的映像也會將版本鎖定為其特定的次要版本。

連線至 EUS 存放庫的映像將會在 SKU 中包含次要版本號碼。 例如，下列所有產生的映像都會附加至 EUS 存放庫：

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS 和版本鎖定的 RHEL VM

在佈建 VM 之後，可能需要將 RHEL VM 鎖定至特定 RHEL 次要版本的客戶可以使用「擴充更新支援」(EUS) 存放庫。 您可以藉由將存放庫更新為指向「擴充更新支援」存放庫，將 RHEL 虛擬機器的版本鎖定為特定次要版本。 您也可以復原 EUS 版本鎖定作業。

>[!NOTE]
> RHEL 額外項目上不支援 EUS。 這表示如果您要安裝的套件通常可從 RHEL 額外項目通道取得，則在 EUS 時將無法這麼做。 Red Hat 額外項目產品生命週期詳述於[這裡](https://access.redhat.com/support/policy/updates/extras/)。

在撰寫本文時，RHEL <= 7.4 的 EUS 支援已結束。 如需詳細資訊，請參閱 [Red Hat 檔](https://access.redhat.com/support/policy/updates/errata/#Long_Support) 中的「Red Hat Enterprise Linux 擴充維護」一節。
* RHEL 7.4 EUS 支援於 2019 年 8 月 31 日結束
* RHEL 7.5 EUS 支援於 2020 年 4 月 30 日結束
* RHEL 7.6 EUS 支援將于2021年5月31日結束
* RHEL 7.7 EUS 支援於 2021 年 8 月 30 日結束

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>切換 RHEL VM 7.x 以 EUS 特定次要版本的 (版本-鎖定) 
使用下列指示，將 RHEL 7.x VM 鎖定至特定次要版本， (以根) 執行：

>[!NOTE]
> 這僅適用于適用 EUS 的 RHEL 7.x 版本。 撰寫本文時，這包括 RHEL 7.2-7.7。 如需更多詳細資料，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata) \(英文\) 頁面。

1. 停用非 EUS 存放庫：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. 新增 EUS 存放庫：
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. 鎖定 `releasever` 變數 (以 root 身分執行)：
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 上述指示會將 RHEL 次要版本鎖定為目前次要版本。 如果您要升級並鎖定到不是最新的次要版本，請輸入特定次要版本。 例如， `echo 7.5 > /etc/yum/vars/releasever` 會將您的 rhel 版本鎖定至 rhel 7.5。

1. 更新您的 RHEL 虛擬機器
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>切換 RHEL VM 8.x 以 EUS 特定次要版本的 (版本-鎖定) 
使用下列指示，將 RHEL 8.x VM 鎖定至特定次要版本， (以根) 執行：

>[!NOTE]
> 這僅適用于 EUS 可用的 RHEL 8.x 版。 在撰寫本文時，這包括 RHEL 8.1-8.2。 如需更多詳細資料，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata) \(英文\) 頁面。

1. 停用非 EUS 存放庫：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. 取得 EUS 存放庫設定檔：
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. 新增 EUS 存放庫：
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. 鎖定 `releasever` 變數 (以 root 身分執行)：
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 上述指示會將 RHEL 次要版本鎖定為目前次要版本。 如果您要升級並鎖定到不是最新的次要版本，請輸入特定次要版本。 例如， `echo 8.1 > /etc/yum/vars/releasever` 會將您的 rhel 版本鎖定至 rhel 8.1。

    >[!NOTE]
    > 如果有許可權問題可以存取 releasever，您可以使用 ' nano/etc/yum/vars/releaseve ' 編輯檔案並新增映射版本詳細資料，並 ( ' Ctrl + o ' 儲存，然後按 enter 鍵，然後按下 Ctrl + x ' ) 。  

1. 更新您的 RHEL 虛擬機器
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>將 RHEL 7.x VM 切換回非 EUS (移除版本鎖定) 
以 root 身分執行下列內容：
1. 移除 `releasever` 檔案：
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. 停用 EUS 存放庫：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. 設定 RHEL VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. 更新您的 RHEL 虛擬機器
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>將 RHEL 8.x VM 切換回非 EUS (移除版本鎖定) 
以 root 身分執行下列內容：
1. 移除 `releasever` 檔案：
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. 停用 EUS 存放庫：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. 取得一般存放庫設定檔：
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. 新增 EUS 存放庫：
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
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
>[!NOTE]
>從2020年1月起，新的 Azure 美國政府映射將會使用上述 Azure Global 標頭下所述的公用 IP。

>[!NOTE]
>另外也請注意，Azure 德國已被取代為公用德國區域。 Azure 德國客戶的建議是使用 [這裡](#manual-update-procedure-to-use-the-azure-rhui-servers)的步驟開始指向公用 RHUI。

## <a name="azure-rhui-infrastructure"></a>Azure RHUI 基礎結構


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>更新虛擬機器上已過期的 RHUI 用戶端憑證

如果您使用的是較舊的 RHEL VM 映像，例如 RHEL 7.4 (映像 URN：`RedHat:RHEL:7.4:7.4.2018010506`)，因為 TLS/SSL 用戶端憑證已過期，所以會遭遇對 RHUI 的連線問題。 您會看到如下錯誤： _「SSL 同儕節點拒絕了您已過期的憑證」_ 或 _「錯誤:無法擷取存放庫的存放庫中繼資料 (repomd.xml): ...請確認其路徑正確，然後再試一次」_ 。 若要解決這個問題，請使用下列命令來更新虛擬機器上的 RHUI 用戶端套件：

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

或者，也可以執行 `sudo yum update`，如此也會更新用戶端憑證套件 (端視您的 RHEL 版本而定)，只是會看到其他存放庫顯示「SSL 憑證已過期」錯誤。 如果更新成功，其他 RHUI 存放庫應該會恢復正常連線，以便您可以順利執行 `sudo yum update`。

如果您在執行 `yum update` 時發生 404 錯誤，請嘗試下列動作以重新整理您的 yum 快取：
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

- RHEL 8：
    1. 建立組態檔：
        ```bash
        vi rhel8.config
        ```
    1. 將下列內容新增至組態檔：
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. 儲存檔案，然後執行下列命令：
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. 更新您的 VM
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>後續步驟
* 若要從 Azure Marketplace PAYG 映像建立 Red Hat Enterprise Linux VM 並使用 Azure 代管的 RHUI，請移至 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6)。
* 若要深入了解 Azure 中的 Red Hat 映像，請移至[文件頁面](./redhat-images.md)。
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
