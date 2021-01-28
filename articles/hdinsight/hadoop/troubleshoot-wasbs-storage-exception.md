---
title: 所存取的帳戶不支援 Azure HDInsight 中的 HTTP 錯誤
description: 本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943077"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>所存取的帳戶不支援 Azure HDInsight 中的 HTTP 錯誤

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

收到下列錯誤訊息：

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>原因

收到錯誤訊息的原因有很多：

* 儲存體帳戶已啟用 [安全傳輸](../../storage/common/storage-require-secure-transfer.md) ，並使用了不正確的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 。

* 叢集是使用 *已停* 用安全傳輸的儲存體帳戶所建立。 之後，會在儲存體帳戶上啟用安全傳輸。

## <a name="resolution"></a>解決方案

如果已針對 Azure 儲存體或 Data Lake Storage Gen2 啟用安全傳輸，則 URI 會 `wasbs://` 分別為或 `abfss://` 。  另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

針對新的叢集，請使用已具有所需安全傳輸設定的儲存體帳戶。 請勿變更現有叢集所使用之儲存體帳戶的安全傳輸設定。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。