---
title: 教學課程：適用於 Spark & IntelliJ 的 Scala Maven 應用程式 - Azure HDInsight
description: 教學課程 - 建立以 Scala 撰寫的 Spark 應用程式，並以 Apache Maven 作為組建系統。 以及 IntelliJ IDEA 為 Scala 提供的現有 Maven 原型。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: contperfq1
ms.date: 08/21/2020
ms.openlocfilehash: 02192f3960b8021ee32203620893620c7b96f076
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539440"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>教學課程：使用 IntelliJ 為 HDInsight 中的 Apache Spark 建立 Scala Maven 應用程式

在本教學課程中，您將了解如何使用 Apache Maven 與 IntelliJ IDEA 建立以 Scala 撰寫的 Apache Spark 應用程式。 本文會使用 Apache Maven 作為組建系統。 並從 IntelliJ IDEA 為 Scala 提供的現有 Maven 原型開始。  要在 IntelliJ IDEA 中建立 Scala 應用程式，必須執行下列步驟：

* 以 Maven 做為建置系統。
* 更新專案物件模型 (POM) 檔案，以解析 Spark 模組相依性。
* 以 Scala 撰寫應用程式。
* 產生可提交至 HDInsight Spark 叢集的 jar 檔案。
* 使用 Livy 在 Spark 叢集上執行應用程式。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 安裝 IntelliJ IDEA 的 Scala 外掛程式
> * 使用 IntelliJ 開發 Scala Maven 應用程式
> * 建立獨立 Scala 專案

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

