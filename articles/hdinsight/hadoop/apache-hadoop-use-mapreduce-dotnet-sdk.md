---
title: 使用 HDInsight .NET SDK 提交 MapReduce 工作 - Azure
description: 了解如何使用 HDInsight .NET SDK 將 MapReduce 作業提交至 Azure HDInsight Apache Hadoop。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157044"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK 執行 MapReduce 作業

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

了解如何使用 HDInsight .NET SDK 提交 MapReduce 作業。 HDInsight 叢集隨附內含一些 MapReduce 範例的 jar 檔案。 jar 檔為`/example/jars/hadoop-mapreduce-examples.jar`。  其中一個範例是 wordcount****。 您可開發 C# 主控台應用程式以提交 wordcount 作業。  作業讀取`/example/data/gutenberg/davinci.txt`檔，並將結果輸出到`/example/data/davinciwordcount`。  如果您想要重新執行應用程式，您必須清除輸出資料夾。

> [!NOTE]  
> 此文章中的步驟必須從 Windows 用戶端執行。 如需搭配 Linux、OS X 或 Unix 用戶端使用 Hive 的資訊，請使用本文頂端顯示的索引標籤選取器。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[使用 Azure 門戶創建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

* [視覺工作室](https://visualstudio.microsoft.com/vs/community/)。

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK 提交 MapReduce 工作

HDInsight .NET SDK 提供 .NET 用戶端庫，這使得使用 .NET 的 HDInsight 群集變得更加容易。

1. 啟動視覺化工作室並創建 C# 主控台應用程式。

1. 導航到**工具** > **NuGet 包** > **管理器管理器主控台**並輸入以下命令：

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. 將下面的代碼複製到**Program.cs**。 然後，`existingClusterName`通過設置： 、 `existingClusterPassword` `defaultStorageAccountName`、`defaultStorageAccountKey`和`defaultStorageContainerName`的值來編輯代碼。

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);
    
                System.Console.WriteLine("Waiting for the job completion ...");
    
                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }
    
                // Get job output
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. 按**F5**以運行應用程式。

要再次運行作業，必須更改作業輸出檔案夾名稱，在示例中是`/example/data/davinciwordcount`。

作業成功完成後，應用程式將列印輸出檔案`part-r-00000`的內容。

## <a name="next-steps"></a>後續步驟

在本文中，您學到幾種建立 HDInsight 叢集的方法。 如需詳細資訊，請參閱下列文章：

* 對於提交 Hive 作業，請參閱[使用 HDInsight.NET SDK 執行 Apache Hive 查詢](apache-hadoop-use-hive-dotnet-sdk.md)。
* 如需建立 HDInsight 叢集，請參閱[在 HDInsight 中建立 Linux 型 Apache Hadoop 叢集](../hdinsight-hadoop-provision-linux-clusters.md)。
* 如需管理 HDInsight 叢集，請參閱[在 HDInsight 中管理 Apache Hadoop 叢集](../hdinsight-administer-use-portal-linux.md)。
* 如需了解 HDInsight .NET SDK，請參閱 [HDInsight .NET SDK 參考](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)。
* 若要向 Azure 進行非互動式驗證，請參閱[建立非互動式驗證 .NET HDInsight 應用程式](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)。