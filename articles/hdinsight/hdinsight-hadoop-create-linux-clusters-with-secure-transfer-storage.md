---
title: 阿帕奇哈多普&安全傳輸存儲 - Azure HDInsight
description: 了解如何使用已啟用安全傳輸的 Azure 儲存體帳戶建立 HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560077"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>在 Azure HDInsight 中具有安全傳輸存儲帳戶的 Apache Hadoop 群集

[需要安全傳輸](../storage/common/storage-require-secure-transfer.md)功能透過安全連線來強制對您帳戶的所有要求，以增強 Azure 儲存體帳戶的安全性。 只有 HDInsight 叢集 3.6 版或更新版本支援這項功能和 wasbs 配置。

> [!IMPORTANT]
> 在創建群集後啟用安全存儲傳輸可能會導致使用存儲帳戶時出錯，不建議這樣做。 最好使用已啟用安全傳輸的存儲帳戶創建新群集。

## <a name="storage-accounts"></a>儲存體帳戶

### <a name="azure-portal"></a>Azure 入口網站

預設情況下，在 Azure 門戶中創建存儲帳戶時啟用所需的安全傳輸屬性。

要使用 Azure 門戶更新現有存儲帳戶，請參閱[使用 Azure 門戶進行安全傳輸](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)。

### <a name="powershell"></a>PowerShell

對於 PowerShell Cmdlet[新存儲帳戶](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)，請確保參數`-EnableHttpsTrafficOnly`設置為`1`。

要使用 PowerShell 更新現有存儲帳戶，請參閱[使用 PowerShell 進行安全傳輸](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)。

### <a name="azure-cli"></a>Azure CLI

對於 Azure CLI 命令[az 存儲帳戶創建](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)，請確保參數`--https-only`設置為`true`。

要使用 Azure CLI 更新現有存儲帳戶，請參閱[使用 Azure CLI 進行安全傳輸](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)。

## <a name="add-additional-storage-accounts"></a>新增其他儲存體帳戶

有數個選項可新增其他已啟用安全傳輸的儲存體帳戶：

* 在最後一節中修改 Azure Resource Manager 範本。
* 使用 [Azure 入口網站](https://portal.azure.com)建立叢集並指定連結的儲存體帳戶。
* 使用指令碼動作，將其他已啟用安全傳輸的儲存體帳戶新增至現有的 HDInsight 叢集。 如需詳細資訊，請參閱[將其他儲存體帳戶新增至 HDInsight](hdinsight-hadoop-add-storage.md)。

## <a name="next-steps"></a>後續步驟

* 如何使用「Azure 儲存體」(WASB) (而非 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html))作為預設資料存放區
* 如需有關 HDInsight 如何使用 Azure 儲存體的資訊，請參閱 [搭配 HDInsight 使用 Azure 儲存體](hdinsight-hadoop-use-blob-storage.md)。
* 如需關於如何上傳資料到 HDInsight 的資訊，請參閱[將資料上傳到 HDInsight](hdinsight-upload-data.md)。
