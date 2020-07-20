---
title: Azure HDInsight：.NET 範例
description: 使用適用於 .NET 的 HDInsight SDK 在 GitHub 上尋找 C# .NET 範例以執行一般工作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 12/06/2019
ms.openlocfilehash: 097323b4566daeda737428121bee4d64e9f7248b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74951559"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight：.NET 範例

> [!div class="op_single_selector"]
> * [.NET 範例](hdinsight-sdk-dotnet-samples.md)
> * [Python 範例](hdinsight-sdk-python-samples.md)
> * [Java 範例](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

本文提供：

* 叢集建立工作範例的連結。
* 其他管理工作參考內容的連結。

您可以 [啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每個月都會提供額度，供您用在 Azure 付費服務。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisite"></a>必要條件

[適用於.NET 的 Azure HDInsight SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>叢集管理 - 建立

* [建立 Kafka 叢集](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [建立 Spark 叢集](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [建立搭配 Azure Data Lake Storage Gen2 的 Spark 叢集](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [使用企業安全性套件 (ESP) 建立 Spark 叢集](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

您可以藉由複製 [hdinsight-dotnet-sdk-samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub 存放庫來取得 .NET 的這些範例。

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

可以在[適用於 .NET 的 HDInsight SDK 參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)中找到此額外 SDK 功能的程式碼片段。
