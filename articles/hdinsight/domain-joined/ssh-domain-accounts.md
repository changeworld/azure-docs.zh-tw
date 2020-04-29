---
title: 在 Azure HDInsight 中管理網域帳戶的 SSH 存取
description: 在 HDInsight 中管理 Azure AD 帳戶之 SSH 存取的步驟。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472514"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>在 Azure HDInsight 中管理網域帳戶的 SSH 存取

在安全叢集上，預設會允許[AZURE AD DS](../../active-directory-domain-services/overview.md)中的所有網域使用者透過[SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)連線到前端和邊緣節點。 這些使用者不屬於 sudoers 群組，而且不會取得根存取權。 在叢集建立期間建立的 SSH 使用者將具備根存取權。

## <a name="manage-access"></a>管理存取權

若要修改對特定使用者或群組的 SSH 存取`/etc/ssh/sshd_config` ，請在每個節點上更新。

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到您的叢集。 以您叢集的名稱取代 CLUSTERNAME，然後輸入命令，以編輯下面的命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 開啟`ssh_confi`g 檔案。

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. `sshd_config`視需要修改檔案。 如果您將使用者限制在特定群組，則本機帳戶無法透過 SSH 連線到該節點。 以下是語法的範例：

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    然後儲存變更： **Ctrl + X**、 **Y**、 **Enter**。

1. 重新開機 sshd。

    ```bash
    sudo systemctl restart sshd
    ```

1. 針對每個節點重複上述步驟。

## <a name="ssh-authentication-log"></a>SSH 驗證記錄

SSH 驗證記錄檔會寫入`/var/log/auth.log`。 如果您看到透過 SSH 針對本機或網域帳戶的任何登入失敗，您將需要經過記錄檔以進行錯誤的檢查。 此問題通常與特定的使用者帳戶有關，而使用預設的 SSH 使用者（本機帳戶）嘗試其他使用者帳戶或 SSH 通常是很好的作法，然後嘗試 kinit。

## <a name="ssh-debug-log"></a>SSH 調試記錄

若要啟用詳細資訊記錄，您必須使用`sshd` `-d`選項重新開機。 就`/usr/sbin/sshd -d`像您也可以`sshd`在自訂埠（例如2222）執行，這樣就不需要停止主要的 SSH daemon。 您也可以搭配`-v` SSH 用戶端使用選項來取得更多記錄（失敗的用戶端觀點）。

## <a name="next-steps"></a>後續步驟

* [使用企業安全性套件管理 HDInsight 叢集](./apache-domain-joined-manage.md)
* [使用 SSH 連線到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。
