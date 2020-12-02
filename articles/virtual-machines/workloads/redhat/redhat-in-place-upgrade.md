---
title: 在 Azure 上就地升級 Red Hat Enterprise Linux 映射
description: 瞭解如何從 Red Hat Enterprise 7.x 映射就地升級為最新的8.x 版。
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 9b35a3cbe23af91f7f0b8aceecfb8ba3c9720461
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484408"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux 就地升級

本文提供如何將 Red Hat Enterprise Linux (RHEL) 7 就地升級到 Red Hat Enterprise Linux 8 的指示。 這些指示會使用 `leapp` Azure 中的工具。 在就地升級期間，現有的 RHEL 7 作業系統會由 RHEL 8 版本取代。

>[!Note] 
> Red Hat Enterprise Linux 上的 SQL Server 供應專案不支援 Azure 上的就地升級。

## <a name="what-to-expect-during-the-upgrade"></a>升級期間的預期事項
在升級期間，系統會重新開機幾次。 最後一次重新開機會將 VM 升級至 RHEL 8 最新次要版本。 

升級程式可能需要20分鐘到2小時的時間。 總時間取決於數個因素，例如 VM 大小和系統上所安裝的套件數目。

## <a name="preparations"></a>準備
Red Hat 和 Azure 建議使用就地升級，將系統轉換成下一個主要版本。 

開始升級之前，請記住下列考慮事項。 

>[!Important] 
> 開始升級之前，請先取得映射的快照集。

* 請確定您使用的是最新的 RHEL 7 版本。 目前，最新版本為 RHEL 7.9。 如果您使用鎖定的版本且無法升級至 RHEL 7.9，請遵循 [下列步驟來切換至非 EUS (擴充更新支援) 存放庫](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)。

* 執行下列命令來檢查您的升級，並查看它是否能順利完成。 此命令應該會產生 */var/log/leapp/leapp-report.txt* 檔。 這個檔案會說明程式、發生的狀況，以及是否可能進行升級。

    >[!NOTE]
    > 使用根帳號來執行本文中的命令。 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* 確定序列主控台可正常運作。 在升級過程中，您將使用此主控台進行監視。

* 在 */etc/ssh/sshd_config* 中啟用 SSH 根目錄存取：
    1. 開啟 [file */etc/ssh/sshd_config*]。
    1. 搜尋 `#PermitRootLogin yes`。
    1. 移除數位記號 (`#`) 以取消批註字串。

## <a name="upgrade-steps"></a>升級步驟

請仔細遵循這些步驟。 建議您先在測試電腦上嘗試升級，然後再于生產實例上試用。

1. 進行 `yum` 更新以提取最新的用戶端套件。
    ```bash
    yum update -y
    ```

1. 安裝 `leapp-client-package`
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. 在 [Red Hat 入口網站](https://access.redhat.com/articles/3664871)中，取得包含 *repomap.csv* 和 *pes-events.js* 的 *leapp-data。* 將 *leapp-data gz* 檔案解壓縮。
    1. 下載 *leapp-data gz* 檔案。
    1. 將內容解壓縮，並移除該檔案。 使用下列命令：
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. 新增 `answers` 的檔案 `leapp` 。
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. 開始升級。
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade`命令順利完成後，請手動重新開機系統以完成此程式。 系統無法使用，因為它會重新開機幾次。 使用序列主控台監視處理常式。

1.  確認升級已成功完成。
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. 當升級完成時，移除根 SSH 存取：
    1. 開啟 [file */etc/ssh/sshd_config*]。
    1. 搜尋 `#PermitRootLogin yes`。
    1. 新增數位記號 (`#`) 來批註字串。

1. 重新開機 SSHD 服務以套用變更。
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>常見問題

當 `leapp preupgrade` 進程失敗或進程失敗時，通常會發生下列錯誤 `leapp upgrade` ：

* **錯誤**：找不到下列已停用外掛程式模式的相符專案。

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **解決方案**：停用訂用帳戶管理員外掛程式。 藉由編輯檔案 */etc/yum/pluginconf.d/subscription-manager.conf* 並變更為來停用它 `enabled` `enabled=0` 。

    當 `yum` vm 未使用已啟用的訂用帳戶管理員外掛程式時，就會發生此錯誤 `PAYG` 。

* **錯誤**：使用根的遠端登入可能發生問題。

    當失敗時，您可能會看到此錯誤 `leapp preupgrade` ：

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
    **解決方案**：在 */Etc/sshd_config* 中啟用根目錄存取。

    */Etc/sshd_config* 中未啟用根 SSH 存取時，就會發生此錯誤。 如需詳細資訊，請參閱本文中的「 [準備](#preparations) 」一節。 


## <a name="next-steps"></a>後續步驟
* 深入瞭解 [Azure 中的 Red Hat 映射](./redhat-images.md)。
* 深入瞭解 [Red Hat 更新基礎結構](./redhat-rhui.md)。
* 深入瞭解 [RHEL BYOS 供應](./byos.md)專案。
* 若要深入瞭解 Red Hat 就地升級流程，請參閱 Red Hat 檔中的 [從 rhel 7 升級至 rhel 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) 。
* 若要深入瞭解所有 RHEL 版本的 Red Hat 支援原則，請參閱 Red Hat 檔中的 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata) 。