* [Oracle Java Development Kit](https://www.azul.com/downloads/azure-only/zulu/)。  本教學課程使用 Java 8.0.202 版。

* Java IDE。 本文使用 [IntelliJ IDEA Community 版本2018.3.4](https://www.jetbrains.com/idea/download/)。

* Azure Toolkit for IntelliJ。  請參閱[安裝 Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in)。

## <a name="install-scala-plugin-for-intellij-idea"></a>安裝 IntelliJ IDEA 的 Scala 外掛程式

執行下列步驟來安裝 Scala 外掛程式：

1. 開啟 IntelliJ IDEA。

2. 在歡迎使用畫面上，瀏覽至 [設定]   > [外掛程式]  以開啟 [外掛程式]  視窗。

    ![「IntelliJ IDEA 啟用 Scala 外掛程式」](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. 針對新視窗中精選的 Scala 外掛程式，選取 [安裝]  。  

    ![「IntelliJ IDEA 安裝 Scala 外掛程式」](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. 在外掛程式安裝成功後，您必須重新啟動 IDE。

## <a name="use-intellij-to-create-application"></a>使用 IntelliJ 建立應用程式

1. 啟動 IntelliJ IDEA，然後選取 [建立新專案]  來開啟 [新增專案]  視窗。

2. 選取左窗格中的 [Apache Spark/HDInsight]  。

3. 選取主視窗中的 [Spark 專案 (Scala)]  。

4. 從 [建置工具]  下拉式清單中，選取下列其中一個值：
      * **Maven** ：建立 Scala 專案精靈支援。
      * **SBT** ：可供管理相依性並建置 Scala 專案。

   ![IntelliJ 的新增專案對話方塊](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. 選取 [下一步]  。

6. 在 [新增專案]  視窗中，提供下列資訊：  

  	|  屬性   | 描述   |  
  	| ----- | ----- |  
  	|專案名稱| 輸入名稱。|  
  	|專案&nbsp;位置| 輸入要儲存您專案的位置。|
  	|專案 SDK| 您第一次使用 IDEA 時，此欄位會是空白的。  選取 [新增...]  並瀏覽至您的 JDK。|
  	|Spark 版本|建立精靈會為 Spark SDK 和 Scala SDK 整合正確的版本。 如果 Spark 叢集版本早於 2.0，請選取 [Spark 1.x]  。 否則，請選取 [Spark 2.x]  。 此範例使用 **Spark 2.3.0 (Scala 2.11.8)** 。|

    ![IntelliJ IDEA 選取 Spark SDK](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. 選取 [完成]  。

## <a name="create-a-standalone-scala-project"></a>建立獨立 Scala 專案

1. 啟動 IntelliJ IDEA，然後選取 [建立新專案]  來開啟 [新增專案]  視窗。

2. 選取左窗格中的 [Maven]  。

3. 指定 [專案 SDK]  。 如果空白，請選取 [新增...]  ，然後瀏覽至 Java 安裝目錄。

4. 選取 [從原型建立]  核取方塊。  

5. 從 archetype 清單中選取 [`org.scala-tools.archetypes:scala-archetype-simple` ]。 此 archetype 會建立正確的目錄結構，並下載撰寫 Scala 程式所需的預設相依性。

    ![此螢幕擷取畫面顯示 [新增專案] 視窗中選取的原型。](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. 選取 [下一步]  。

7. 展開 [成品座標]  。 提供 [GroupId]  和 [ArtifactId]  的相關值。 [名稱]  和 [位置]  會自動填入。 本教學課程中使用下列值：

    - **GroupId：** com.microsoft.spark.example
    - **ArtifactId：** SparkSimpleApp

    ![此螢幕擷取畫面顯示 [新增專案] 視窗中的 [成品座標] 選項。](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. 選取 [下一步]  。

9. 確認設定，然後選取 [下一步]  。

10. 確認專案名稱和位置，然後按一下 [完成]  。  專案需要幾分鐘才能匯入。

11. 匯入專案後，從左窗格瀏覽至 **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **microsoft** > **spark** > **example** 。  以滑鼠右鍵按一下 **MySpec** ，然後選取 [刪除...]  。應用程式並不需要此檔案。  在對話方塊中選取 [確定]  。
  
12. 在稍後的步驟中，您會更新 **pom.xml** ，以定義 Spark Scala 應用程式的相依性。 若要自動下載並解析這些相依性，您必須設定 Maven。

13. 在 [檔案]  功能表中，選取 [設定]  以開啟 [設定]  視窗。

14. 從 [設定]  視窗，瀏覽至 [建置、執行、部署]   > [建置工具]   > [Maven]   > [匯入]  。

15. 選取 [自動匯入 Maven 專案]  核取方塊。

16. 選取 [套用]  ，然後選取 [確定]  。  您接著會返回專案視窗。

    ![設定 Maven 以進行自動下載](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. 從左窗格瀏覽至 **src** > **main** > **scala** > **com.microsoft.spark.example** ，然後按兩下 [應用程式]  以開啟 App.scala。

18. 將現有的範例程式碼取代為下列程式碼，然後儲存變更。 此程式碼會從 HVAC.csv (所有 HDInsight Spark 叢集上均有提供) 讀取資料。 擷取在第六個資料行中只有一個數字的資料列。 以即將輸出寫入叢集預設儲存體容器下的 **/HVACOut** 。

    ```scala
    package com.microsoft.spark.example

    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    /**
      * Test IO to wasb
      */
    object WasbIOTest {
        def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACout")
        }
    }
    ```

19. 在左窗格中按兩下 **pom.xml** 。  

20. 在 `<project>\<properties>` 內新增下列區段：

    ```xml
    <scala.version>2.11.8</scala.version>
    <scala.compat.version>2.11.8</scala.compat.version>
    <scala.binary.version>2.11</scala.binary.version>
    ```

21. 在 `<project>\<dependencies>` 內新增下列區段：

    ```xml
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_${scala.binary.version}</artifactId>
        <version>2.3.0</version>
    </dependency>
    ```

    儲存 pom.xml 的變更。

22. 建立 .jar 檔案。 IntelliJ IDEA 允許將 JAR 建立為專案的構件。 請執行下列步驟。

    1. 從 [檔案]  功能表，選取 [專案結構...]  。

    2. 從 [專案結構]  視窗，瀏覽至 [成品]   > **加號 +**  > [JAR]   > [從具有相依性的模組...]  。

        ![「IntelliJ IDEA 專案結構新增 jar」](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. 在 [從模組建立 JAR]  視窗中，選取 [主要類別]  文字方塊中的資料夾圖示。

    4. 在 [選取主要類別]  視窗中，選取依預設出現的類別，然後選取 [確定]  。

        ![「IntelliJ IDEA 專案結構選取類別」](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. 在 [從模組建立 JAR]  視窗中，確定已選取 [擷取至目標 JAR]  選項，然後選取 [確定]  。  此設定會建立具有所有相依性的單一 JAR。

        ![IntelliJ IDEA 專案結構 jar 來自模組](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. [輸出配置]  索引標籤會列出所有納入 Maven 專案中的 jar。 您可以選取並刪除 Scala 應用程式未直接依存的 jar。 對於您在此處建立的應用程式，您可以移除最後一個 ( **SparkSimpleApp 編譯輸出** ) 以外的所有項目。 選取要刪除的 jar，然後選取負號 **-** 。

        ![「IntelliJ IDEA 專案結構刪除輸出」](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        確定已選取 [包含在專案組建中]  核取方塊。 此選項可確保在每次建立或更新專案時都會建立 jar。 依序選取 [套用]  和 [確定]  。

    7. 若要建立 jar，請瀏覽至 [建置]   > [建置成品]   > [建置]  。 專案會在大約 30 秒內編譯。  輸出 jar 會建立在 **\out\artifacts** 下。

        ![IntelliJ IDEA 專案成品輸出](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>在 Apache Spark 叢集上執行應用程式

若要在叢集上執行應用程式，您可以使用下列方法：

* **將應用程式 jar 複製到與叢集相關聯的 Azure 儲存體 Blob** 。 您可以使用命令列公用程式 **AzCopy** 來執行此動作。 另外也有很多用戶端可用來上傳資料。 [在 HDInsight 上將 Apache Hadoop 作業的資料上傳](../hdinsight-upload-data.md)中可找到其詳細資訊。

* **使用 Apache Livy 從遠端提交應用程式作業至** Spark 叢集。 HDInsight 上的 Spark 叢集包含會公開 REST 端點以從遠端提交 Spark 作業的 Livy。 如需詳細資訊，請參閱 [搭配 HDInsight 上的 Spark 叢集利用 Apache Livy 遠端提交 Apache Spark 作業](apache-spark-livy-rest-interface.md)。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您所建立的叢集：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在頂端的 [搜尋]  方塊中，輸入 **HDInsight** 。

1. 在 [服務]  底下，選取 [HDInsight 叢集]  。

1. 從出現的 HDInsight 叢集清單中，在您為本教學課程建立的叢集旁選取 [...]  。

1. 選取 [刪除]  。 選取 [是]  。

![「HDInsight Azure 入口網站刪除叢集」](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "刪除 HDInsight 叢集")

## <a name="next-step"></a>後續步驟

在本文中，您已了解如何建立 Apache Spark Scala 應用程式。 前往下篇文章，了解如何使用 Livy 在 HDInsight Spark 叢集上執行此應用程式。

> [!div class="nextstepaction"]
>[利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](./apache-spark-livy-rest-interface.md)