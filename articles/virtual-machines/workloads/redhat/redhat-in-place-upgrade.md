---
title: 在 Azure 上就地升級 Red Hat Enterprise Linux 映射
description: 尋找從 Red Hat Enterprise 7.x 映射就地升級為最新的8.x 版所執行的步驟
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 1160bc43db0dc9ec1714b1766c8cadf09660e291
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844554"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux 就地升級

本文提供逐步指示，說明如何使用 Azure 上的 ' Leapp ' 公用程式，執行從 Red Hat Enterprise Linux 7 到 Red Hat Enterprise Linux 8 的就地升級。 在就地升級期間，現有的 RHEL 7 作業系統會由 RHEL 8 版本取代。

>[!Note] 
> Red Hat Enterprise Linux 優惠上的 SQL Server 不支援在 Azure 上進行就地升級。

## <a name="what-to-expect-during-the-upgrade"></a>升級期間的預期事項
在升級期間，系統會重新開機幾次，而且是正常的。 最後一次重新開機會將 VM 升級至 RHEL 8 最新次要版本。 

升級程式可能需要花費20分鐘到數小時的時間，這取決於數個因素，例如 VM 大小和系統上安裝的套件數目。

## <a name="preparations-for-the-upgrade"></a>升級的準備工作
「就地升級」是 Red Hat 和 Azure 正式建議的方式，可讓客戶將系統升級至下一個主要版本。 在這裡執行升級之前，您應該先留意並考慮。 

>[!Important] 
> 執行升級之前，請先取得映射的快照集。

>[!NOTE]
> 本文中的命令需要使用根帳號執行

* 請確定您使用的是最新的 RHEL 7 版本，其目前為 RHEL 7.9。 如果您使用鎖定的版本且無法升級至 RHEL 7.9，您可以使用這裡的 [步驟來切換至非 EUS 存放庫](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)。

* 執行下列命令，以瞭解您的升級進行中的方式，以及其是否將完成。 此命令應該會在「/var/log/leapp/leapp-report.txt」下產生檔案，以說明程式和進行的作業，以及是否可能進行升級
    ```bash
    leapp preupgrade --no-rhsm
    ```
* 確定序列主控台可正常運作，因為這可讓您在升級過程中進行監視。

* 啟用 SSH 根目錄存取 `/etc/ssh/sshd_config`
    1. 開啟檔案 `/etc/ssh/sshd_config`
    1. 搜尋 ' #PermitRootLogin yes '
    1. 移除 ' # ' 以取消批註

## <a name="steps-for-performing-the-upgrade"></a>執行升級的步驟

請小心執行這些步驟。 建議您在實際執行實例之前，先在測試電腦上嘗試升級。

1. 執行 yum 更新，以提取最新的用戶端套件。
    ```bash
    yum update -y
    ```

1. 安裝 leapp-用戶端套件。
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. 使用具有 repomap.csv 的 gz 檔案，並在 [RedHat 入口網站](https://access.redhat.com/articles/3664871)中提供 pes-events.js，並將其解壓縮。 
    1. 下載檔案。
    1. 使用下列命令將內容解壓縮，並移除檔案：
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. 新增 ' Leapp ' 的「解答」檔案。
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. 執行 ' Leapp ' 升級。
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade`命令順利完成後，請手動重新開機系統以完成此程式。 系統將會重新開機幾次，在這段期間將無法使用。 使用序列主控台監視處理常式。

1.  確認升級已順利完成。
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. 升級完成之後，請移除根 ssh 存取。
    1. 開啟檔案 `/etc/ssh/sshd_config`
    1. 搜尋 ' #PermitRootLogin yes '
    1. 新增 ' # ' 以進行批註

1. 重新開機 sshd 服務，變更才會生效
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>常見問題
這些是 `leapp preupgrade` 或進程可能失敗的一些常見實例 `leapp upgrade` 。

**錯誤：找不到下列已停用外掛程式模式的相符專案**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**解決方式**\
藉由編輯檔案 `/etc/yum/pluginconf.d/subscription-manager.conf` 並將 [已啟用] 變更為，以停用訂用帳戶管理員外掛程式 `enabled=0` 。

這是因為訂用帳戶管理員 yum 外掛程式已啟用，但未用於 PAYG Vm。

**錯誤：使用根的遠端登入可能發生問題**`leapp preupgrade`可能會因為下列錯誤而失敗：
```structured-text
============================================================
                     UPGRADE INHIBITED
============================================================

Upgrade has been inhibited due to the following problems:
    1. Inhibitor: Possible problems with remote login using root account
Consult the pre-upgrade report for details and possible remediation.

============================================================
                     UPGRADE INHIBITED
============================================================
```
**解決方式**\
啟用的根目錄存取 `/etc/sshd_config` 。
這是因為「 `/etc/sshd_config` [升級準備工作](#preparations-for-the-upgrade)」一節中的「根 ssh 存取」未啟用。 

## <a name="next-steps"></a>後續步驟
* 深入瞭解 [Azure 中的 Red Hat 映射](./redhat-images.md)。
* 深入瞭解 [Red Hat 更新基礎結構](./redhat-rhui.md)。
* 深入瞭解 [RHEL BYOS 供應](./byos.md)專案。
* 有關 Red Hat 就地升級程式的資訊，可在 Red Hat 檔中找到， [從 RHEL 7 升級至 rhel 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
