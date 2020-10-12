---
title: 管理 Azure HDInsight 中網域帳戶的 SSH 存取
description: 在 HDInsight 中管理 Azure AD 帳戶 SSH 存取的步驟。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 5be992ef8375f98b3c5978d8b71dc92ce9f91123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081497"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>管理 Azure HDInsight 中網域帳戶的 SSH 存取

在安全叢集上，預設會允許 [AZURE AD DS](../../active-directory-domain-services/overview.md) 中的所有網域使用者透過 [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) 連線到前端和邊緣節點。 這些使用者不是 sudoers 群組的一部分，也不會取得根存取權。 在叢集建立期間建立的 SSH 使用者將擁有根存取權。

## <a name="manage-access"></a>管理存取權

若要修改特定使用者或群組的 SSH 存取權，請 `/etc/ssh/sshd_config` 在每個節點上更新。

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)來連線到您的叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 開啟 `ssh_confi` g 檔案。

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

SSH 驗證記錄檔會寫入至 `/var/log/auth.log` 。 如果您透過 SSH 看到本機或網域帳戶的任何登入失敗，您將需要經過記錄檔以檢查錯誤。 問題通常可能與特定的使用者帳戶有關，而且通常最好使用預設的 SSH 使用者 (本機帳戶來嘗試其他使用者帳戶或 SSH) 然後嘗試 kinit。

## <a name="ssh-debug-log"></a>SSH 調試記錄

若要啟用詳細資訊記錄，您必須使用選項來重新開機 `sshd` `-d` 。 如同 `/usr/sbin/sshd -d` 您也可以 `sshd` 在自訂埠上執行 (例如 2222) ，如此您就不需要停止主要 SSH 背景程式。 您也可以搭配 `-v` SSH 用戶端使用選項，以取得更多的記錄 (用戶端) 失敗的觀點。

## <a name="next-steps"></a>接下來的步驟

* [使用企業安全性套件管理 HDInsight 叢集](./apache-domain-joined-manage.md)
* [使用 SSH 連線到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。
