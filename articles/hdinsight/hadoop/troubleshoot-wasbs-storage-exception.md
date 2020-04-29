---
title: 正在存取的帳戶不支援 Azure HDInsight 中的 HTTP 錯誤
description: 本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77165549"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>正在存取的帳戶不支援 Azure HDInsight 中的 HTTP 錯誤

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

收到下列錯誤訊息：

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>原因

收到錯誤訊息的原因有很多：

* 儲存體帳戶已啟用[安全傳輸](../../storage/common/storage-require-secure-transfer.md)，且使用了不正確的[URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。

* 叢集是使用*已停*用安全傳輸的儲存體帳戶所建立。 之後，就會在儲存體帳戶上啟用安全傳輸。

## <a name="resolution"></a>解決方案

如果已啟用 Azure 儲存體或 Data Lake Storage Gen2 的安全傳輸，則 URI 會分別`wasbs://`是`abfss://`或。  另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

針對新的叢集，請使用已具有所需安全傳輸設定的儲存體帳戶。 請勿變更現有叢集所使用之儲存體帳戶的安全傳輸設定。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
