---
title: C#， 阿帕奇蜂巢&阿帕奇豬在阿帕奇哈多普 - Azure HDInsight
description: 了解如何搭配使用 C# 使用者定義函數 (UDF) 與 Azure HDInsight 中的 Apache Hive 和 Apache Pig 串流處理。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74949384"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>在 HDInsight 中的 Apache Hadoop 上使用 C# 使用者定義的功能與 Apache Hive 和 Apache Pig

瞭解如何在 HDInsight 上使用 C# 使用者定義的函數 （UDF） 與[Apache Hive](https://hive.apache.org)和[Apache Pig。](https://pig.apache.org)

> [!IMPORTANT]
> 本文檔中的步驟與基於 Linux 的 HDInsight 群集配合使用。 Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 元件版本設定](../hdinsight-component-versioning.md)。

Hive 和 Pig 都可以將資料傳遞至外部應用程式進行處理。 這個程序稱為_串流處理_。 使用 .NET 應用程式時，資料會在 STDIN 上傳遞至應用程式，而應用程式會在 STDOUT 上傳回結果。 為了在 STDIN 和 STDOUT 讀取和寫入，您可以從主控台應用程式使用 `Console.ReadLine()` 和 `Console.WriteLine()`。

## <a name="prerequisites"></a>Prerequisites

