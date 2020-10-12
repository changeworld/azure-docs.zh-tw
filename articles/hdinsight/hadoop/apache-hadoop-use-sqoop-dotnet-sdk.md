---
title: 使用 .NET 和 HDInsight 執行 Apache Sqoop 作業 - Azure
description: 瞭解如何使用 HDInsight .NET SDK 執行 apache Hadoop 叢集與 Azure SQL Database 之間的 Apache Sqoop 匯入和匯出。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: bc4b3019cb8308ea19d86eea7bf886063ea28563
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89008279"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>在 HDInsight 中使用 .NET SDK for Apache Hadoop 執行 Apache Sqoop 作業

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

瞭解如何使用 Azure HDInsight .NET SDK 在 HDInsight 中執行 Apache Sqoop 作業，以在 HDInsight 叢集與 Azure SQL Database 或 SQL Server 資料庫之間進行匯入和匯出。

## <a name="prerequisites"></a>必要條件

* 透過[搭配使用 Apache Sqoop 與 HDInsight 中的 Hadoop](./hdinsight-use-sqoop.md)完成[設定測試環境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)。

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/)。

* 熟悉 Sqoop。 如需詳細資訊，請參閱 [Sqoop 使用者指南](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)。

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>使用 .NET SDK 在 HDInsight 叢集上使用 Sqoop

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 使用 HDInsight 叢集。 在本節中，您會建立 c # 主控台應用程式，以將匯出 `hivesampletable` 至您從必要條件建立的 Azure SQL Database 資料表。

## <a name="set-up"></a>設定

1. 啟動 Visual Studio，然後建立 c # 主控台應用程式。

1. 流覽至 [**工具**]  >  **NuGet 封裝管理員**  >  **封裝管理員主控台**，然後執行下列命令：

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop export

從 Hive 到 SQL Server。  此範例會將 Hive `hivesampletable` 資料表的資料匯出至 SQL Database 中的 `mobiledata` 資料表。

1. 在 Program.cs 檔案中使用下列程式碼。 編輯程式碼，以設定 `ExistingClusterName` 、和的值 `ExistingClusterPassword` 。

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

從 SQL Server 到 Azure 儲存體。 此範例相依于已執行的上述匯出。  此範例會將 SQL Database 資料表中的資料匯入 `mobiledata` 至叢集 `wasb:///tutorials/usesqoop/importeddata` 預設儲存體帳戶上的目錄。

1. 將區塊中的程式碼取代為 `//sqoop start //sqoop end` 下列程式碼：

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

* 大量匯出：用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database 的 Sqoop 連接器目前不支援大量插入。

* 批次處理：藉由使用 `-batch` 參數，Sqoop 會執行多個插入，而不是批次處理插入作業。

## <a name="next-steps"></a>後續步驟

現在，您已了解如何使用 Sqoop。 若要深入了解，請參閱：

* [使用 Apache Oozie 搭配 HDInsight](../hdinsight-use-oozie-linux-mac.md)：在 Oozie 工作流程中使用 Sqoop 動作。
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)：尋找其他方法以將資料上傳至 HDInsight 或 Azure Blob 儲存體。
