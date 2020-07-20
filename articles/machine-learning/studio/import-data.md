---
title: 匯入訓練資料
titleSuffix: ML Studio (classic) - Azure
description: 如何將資料從各種資料來源匯入 Azure Machine Learning Studio （傳統）。 了解支援的資料類型和資料格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: fa031e6e23c218ff2ca8e2ef3e778768fabb1ebb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85321470"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>從各種資料來源將定型資料匯入 Azure Machine Learning Studio （傳統）

若要在 Machine Learning Studio （傳統）中使用您自己的資料來開發和定型預測性分析解決方案，您可以使用來自下列來源的資料： 

* **本機檔案** - 事先從硬碟載入本機資料，以在工作區中建立資料集模組
* **線上資料來源** - 使用[匯入資料][import-data]模組，在實驗進行時從數個線上來源之一存取資料
* **Machine Learning Studio （傳統）實驗**-使用在 Machine Learning Studio 中儲存為資料集的資料（傳統）
* [**SQL Server 資料庫**](use-data-from-an-on-premises-sql-server.md)-使用 SQL Server 資料庫中的資料，而不需要手動複製資料

> [!NOTE]
> Machine Learning Studio （傳統）提供一些可用於定型資料的範例資料集。 如需相關資訊，請參閱[在 Azure Machine Learning Studio 中使用範例資料集（傳統）](use-sample-datasets.md)。

## <a name="prepare-data"></a>準備資料

Machine Learning Studio （傳統）是設計用來處理矩形或表格式資料，例如從資料庫分隔或結構化資料的文字資料，但在某些情況下，可能會使用非矩形資料。

在您將資料匯入 Studio （傳統）之前，最好先將它放在更清楚的情況。 例如，您想要處理沒有引號的字串之類的問題。

不過，Studio （傳統）中有可用的模組，可讓您在匯入資料之後，對實驗中的資料進行一些操作。 依據您使用的機器學習演算法，您可能需要決定如何處理資料結構問題，例如遺漏值和疏鬆資料，有模組可以協助處理。 請參閱模組調色盤的 **資料轉換** 區段，以取得執行這些功能的模組。

您可以在實驗的任何位置，檢視或下載模組產生的資料，方法是按一下輸出連接埠。 視模組而定，可能會有不同的下載選項可供使用，或者您可以在 Studio （傳統）中的網頁瀏覽器中將資料視覺化。

## <a name="supported-data-formats-and-data-types"></a>支援的資料格式和資料類型

視您用來匯入資料的機制及其來源而定，您可以將一些資料類型匯入您的實驗：

