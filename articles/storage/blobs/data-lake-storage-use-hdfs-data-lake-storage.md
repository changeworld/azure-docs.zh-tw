---
title: 使用 HDFS CLI 搭配 Azure Data Lake Storage Gen2
description: 適用於 Data Lake Storage Gen2 的 HDFS CLI 簡介
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 36e6b39aaf481abaabe4fb5a4a71a527d1e74749
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109445"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>使用 HDFS CLI 搭配 Data Lake Storage Gen2

您可以使用命令列介面來存取和管理儲存體帳戶中的資料，就像使用[Hadoop 分散式檔案系統（HDFS）](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)一樣。 本文提供一些可協助您開始使用的範例。

HDInsight 提供本機連接至計算節點之分散式容器的存取權。 您可以使用直接與 HDFS 和 Hadoop 支援的其他檔案系統互動的 shell 來存取此容器。

如需 HDFS CLI 的詳細資訊，請參閱[官方檔](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)和[hdfs 許可權指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>如果您使用 Azure Databricks 而非 HDInsight，而且想要使用命令列介面與資料互動，您可以使用 Databricks CLI 與 Databricks 檔案系統互動。 請參閱[DATABRICKS CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)。

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>在 Linux 上搭配使用 HDFS CLI 和 HDInsight Hadoop 叢集

首先，建立[服務的遠端存取](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)。 如果您挑選 [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)，則範例 PowerShell 程式碼會看起來像這樣：

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
您可以在 Azure 入口網站中 HDInsight 叢集刀鋒視窗的「SSH + 叢集登入」區段找到連接字串。 在叢集建立時，就已指定 SSH 認證。

>[!IMPORTANT]
>HDInsight 叢集的計費，是從建立叢集之後開始算起，並於叢集被刪除時停止計算。 計費是以每分鐘按比例計算，因此不再使用時，請一律刪除您的叢集。 若要了解如何刪除叢集，請參閱我們[針對該主題的文章](../../hdinsight/hdinsight-delete-cluster.md)。 不過，儲存在已啟用 Data Lake Storage Gen2 之儲存體帳戶中的資料，即使在 HDInsight 叢集被刪除後仍會存在。

## <a name="create-a-container"></a>建立容器

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

* 請將 `<container-name>` 預留位置取代為您要為容器指定的名稱。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置。

## <a name="get-a-list-of-files-or-directories"></a>取得檔案清單或目錄

`hdfs dfs -ls <path>`

將預留位置取代為 `<path>` 容器或容器檔案夾的 URI。

例如： `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>建立目錄

`hdfs dfs -mkdir [-p] <path>`

將預留位置取代為 `<path>` 根容器名稱或容器中的資料夾。

例如： `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>刪除檔案或目錄

`hdfs dfs -rm <path>`

將 `<path>` 預留位置取代為您想要刪除之檔案或資料夾的 URI。

例如： `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>顯示檔案和目錄的存取控制清單 (ACL)

`hdfs dfs -getfacl [-R] <path>`

範例：

`hdfs dfs -getfacl -R /dir`

請參閱 [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>設定檔案和目錄的 ACL

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

範例：

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

請參閱 [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>變更檔案的擁有者

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

請參閱 [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>變更檔案的群組關聯

`hdfs dfs -chgrp [-R] <group> <URI>`

請參閱 [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>變更檔案的權限

`hdfs dfs -chmod [-R] <mode> <URI>`

請參閱 [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

您可以在 [Apache Hadoop 2.4.1 檔案系統殼層指南](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)網站上檢視完整的命令清單。

## <a name="next-steps"></a>後續步驟

* [在 Azure Databricks 中使用具有 Azure Data Lake Storage Gen2 功能的帳戶](./data-lake-storage-quickstart-create-databricks-account.md)

* [瞭解檔案和目錄的存取控制清單](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
