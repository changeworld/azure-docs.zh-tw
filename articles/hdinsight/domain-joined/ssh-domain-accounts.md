---
title: 在 Azure HDInsight 中管理域帳戶的 SSH 存取權限
description: 在 HDInsight 中管理 Azure AD 帳戶的 SSH 訪問的步驟。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472514"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>在 Azure HDInsight 中管理域帳戶的 SSH 存取權限

預設情況下，在安全群集上[，Azure AD DS](../../active-directory-domain-services/overview.md)中的所有域使用者都允許[SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)進入頭節點和邊緣節點。 這些使用者不是 sudoers 組的一部分，並且不會獲取根存取權限。 在群集創建期間創建的 SSH 使用者將具有根存取權限。

## <a name="manage-access"></a>管理存取權

要修改對特定使用者或組的 SSH 存取權限`/etc/ssh/sshd_config`，請在每個節點上更新。

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到群集。 通過將 CLUSTERNAME 替換為群集的名稱來編輯下面的命令，然後輸入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 打開`ssh_confi`g 檔。

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. 根據需要修改`sshd_config`檔。 如果將使用者限制為特定組，則本地帳戶無法將 SSH 放入該節點。 以下只是語法的示例：

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    然後保存更改： **Ctrl = X**， **Y**，**輸入**。

1. 重新開機 sshd。

    ```bash
    sudo systemctl restart sshd
    ```

1. 每個節點重複上述步驟。

## <a name="ssh-authentication-log"></a>SSH 身份驗證日誌

SSH 身份驗證日誌寫入`/var/log/auth.log`。 如果您看到本地或域帳戶通過 SSH 出現任何登錄失敗，則需要通過日誌來調試錯誤。 通常，問題可能與特定使用者帳戶相關，通常最好使用預設 SSH 使用者（本地帳戶）嘗試其他使用者帳戶或 SSH，然後嘗試 kinit。

## <a name="ssh-debug-log"></a>SSH 調試日誌

要啟用詳細日誌記錄，您需要使用 該`sshd``-d`選項重新開機。 就像`/usr/sbin/sshd -d`您也可以在自訂埠`sshd`（如 2222）上運行一樣，您不必停止主 SSH 守護進程。 您還可以使用`-v`SSH 用戶端選項獲取更多日誌（故障的用戶端視圖）。

## <a name="next-steps"></a>後續步驟

* [使用企業安全性套件管理 HDInsight 叢集](./apache-domain-joined-manage.md)
* [使用 SSH 連線到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。
