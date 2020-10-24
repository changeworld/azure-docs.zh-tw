---
title: Apache Hadoop & 安全傳輸儲存體-Azure HDInsight
description: 了解如何使用已啟用安全傳輸的 Azure 儲存體帳戶建立 HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 8c1ff1eebabd94c5e6b1241b33ca83960e8eedbb
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490283"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>在 Azure HDInsight 中使用安全傳輸儲存體帳戶的 Apache Hadoop 叢集

[需要安全傳輸](../storage/common/storage-require-secure-transfer.md)功能透過安全連線來強制對您帳戶的所有要求，以增強 Azure 儲存體帳戶的安全性。 只有 HDInsight 叢集 3.6 版或更新版本支援這項功能和 wasbs 配置。

> [!IMPORTANT]
> 在建立叢集之後啟用安全儲存體傳輸，可能會在使用您的儲存體帳戶時發生錯誤，因此不建議使用。 最好是使用已啟用安全傳輸的儲存體帳戶來建立新的叢集。

## <a name="storage-accounts"></a>儲存體帳戶

### <a name="azure-portal"></a>Azure 入口網站

當您在 Azure 入口網站中建立儲存體帳戶時，預設會啟用 [需要安全傳輸] 屬性。

若要使用 Azure 入口網站更新現有的儲存體帳戶，請參閱 [需要使用 Azure 入口網站的安全傳輸](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)。

### <a name="powershell"></a>PowerShell

針對 PowerShell Cmdlet [New-new-azstorageaccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)，請確定參數 `-EnableHttpsTrafficOnly` 設定為 `1` 。

若要使用 PowerShell 來更新現有的儲存體帳戶，請參閱 [使用 Powershell 要求安全傳輸](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)。

### <a name="azure-cli"></a>Azure CLI

針對 Azure CLI 命令 [az storage account create](/cli/azure/storage/account#az-storage-account-create)，請確定參數 `--https-only` 設定為 `true` 。

若要使用 Azure CLI 更新現有的儲存體帳戶，請參閱 [需要使用 Azure CLI 的安全傳輸](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)。

## <a name="add-additional-storage-accounts"></a>新增其他儲存體帳戶

有數個選項可新增其他已啟用安全傳輸的儲存體帳戶：

* 在最後一節中修改 Azure Resource Manager 範本。
* 使用 [Azure 入口網站](https://portal.azure.com)建立叢集並指定連結的儲存體帳戶。
* 使用指令碼動作，將其他已啟用安全傳輸的儲存體帳戶新增至現有的 HDInsight 叢集。 如需詳細資訊，請參閱[將其他儲存體帳戶新增至 HDInsight](hdinsight-hadoop-add-storage.md)。

## <a name="next-steps"></a>後續步驟

* 如何使用「Azure 儲存體」(WASB) (而非 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html))作為預設資料存放區
* 如需有關 HDInsight 如何使用 Azure 儲存體的資訊，請參閱 [搭配 HDInsight 使用 Azure 儲存體](hdinsight-hadoop-use-blob-storage.md)。
* 如需關於如何上傳資料到 HDInsight 的資訊，請參閱[將資料上傳到 HDInsight](hdinsight-upload-data.md)。
