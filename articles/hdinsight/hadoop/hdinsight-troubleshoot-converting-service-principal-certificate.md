---
title: 將憑證內容轉換成 base-64-Azure HDInsight
description: 將服務主體憑證內容轉換成以64為基底的編碼字串格式（在 Azure HDInsight 中）
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894177"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>將服務主體憑證內容轉換成 HDInsight 中的 base-64 編碼字串格式

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

您會收到一則錯誤訊息，指出輸入不是有效的 Base-64 字串，因為它包含非基底64字元、超過兩個填補字元，或在填補字元中有非空白字元。

## <a name="cause"></a>原因

使用 PowerShell 或 Azure 範本部署來建立具有 Data Lake 作為主要或其他儲存體的叢集時，所提供用來存取 Data Lake 儲存體帳戶的服務主體憑證內容會採用 base-64 格式。 不正確地將 pfx 憑證內容轉換成以64編碼的字串，可能會導致此錯誤。

## <a name="resolution"></a>解決方案

當您以 pfx 格式提供服務主體憑證（請參閱[這裡](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)的範例服務主體建立步驟）時，請使用下列 PowerShell 命令或 c # 程式碼片段，將憑證內容轉換成64格式。

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
