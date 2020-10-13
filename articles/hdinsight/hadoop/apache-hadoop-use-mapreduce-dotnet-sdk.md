---
title: 使用 HDInsight .NET SDK 提交 MapReduce 工作 - Azure
description: 了解如何使用 HDInsight .NET SDK 將 MapReduce 作業提交至 Azure HDInsight Apache Hadoop。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 3de7101eb998d0f9b3b57afba6d19595738cbb63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020400"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK 執行 MapReduce 作業

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

了解如何使用 HDInsight .NET SDK 提交 MapReduce 作業。 HDInsight 叢集隨附內含一些 MapReduce 範例的 jar 檔案。 Jar 檔案為 `/example/jars/hadoop-mapreduce-examples.jar` 。  其中一個範例是 wordcount****。 您可開發 C# 主控台應用程式以提交 wordcount 作業。  作業會讀取檔案 `/example/data/gutenberg/davinci.txt` ，並將結果輸出至 `/example/data/davinciwordcount` 。  如果您想要重新執行應用程式，您必須清除輸出資料夾。

> [!NOTE]  
> 此文章中的步驟必須從 Windows 用戶端執行。 如需搭配 Linux、OS X 或 Unix 用戶端使用 Hive 的資訊，請使用本文頂端顯示的索引標籤選取器。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/)。

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK 提交 MapReduce 工作

HDInsight .NET SDK 提供 .NET 用戶端程式庫，可讓您更輕鬆地從 .NET 使用 HDInsight 叢集。

1. 啟動 Visual Studio，然後建立 c # 主控台應用程式。

1. 流覽至 [**工具**]  >  **NuGet 封裝管理員**  >  **封裝管理員主控台**，然後輸入下列命令：

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. 將下列程式碼複製到 **Program.cs**中。 然後藉由設定下列值來編輯程式碼： `existingClusterName` 、、 `existingClusterPassword` `defaultStorageAccountName` 、 `defaultStorageAccountKey` 和 `defaultStorageContainerName` 。

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

1. 按 **F5** 執行應用程式。

若要再次執行作業，您必須在範例中變更作業輸出檔案夾名稱 `/example/data/davinciwordcount` 。

當工作順利完成時，應用程式會列印輸出檔案的內容 `part-r-00000` 。

## <a name="next-steps"></a>後續步驟

在本文中，您學到幾種建立 HDInsight 叢集的方法。 如需詳細資訊，請參閱下列文章：

* 對於提交 Hive 作業，請參閱[使用 HDInsight.NET SDK 執行 Apache Hive 查詢](apache-hadoop-use-hive-dotnet-sdk.md)。
* 如需建立 HDInsight 叢集，請參閱[在 HDInsight 中建立 Linux 型 Apache Hadoop 叢集](../hdinsight-hadoop-provision-linux-clusters.md)。
* 如需管理 HDInsight 叢集，請參閱[在 HDInsight 中管理 Apache Hadoop 叢集](../hdinsight-administer-use-portal-linux.md)。
* 如需了解 HDInsight .NET SDK，請參閱 [HDInsight .NET SDK 參考](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)。
* 若要向 Azure 進行非互動式驗證，請參閱[建立非互動式驗證 .NET HDInsight 應用程式](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)。
