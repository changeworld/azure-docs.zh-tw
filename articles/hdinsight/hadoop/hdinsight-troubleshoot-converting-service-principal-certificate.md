---
title: 將憑證內容轉換為 base-64-Azure HDInsight
description: 在 Azure HDInsight 中將服務主體憑證內容轉換為 base-64 編碼的字串格式
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944412"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>在 HDInsight 中將服務主體憑證內容轉換為 base-64 編碼字串格式

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

您會收到錯誤訊息，指出輸入不是有效的基底-64 字串，因為它包含非基底64字元、超過兩個填補字元，或填補字元之間有非空白字元。

## <a name="cause"></a>原因

使用 PowerShell 或 Azure 範本部署來建立以主要或其他儲存體 Data Lake 的叢集時，所提供用來存取 Data Lake 儲存體帳戶的服務主體憑證內容是以64為基礎的格式。 不當將 pfx 憑證內容轉換為 base-64 編碼的字串可能會導致此錯誤。

## <a name="resolution"></a>解決方案

當您擁有 pfx 格式的服務主體憑證之後 (請參閱 [這裡](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) 以取得) 的範例服務主體建立步驟，請使用下列 PowerShell 命令或 c # 程式碼片段，將憑證內容轉換為64格式。

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]