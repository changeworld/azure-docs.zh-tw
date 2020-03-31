---
title: 使用 .NET 和 HDInsight 執行 Apache Sqoop 作業 - Azure
description: 瞭解如何使用 HDInsight .NET SDK 在 Apache Hadoop 群集和 Azure SQL 資料庫之間運行 Apache Sqoop 導入和匯出。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157057"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>在 HDInsight 中使用 .NET SDK for Apache Hadoop 執行 Apache Sqoop 作業

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

瞭解如何使用 Azure HDInsight .NET SDK 在 HDInsight 中運行 Apache Sqoop 作業，以在 HDInsight 群集和 Azure SQL 資料庫或 SQL Server 資料庫之間導入和匯出。

## <a name="prerequisites"></a>Prerequisites

* 完成[設置測試環境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)從[使用阿帕奇與Hadoop在HDInsight。](./hdinsight-use-sqoop.md)

* [視覺工作室](https://visualstudio.microsoft.com/vs/community/)。

* 熟悉 Sqoop。 有關詳細資訊，請參閱[Sqoop 使用者指南](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)。

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>使用 .NET SDK 在 HDInsight 叢集上使用 Sqoop

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 使用 HDInsight 叢集。 在本節中，您將創建一個 C# 主控台應用程式，`hivesampletable`以匯出到從先決條件創建的 Azure SQL 資料庫表。

## <a name="set-up"></a>設定

1. 啟動視覺化工作室並創建 C# 主控台應用程式。

1. 導航到**工具** > **NuGet 包** > **管理器管理器主控台**並運行以下命令：

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop export

從蜂巢到 SQL 伺服器。  本示例將資料從 Hive`hivesampletable`表匯出到`mobiledata`SQL 資料庫中的表。

1. 在Program.cs檔中使用以下代碼。 編輯代碼以設置`ExistingClusterName`和 的值。 `ExistingClusterPassword`

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. 若要執行程式，請選取 **F5** 鍵。

## <a name="sqoop-import"></a>Sqoop import

從 SQL 伺服器到 Azure 存儲。 此示例取決於已執行的上述匯出。  本示例將資料從 SQL`mobiledata`資料庫中的表導入群集`wasb:///tutorials/usesqoop/importeddata`預設存儲帳戶上的目錄。

1. 將塊中的`//sqoop start //sqoop end`上述代碼替換為以下代碼：

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. 若要執行程式，請選取 **F5** 鍵。

## <a name="limitations"></a>限制

以 Linux 為基礎的 HDInsight 存在下列限制：

* 批量匯出：用於將資料匯出到 Microsoft SQL Server 或 Azure SQL 資料庫的 Sqoop 連接器當前不支援批量插入。

* 批次處理：通過使用開關，Sqoop`-batch`執行多個插入，而不是批次處理插入操作。

## <a name="next-steps"></a>後續步驟

現在，您已經學會了如何使用 Sqoop。 若要深入了解，請參閱：

* [將 Apache Oozie 與 HDInsight](../hdinsight-use-oozie-linux-mac.md)一起使用 ： 在 Oozie 工作流中使用 Sqoop 操作。
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)：尋找其他方法將資料上傳至 HDInsight 或 Azure Blob 儲存體。