* 熟悉如何撰寫和建置以 .NET Framework 4.5 為目標的 C# 程式碼。

    使用您想要的任何 IDE。 我們建議[視覺工作室](https://www.visualstudio.com/vs)或[視覺工作室代碼](https://code.visualstudio.com/)。 本文檔中的步驟使用 Visual Studio 2019。

* 將 .exe 檔案上傳至叢集並執行 Pig 和 Hive 作業所採取的方式。 我們建議用於視覺化工作室[、Azure PowerShell](/powershell/azure)和 Azure [CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)[的資料湖工具](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md)。 本文件中的步驟使用 Data Lake Tools for Visual Studio 上傳檔案並執行範例 Hive 查詢。

    有關運行 Hive 查詢的其他方法的資訊，請參閱[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](hdinsight-use-hive.md)

* HDInsight 叢集上的 Hadoop。 有關創建群集的詳細資訊，請參閱[創建 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="net-on-hdinsight"></a>HDInsight 上的 .NET

*以 Linux 為基礎的 HDInsight* 叢集會使用 [Mono (https://mono-project.com)](https://mono-project.com) 來執行 .NET 應用程式。 4.2.1 版的 Mono 隨附於 3.6 版的 HDInsight。

如需 Mono 與 .NET Framework 版本之相容性的詳細資訊，請參閱 [Mono 相容性](https://www.mono-project.com/docs/about-mono/compatibility/) \(英文\)。

有關 HDInsight 版本附帶的 .NET 框架和單聲道版本的詳細資訊，請參閱[HDInsight 元件版本](../hdinsight-component-versioning.md)。

## <a name="create-the-c-projects"></a>建立 C\# 專案

以下各節介紹如何在 Visual Studio 中為 Apache Hive UDF 和 Apache Pig UDF 創建 C# 專案。

### <a name="apache-hive-udf"></a>Apache Hive UDF

要為 Apache 蜂巢 UDF 創建 C# 專案，請進行：

1. 啟動 Visual Studio。

2. 選取 [建立新專案]****。

3. 在 **"創建新專案**"視窗中，選擇**主控台應用 （.NET 框架）** 範本（C# 版本）。 然後選擇 **"下一步**"。

4. 在 **"配置新專案**"視窗中，輸入*HiveCSharp***的專案名稱**，然後導航到或創建**位置**以保存新專案。 然後選擇 **"創建**"。

5. 在視覺化工作室 IDE 中，將*Program.cs*的內容替換為以下代碼：

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. 在功能表列中，選擇 **"生成** > **生成解決方案**"以生成專案。

7. 關閉解決方案。

### <a name="apache-pig-udf"></a>Apache Pig UDF

要為 Apache 蜂巢 UDF 創建 C# 專案，請進行：

1. 開啟 Visual Studio。

2. 在 [開始]**** 視窗中，選取 [建立新專案]****。

3. 在 **"創建新專案**"視窗中，選擇**主控台應用 （.NET 框架）** 範本（C# 版本）。 然後選擇 **"下一步**"。

4. 在 **"配置新專案**"視窗中，輸入*PigUDF***的專案名稱**，然後轉到或創建**位置**以將新專案保存到 中。 然後選擇 **"創建**"。

5. 在視覺化工作室 IDE 中，將*Program.cs*的內容替換為以下代碼：

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    此程式碼將剖析 Pig 送出的程式碼行，並將其重新格式化為以 `java.lang.Exception` 開頭的程式碼行。

6. 在功能表列中，選擇 **"生成** > **生成解決方案**"以生成專案。

7. 保持解決方案打開狀態。

## <a name="upload-to-storage"></a>上傳至儲存體

接下來，將 Hive 和 Pig UDF 應用程式上載到 HDInsight 群集上的存儲。

1. 在視覺化工作室中，導航到**查看** > **伺服器資源管理器**。

1. 從**伺服器資源管理器**中，按右鍵**Azure，** 選擇 **"連接到 Microsoft Azure 訂閱**"，然後完成登錄過程。

1. 展開您要部署此應用程式的 HDInsight 叢集。 就會列出含有文字 **(預設儲存體帳戶)** 的項目。

    ![預設存儲帳戶、HDInsight 群集、伺服器資源管理器](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * 如果可以展開此條目，則使用**Azure 存儲帳戶**作為群集的預設存儲。 若要檢視叢集之預設儲存體上的檔案，請展開項目，然後按兩下 [(預設容器)]****。

    * 如果無法展開此條目，則使用**Azure 資料湖存儲**作為群集的預設存儲。 若要檢視叢集之預設儲存體上的檔案，請按兩下 [(預設儲存體帳戶)]**** 項目。

1. 若要上傳 .exe 檔案，請使用下列其中一種方法：

    * 如果您使用的是 Azure**存儲帳戶**，請選擇 **"上傳 Blob"** 圖示。

        ![HDInsight 上傳新專案圖示](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        在 **"上載新檔**"對話方塊中，在 **"檔案名稱**"下，選擇 **"流覽**"。 在 **"上傳 Blob"** 對話方塊中，轉到*HiveCSharp*專案的*bin_調試*資料夾，然後選擇*HiveCSharp.exe*檔。 最後，選擇 **"打開****"，然後確定**以完成上載。

    * 如果使用**Azure 資料湖存儲**，請按右鍵檔案清單中的空區域，然後選擇"**上載**"。 最後，選擇*HiveCSharp.exe*檔，然後選擇 **"打開**"。

    *HiveCSharp.exe* 上傳完成後，請針對 *PigUDF.exe* 檔案重複上傳程序。

## <a name="run-an-apache-hive-query"></a>執行 Apache Hive 查詢

現在，您可以運行使用 Hive UDF 應用程式的 Hive 查詢。

1. 在視覺化工作室中，導航到**查看** > **伺服器資源管理器**。

2. 展開 [Azure]****，然後展開 [HDInsight]****。

3. 以滑鼠右鍵按一下部署 *HiveCSharp* 應用程式的叢集，然後選取 [撰寫 Hive 查詢]****。

4. 在 Hive 查詢中使用下列文字：

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > 將和使用於您叢集之預設儲存體類型相符的 `add file` 陳述式取消註解。

    此查詢從`clientid``devicemake``hivesampletable`中選擇 、`devicemodel`和 欄位，然後將欄位傳遞給*HiveCSharp.exe*應用程式。 此查詢預期應用程式會傳回儲存為 `clientid`、`phoneLabel` 和 `phoneHash` 的三個欄位。 查詢還希望在預設存儲容器的根目錄中找到*HiveCSharp.exe。*

5. 將預設**的"互動式"** 切換到**批次處理**，然後選擇"**提交"** 以將作業提交到 HDInsight 群集。 [Hive 作業摘要]**** 視窗隨即開啟。

6. 選擇 **"刷新"** 以刷新摘要，直到**作業狀態**更改為 **"已完成**"。 要查看作業輸出，請選擇 **"作業輸出**"。

## <a name="run-an-apache-pig-job"></a>執行 Apache Pig 工作

您還可以運行使用 Pig UDF 應用程式的 Pig 作業。

1. 使用 SSH 連線到 HDInsight 叢集。 （例如，運行命令`ssh sshuser@<clustername>-ssh.azurehdinsight.net`.）有關詳細資訊，請參閱將[SSH 與 HDInsight 一起使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用以下命令啟動 Pig 命令列：

    ```shell
    pig
    ```

    `grunt>` 提示隨即顯示。

3. 輸入下列命令，以執行使用 .NET Framework 應用程式的 Pig 作業：

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    該`DEFINE`語句`streamer`為*PigUDF.exe*應用程式創建別名，並從`CACHE`群集的預設存儲中載入它。 稍後，`streamer`與`STREAM`運算子一起處理中包含的`LOG`單行，並將資料作為一系列列返回。

    > [!NOTE]
    > 用於流式處理的應用程式名稱在別名時必須由\`（回引）字元括起來，在與 中使用 時必須用 '（單引號）字元`SHIP`括起來。

4. 輸入最後一行後，作業應該就會開始。 它會傳回類似下列文字的輸出：

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. 用於`exit`退出豬。

## <a name="next-steps"></a>後續步驟

在本文中，您已經瞭解如何在 HDInsight 上使用來自 Hive 和 Pig 的 .NET 框架應用程式。 如果您想要了解如何搭配使用 Python 與 Hive 和 Pig，請參閱[搭配使用 Python 與 HDInsight 中的 Apache Hive 和 Apache Pig](python-udf-hdinsight.md)。

有關使用 Hive 並瞭解如何使用 MapReduce 的其他方法，請參閱以下文章：

* [搭配 HDInsight 使用 Apache Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 MapReduce](hdinsight-use-mapreduce.md)
* [豬拉丁基礎知識](https://pig.apache.org/docs/latest/basic.html)