* 純文字 (.txt)
* 逗號分隔值 (CSV)，具有標頭 (.csv) 或不具標頭 (.nh.csv)
* 定位鍵分隔值 (TSV)，具有標頭 (.tsv) 或不具標頭 (.nh.tsv)
* Excel 檔案
* Azure 資料表
* Hive 資料表
* SQL 資料庫資料表
* OData 值
* SVMLight 資料 (.svmlight) (請參閱 [SVMLight 定義](http://svmlight.joachims.org/) 以了解格式資訊)
* 屬性關係檔案格式 (ARFF) 資料 (.arff) (請參閱 [ARFF 定義](https://weka.wikispaces.com/ARFF) 以了解格式資訊)
* Zip 檔案 (.zip)
* R 物件或工作區檔案 (.RData)

如果您以包含中繼資料的 ARFF 格式匯入資料，Studio （傳統）會使用此中繼資料來定義每個資料行的標題和資料類型。

如果您匯入的資料（例如 TSV 或 CSV 格式，但不包含此中繼資料），Studio （傳統）會藉由取樣資料來推斷每個資料行的資料類型。 如果資料也沒有資料行標題，Studio （傳統）會提供預設名稱。

您可以使用[編輯中繼資料][edit-metadata]模組，明確指定或變更資料行的標題和資料類型。

Studio 可辨識下列資料類型（傳統）：

* String
* 整數
* Double
* 布林值
* DateTime
* TimeSpan

Studio 使用名為***資料表格***的內部資料類型以在模組之間傳遞資料。 您可使用[轉換為資料集][convert-to-dataset]模組，以明確地將資料轉換為「資料表」格式。

接受「資料表格」以外格式的任何模組，會在將資料傳遞至下一個模組之前，無訊息地將資料轉換為「資料表格」。

您可以視需要使用其他轉換模組，將「資料表格」格式轉換回 CSV、TSV、ARFF 或 SVMLight 格式。
請參閱模組調色盤的 **資料格式轉換** 區段，以取得執行這些功能的模組。

## <a name="data-capacities"></a>資料容量

Machine Learning Studio （傳統）中的模組支援一般使用案例中，最多可有 10 GB 密集數值資料的資料集。 如果模組接受一個以上的輸入，10 GB 值是所有輸入的大小總計。 您可以使用 Hive 或 Azure SQL Database 查詢來取樣較大型資料集，或者在您匯入資料之前使用「以計數學習」前置處理。  

下列資料類型可以在功能正規化期間擴充為較大型資料集，並限制在小於 10 GB：

* 疏鬆
* 類別
* 字串
* 二進位資料

下列模組限制在小於 10 GB 的資料集：

* Recommender 模組
* 合成少數類過採樣技術 (SMOTE) 模組
* 指令碼模組：R、Python SQL
* 輸出資料大小可以大於輸入資料大小的模組，例如聯結或功能雜湊
* 當反覆運算數目非常大時的交叉驗證、微調模型超參數、序數迴歸和一對多的多類別

針對大於 2 GB 的資料集，請將資料上傳至 Azure 儲存體或 Azure SQL Database，或是使用 Azure HDInsight，而不要直接從本機檔案上傳。

您可以在[匯入影像](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes)模組參考中找到影像資料的相關資訊。

## <a name="import-from-a-local-file"></a>從本機檔案匯入

您可以從硬碟上傳資料檔案，以作為 Studio （傳統）中的定型資料。 匯入資料檔案後，您建立的資料集模組便可以在工作區的實驗中使用。

若要從本機硬碟匯入資料，請執行下列作業︰

1. 按一下 [Studio （傳統）] 視窗底部的 [ **+ 新增**]。
2. 選取 [資料集]**** 和 [從本機檔案]****。
3. 在 [**上傳新的資料集**] 對話方塊中，流覽至您要上傳的檔案。
4. 輸入名稱、識別資料類型，然後選擇性地輸入說明。 建議輸入說明 - 它可以讓您記錄資料的任何特性，您會希望在未來使用資料時記住這些特性。
5. 核取方塊 [ **這是現有資料集的新版本** ] 可讓您使用新資料更新現有資料集。 若要這麼做，可按一下此核取方塊然後輸入現有資料集的名稱即可。

![上傳新的資料集](./media/import-data/upload-dataset-from-local-file.png)

上傳時間取決於資料大小和連接至服務的速度。 如果您知道檔案會花很長的時間，您可以在等候時，在 Studio （傳統）內執行其他動作。 但是，資料上傳完成前關閉瀏覽器會導致上傳失敗。

資料上傳之後，會儲存在資料集模組中，並且可供工作區中的任何實驗使用。

當您在編輯實驗時，您可以在模組調色盤的 [儲存的資料集]**** 清單底下的 [我的資料集]**** 清單內，找到您上傳的資料集。 當您想將資料集用於進一步的分析和機器學習時，可以在實驗畫布上拖放資料集。

## <a name="import-from-online-data-sources"></a>從線上資料來源匯入

使用[匯入資料][import-data]模組時，可以在實驗進行的情況下從各種線上資料來源匯入資料。

> [!NOTE]
> 本文提供有關[匯入資料][import-data]模組的一般資訊。 如需您可以存取的資料類型、格式、參數及常見問題解答的詳細資訊，請參閱[匯入資料][import-data]模組的模組參考主題。

使用[匯入資料][import-data]模組，在實驗進行時，從數個線上資料來源的其中之一存取資料：

* 使用 HTTP 的 Web URL
* 使用 HiveQL 的 Hadoop
* Azure Blob 儲存體
* Azure 資料表
* Azure SQL Database。 SQL 受控執行個體或 SQL Server
* 資料摘要提供者，目前為 OData
* Azure Cosmos DB

這項訓練資料會在您的實驗執行時存取，因此只有在該實驗中才可使用。 相較之下，已儲存在資料集模組中的資料則可供工作區中的任何實驗使用。

若要在您的 Studio （傳統）實驗中存取線上資料來源，請將匯[入資料][import-data]模組新增至您的實驗。 然後選取 [屬性]**** 底下的 [啟動匯入資料精靈]****，取得選取和設定資料來源的逐步引導指示。 或者，您也可以手動選取 [屬性]**** 底下的 [資料來源]****，並提供存取資料所需的參數。

下表列舉支援的線上資料來源。 此表格也會摘錄支援的檔案格式和用來存取資料的參數。

> [!IMPORTANT]
> [匯入資料][import-data]和[匯出資料][export-data]模組目前只能從使用傳統部署模型所建立的 Azure 儲存體讀取和寫入資料。 換句話說，目前尚未支援可提供經常性儲存體存取層或非經常性儲存體存取層的新 Azure Blob 儲存體帳戶類型。
>
> 一般而言，任何您可能已在這個服務選項變成可供使用之前建立的 Azure 儲存體帳戶應該不會受到影響。
> 如果您需要建立新的帳戶，請將部署模型選取為 [傳統]**** 或使用資源管理員，然後將 [帳戶種類]**** 選取為 [一般用途]**** 而不是 [Blob 儲存體]****。
>
> 如需詳細資訊，請參閱 [Azure Blob 儲存體︰經常性存取與非經常性存取儲存層](../../storage/blobs/storage-blob-storage-tiers.md)。

### <a name="supported-online-data-sources"></a>支援的線上資料來源
Azure Machine Learning Studio （傳統）匯**入資料**模組支援下列資料來源：

| 資料來源 | 描述 | 參數 |
| --- | --- | --- |
| 透過 HTTP 的 Web URL |從任何使用 HTTP 的 Web URL 中讀取逗號分隔值 (CSV)、tab 分隔值 (TSV)、屬性關聯檔案格式 (ARFF) 和支援向量機器 (SVM-light) 格式的資料 |<b>URL</b>：指定檔案的完整名稱，包括網站 URL 和檔案名稱與任何副檔名。 <br/><br/><b>資料格式</b>：指定其中一個支援的資料格式：CSV、TSV、ARFF 或 SVM-light。 如果資料有標頭資料列，將會用來指派資料行名稱。 |
| Hadoop/HDFS |從 Hadoop 中的分散式儲存體讀取資料。 您可以使用 HiveQL (類似 SQL 的查詢語言) 指定您要的資料。 在您將資料加入 Studio （傳統）之前，HiveQL 也可以用來匯總資料及執行資料篩選。 |<b>Hive 資料庫查詢</b>︰指定用來產生資料的 Hive 查詢。<br/><br/><b>HCatalog 伺服器 URI</b>：使用 *&lt;您的叢集名稱&gt;.azurehdinsight.net* 格式指定叢集的名稱。<br/><br/><b>Hadoop 使用者帳戶名稱</b>︰指定用來佈建叢集的 Hadoop 使用者帳戶名稱。<br/><br/><b>Hadoop 使用者帳戶密碼</b>︰指定佈建叢集時使用的認證。 如需詳細資訊，請參閱[在 HDInsight 中建立 Hadoop 叢集](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)。<br/><br/><b>輸出資料的位置</b>：指定資料要儲存在 Hadoop 分散式檔案系統 (HDFS) 還是 Azure 中。 <br/><ul>如果您將輸出資料儲存在 HDFS 中，請指定 HDFS 伺服器 URI。 (請確實使用沒有 HTTPS:// 前置詞的 HDInsight 叢集名稱)。 <br/><br/>如果您將輸出資料儲存在 Azure 中，則必須指定 Azure 儲存體帳戶名稱、儲存體存取金鑰和儲存體容器名稱。</ul> |
| SQL 資料庫 |讀取儲存在 Azure SQL Database、SQL 受控執行個體或 Azure 虛擬機器上執行的 SQL Server 資料庫中的資料。 |<b>資料庫伺服器名稱</b>︰指定資料庫執行所在的伺服器名稱。<br/><ul>若為 Azure SQL Database，請輸入所產生的伺服器名稱。 其格式通常為* &lt; generated_identifier &gt; database.windows.net。* <br/><br/>如果是裝載于 Azure 虛擬機器上的 SQL server，請輸入*tcp： &lt; 虛擬機器 DNS 名稱 &gt; ，1433*</ul><br/><b>資料庫名稱</b>︰在伺服器上指定資料庫的名稱。 <br/><br/><b>伺服器使用者帳戶名稱</b>：指定具有資料庫存取權限之帳戶的使用者名稱。 <br/><br/><b>伺服器使用者帳戶密碼</b>：指定使用者帳戶的密碼。<br/><br/><b>資料庫查詢</b>：輸入 SQL 陳述式，描述您要讀取的資料。 |
| 內部部署 SQL Database |讀取儲存在 SQL 資料庫中的資料。 |<b>資料閘道</b>︰指定其可存取 SQL Server 資料庫之電腦上安裝的資料管理閘道的名稱。 如需設定閘道的相關資訊，請參閱[使用 SQL server 中的資料，以 Azure Machine Learning Studio （傳統）執行先進的分析](use-data-from-an-on-premises-sql-server.md)。<br/><br/><b>資料庫伺服器名稱</b>︰指定資料庫執行所在的伺服器名稱。<br/><br/><b>資料庫名稱</b>︰在伺服器上指定資料庫的名稱。 <br/><br/><b>伺服器使用者帳戶名稱</b>：指定具有資料庫存取權限之帳戶的使用者名稱。 <br/><br/><b>使用者名稱和密碼</b>︰按一下 [輸入值]<b></b> 來輸入資料庫認證。 您可以根據 SQL Server 的設定方式，使用 Windows 整合式驗證或 SQL Server 驗證。<br/><br/><b>資料庫查詢</b>：輸入 SQL 陳述式，描述您要讀取的資料。 |
| Azure 資料表 |從 Azure 儲存體中的表格服務讀取資料。<br/><br/>如果您不常讀取大量資料，請使用 Azure 表格服務。 它可提供有彈性、非關聯式 (NoSQL)、具有超高延展性、經濟的且高度可用的儲存解決方案。 |**匯入資料**中的選項會依據您存取的是公開資訊，還是需要登入認證的私人儲存體帳戶而有所不同。 這取決於值有可能是 "PublicOrSAS" 或 "Account" 的<b>驗證類型</b>，兩者都有其本身的參數集。 <br/><br/><b>公用或共用存取簽章 (SAS) URI</b>︰參數為︰<br/><br/><ul><b>資料表 URI</b>︰指定資料表的公用或 SAS URL。<br/><br/><b>指定要為屬性名稱掃描的資料列</b>：值為 <i>TopN</i>，用以掃描指定的資料列數，或是 <i>ScanAll</i>，用以取得資料表中的所有資料列。 <br/><br/>如果資料是同質且可預測的，建議您選取 *TopN*，並且輸入 N 的數目。對於大型資料表，這可能會使讀取速度更快。<br/><br/>如果資料是結構化的，且其屬性集會隨著資料表的深度和位置而變化，請選擇 [ScanAll]** 選項以掃描所有資料列。 這可確保產生的屬性和中繼資料轉換具有完整性。<br/><br/></ul><b>私人儲存體帳戶</b>︰參數為︰ <br/><br/><ul><b>帳戶名稱</b>︰指定包含要讀取之資料表的帳戶名稱。<br/><br/><b>帳戶金鑰</b>︰指定與帳戶相關聯的儲存體金鑰。<br/><br/><b>資料表名稱</b>︰指定包含要讀取之資料的資料表名稱。<br/><br/><b>要為屬性名稱掃描的資料列</b>：值為 <i>TopN</i>，用以掃描指定的資料列數，或是 <i>ScanAll</i>，用以取得資料表中的所有資料列。<br/><br/>如果資料是同質且可預測的，建議您選取 *TopN*，並且輸入 N 的數目。對於大型資料表，這可能會使讀取速度更快。<br/><br/>如果資料是結構化的，且其屬性集會隨著資料表的深度和位置而變化，請選擇 [ScanAll]** 選項以掃描所有資料列。 這可確保產生的屬性和中繼資料轉換具有完整性。<br/><br/> |
| Azure Blob 儲存體 |讀取儲存在 Azure 儲存體之 Blob 服務中的資料，包括影像、非結構化文字或二進位資料。<br/><br/>您可以使用 Blob 服務來對外公開資料，或私下儲存應用程式資料。 您可以使用 HTTP 或 HTTPS 連線從任何地方存取您的資料。 |**匯入資料**模組中的選項會依據您存取的是公開資訊，還是需要登入認證的私人儲存體帳戶而有所不同。 這取決於<b>驗證類型</b>，其可能的值有「PublicOrSAS」或「帳戶」。<br/><br/><b>公用或共用存取簽章 (SAS) URI</b>︰參數為︰<br/><br/><ul><b>URI</b>︰指定儲存體 Blob 的公用或 SAS URL。<br/><br/><b>檔案格式</b>：指定 Blob 服務中的資料格式。 支援的格式為 CSV、TSV 和 ARFF。<br/><br/></ul><b>私人儲存體帳戶</b>︰參數為︰ <br/><br/><ul><b>帳戶名稱</b>︰指定包含要讀取之 blob 的帳戶名稱。<br/><br/><b>帳戶金鑰</b>︰指定與帳戶相關聯的儲存體金鑰。<br/><br/><b>容器、目錄或 Blob 的路徑</b>︰指定包含要讀取之資料的 Blob 名稱。<br/><br/><b>Blob 檔案格式</b>：指定 Blob 服務中的資料格式。 支援的資料格式包括 CSV、TSV、ARFF、具有指定編碼方式的 CSV，以及 Excel。 <br/><br/><ul>如果格式為 CSV 或 TSV，請務必指出檔案是否包含標頭列。<br/><br/>您可以使用 [Excel] 選項，從 Excel 活頁簿讀取資料。 在 [Excel 資料格式]<i></i> 選項中，指出資料是在 Excel 工作表範圍中，還是在 Excel 資料表中。 在 [Excel 工作表或內嵌的資料表]<i></i> 選項中，指定您要讀取的工作表或資料表名稱。</ul><br/> |
| 資料摘要提供者 |從支援的摘要提供者讀取資料。 目前僅支援開放式資料通訊協定 (OData) 格式。 |<b>資料內容類型</b>︰指定 OData 格式。<br/><br/><b>來源 URL</b>︰指定資料摘要的完整 URL。 <br/>例如，下列 URL 會從 Northwind 範例資料庫讀取資料：https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>從另一個實驗匯入

有時候您會想要從實驗取得中繼結果，並且用來做為其他實驗的一部分。 若要這樣做，您可以將模組另存為資料集：

1. 按一下您要儲存為資料集的模組輸出。
2. 按一下 [ **另存為資料集**]。
3. 系統提示時，請輸入可讓您輕易識別資料集的名稱和說明。
4. 按一下 [ **確定** ] 核取記號。

儲存完成時，資料集可用於工作區中的任何實驗。 您可以在模組調色盤的 [ **儲存的資料集** ] 清單中找到它。

## <a name="next-steps"></a>後續步驟

[部署使用資料匯入和資料匯出模組 Azure Machine Learning Studio web 服務](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
