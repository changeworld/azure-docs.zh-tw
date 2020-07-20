---
title: 從 Azure 資料箱匯出資料的教學課程 |Microsoft Docs
description: 瞭解部署必要條件以及如何從 Azure 資料箱匯出資料
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 07/10/2020
ms.author: v-grpr
ms.openlocfilehash: 5b17488ebab8dc29221723fabf8efe0c00467509
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209320"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>教學課程：建立 Azure 資料箱 (Preview 的匯出順序) 

Azure 資料箱是一種混合式解決方案，可讓您將資料從 Azure 移至您的位置。 本教學課程說明如何建立 Azure 資料箱的匯出順序。 建立匯出順序的主要原因是為了嚴重損壞修復，萬一內部部署儲存體遭到入侵，而且需要還原備份。

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 匯出的必要條件
> * 排序資料箱進行匯出
> * 追蹤匯出順序
> * 取消匯出順序

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>必要條件

訂購裝置之前，請先完成資料箱服務和裝置的下列設定必要條件。

### <a name="for-service"></a>針對服務

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* 請確定您有現有的資源群組可與您的 Azure 資料箱搭配使用。

* 請確定您想要從中匯出資料的 Azure 儲存體帳戶是其中一種支援的儲存體帳戶類型，如[資料箱所支援的儲存體](data-box-system-requirements.md#supported-storage-accounts)帳戶。

### <a name="for-device"></a>針對裝置

在您開始前，請確定：

* 您已將主機電腦連線到資料中心網路。 您會將資料從 Azure 資料箱複製到這部電腦。 您的主機電腦必須執行支援的作業系統，如 [Azure 資料箱系統需求](data-box-system-requirements.md)中所述。

* 您的資料中心必須有高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10-GbE 連線，也可以使用 1-GbE 資料連結，但是複製速度會受到影響。

## <a name="order-data-box-for-export"></a>匯出資料箱的訂單

請在 Azure 入口網站中執行下列步驟，以訂購裝置。

1. 使用您的 Microsoft Azure 認證在以下 URL 登入：[https://portal.azure.com](https://portal.azure.com)。

2. 選取 [+ 建立資源]，然後搜尋「Azure 資料箱」。 選取 [Azure 資料箱]。

   ![建立資源](media/data-box-deploy-export-ordered/azure-data-box-export-00b.png)

3. 選取 [建立]。

   ![建立 Azure 資料箱](media/data-box-deploy-export-ordered/azure-data-box-export-00c.png)

4. 檢查您的區域中是否有可用的 Azure 資料箱服務。 輸入或選取下列資訊，然後選取 [套用]。

    |設定  |值  |
    |---------|---------|
    |傳輸類型     | 選取 [**匯出至 Azure**]。        |
    |訂用帳戶     | 為資料箱服務選取 EA、CSP 或 Azure 贊助訂用帳戶。 <br> 訂用帳戶會連結到您的帳單帳戶。       |
    |資源群組     |    選取現有的資源群組。 <br> 資源群組是適用於資源而可一併管理或部署的邏輯容器：         |
    |來源 Azure 區域    |    選取您的資料目前所在的 Azure 區域。         |
    |目的地國家/地區     |     選取您要寄送裝置的國家/地區。        |

   ![選取您的資料箱設定](media/data-box-deploy-export-ordered/azure-data-box-export-01.png)

5. 選取 [資料箱]。 單一訂單的最大可用容量是 80 TB。 您可以建立多份訂單以訂購更大的資料大小。

   ![選取資料箱容量](media/data-box-deploy-export-ordered/azure-data-box-export-02b.png)

6. 在 [**順序**] 中，指定**基本**訂單詳細資料。 輸入或選取下列資訊，然後選取 [下一步]。

    |設定  |值  |
    |---------|---------|
    |訂用帳戶     | 系統會根據您先前的選取專案自動填入訂用帳戶。|
    |資源群組 | 您先前選取的資源群組。 |
    |匯出順序名稱     |  提供用來追蹤訂單的易記名稱。 <br> 名稱長度可介於 3 到 24 個字元之間，且可以是字母、數字和連字號。 <br> 名稱必須以字母或數字為開頭或結尾。      |

    ![匯出順序基本概念](media/data-box-deploy-export-ordered/azure-data-box-export-03.png)

    選取 **[下一步：資料選取]** 以繼續。

7. 在 [**選取資料**] 中，選取 [**新增儲存體帳戶] 和 [匯出類型**]。

    ![新增儲存體帳戶和匯出類型](media/data-box-deploy-export-ordered/azure-data-box-export-03b.png)

8. 在 [**選取匯出選項**] 中，指定匯出選項的詳細資料。 輸入或選取下列資訊，然後選取 [**新增**]。

    |設定  |值  |
    |---------|---------|
    |儲存體帳戶     | 您要從中匯出資料的 Azure 儲存體帳戶。 |
    |匯出類型     | 指定要從**所有物件**匯出的資料類型，並**使用 XML**檔案。<ul><li> **所有物件**-指定作業會根據您選取的**傳輸選項**來匯出所有資料。</li><li> **使用 xml**檔案–指定 xml 檔案，其中包含要從儲存體帳戶匯出之 blob 和/或檔案的一組路徑和前置詞。 XML 檔案必須位於所選儲存體帳戶的容器中，而且目前不支援從檔案共用中選取。 檔案必須是非空白的 .xml 檔案。</li></ul>        |
    |傳輸選項     |  指定 [全**選**]、[**所有 blob**] 和 [**所有**檔案] 中的資料傳輸選項。 <ul><li> **全選**-指定匯出所有 Blob 和 Azure 檔案。 如果您使用的儲存體帳戶僅支援 blob (Blob 儲存體帳戶) ，將無法選取 [**所有**檔案] 選項。</li><li> **所有 blob** -指定只匯出區塊和分頁 blob。</li><li> **所有**檔案-指定匯出不含 blob 的所有檔案。 您 (GPv1 和 GPv2、premium 儲存體或 blob) 儲存體的儲存體帳戶類型，會決定您可以匯出的資料類型。 如需詳細資訊，請參閱[支援的儲存體帳戶以進行匯出](../storage/common/storage-import-export-requirements.md#supported-storage-types)。</li></ul>         |
    |包含詳細資訊記錄檔     | 指出您是否想要詳細資訊記錄檔，其中包含已成功匯出之所有檔案的清單。        |

    > [!NOTE]
    >
    > 如果您選取 [**使用 xml**檔案做為**匯出類型**] 設定，則必須確定 XML 包含有效的路徑和（或）前置詞。 您必須建立並提供 XML 檔案。  如果檔案無效，或沒有任何資料符合指定的路徑，則順序會以部分資料終止，或不會匯出任何資料。

    若要瞭解如何將 XML 檔案加入至容器，請參閱[使用 xml 檔案的匯出順序](data-box-deploy-export-ordered.md#export-order-using-xml-file)。

   ![選取匯出選項](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

   若要查看 xml 輸入的範例，請參閱[範例 xml 輸入](data-box-deploy-export-ordered.md#sample-xml-file)

9. 在 [**選取資料**] 中，檢查您的設定，然後選取 **[下一步：連絡人詳細資料>**

   ![聯絡詳細資料](media/data-box-deploy-export-ordered/azure-data-box-export-05.png)

10. 在 [**連絡人詳細資料**] 中，選取 [ **+ 新增寄送位址**] 以輸入您的出貨資訊。

    ![新增寄送位址](media/data-box-deploy-export-ordered/azure-data-box-export-06.png)

11. 在 [**新增寄送位址**] 中，提供您的姓名、公司的姓名和郵寄地址，以及有效的電話號碼。 選取 [驗證]。 服務會驗證交貨地址以確認服務可用性。 如果服務可提供至指定的交貨地址，您將會收到該項通知。

    ![驗證交貨位址](media/data-box-deploy-export-ordered/azure-data-box-export-07.png)

    如果您在可使用自我管理運送的區域中進行排序，您可以選取此選項。 如需有關自我管理運送的詳細資訊，請參閱[使用自我管理運送](data-box-portal-customer-managed-shipping.md)。

12. 一旦成功驗證出貨詳細資料，請選取 [**新增寄送位址**]。

13. 在 [**連絡人詳細資料**] 中，檢查您的寄送位址和電子郵件地址。 服務會將關於任何訂單狀態更新的電子郵件通知傳送至指定的電子郵件地址。

    建議您使用群組電子郵件，以便在群組中的管理員離開時繼續接收通知。

    ![訂單詳細資料](media/data-box-deploy-export-ordered/azure-data-box-export-09.png)

14. 選取 **[下一步：審查 + 訂單>] **。 您必須接受這些條款及條件，才能繼續建立訂單。

15. 選取 [訂單]。 建立訂單需要幾分鐘的時間。

    ![認可順序](media/data-box-deploy-export-ordered/azure-data-box-export-10.png)

## <a name="export-order-using-xml-file"></a>使用 XML 檔案的匯出順序

如果您選取 [**使用 XML**檔案]，您可以指定要匯出 (頁面和區塊) 的特定容器和 blob。 您必須遵循[範例 xml 檔案表格](#sample-xml-file)規格來格式化 XML。 下列步驟示範如何使用 XML 檔案來匯出您的資料：

1. 針對 [**匯出類型**]，選取 [**使用 XML**檔案]。 這是您的 XML 檔案，可指定您想要匯出的特定 blob 和 Azure 檔案。 若要加入 XML 檔案，請選取 [**按一下這裡選取 xml**檔案]。
     ![XML 檔](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-01.png)

2. 選取 [ **+ 容器**] 以建立容器。
    ![XML 檔](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-02.png)

3. 在 [**新增容器**] 索引標籤中，從 Azure 入口網站的右邊向外彈出，新增容器的名稱。 名稱必須是小寫，而且您可以包含數位和連字號 '-'。 然後從下拉式清單方塊中選取 [**公用] 存取層級**。 我們建議您選擇**私用 (非匿名存取) ** ，以防止其他人存取您的資料。 如需有關容器存取層級的詳細資訊，請參閱[容器存取權限](../storage/blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)。

   ![XML 檔](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-04.png)

4. 選取 [建立]。

   ![XML 檔](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-07.png)

   如果您的容器已成功建立，您將會收到下列訊息：

   ![XML 檔](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-09.png)

5. 選取您建立的容器，然後在其上按兩下。

   ![XML 檔](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-08.png)

6. 按兩下容器將會顯示 [容器屬性] 視圖。 您現在想要附加 (或流覽至包含您要匯出的 blob 和/或 Azure 檔案清單的 XML 檔案) 。 選取 [上傳] 。

   ![XML 檔](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-10c.png)

7. 您已成功將 XML 檔案新增至容器。 只會匯出您在此 XML 中指定的 blob 和 Azure 檔案。

   ![XML 檔](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-12.png)

## <a name="track-the-order"></a>追蹤訂單狀態

在您下訂單之後，可以從 Azure 入口網站來追蹤訂單狀態。 請移至您的資料箱訂單，然後移至 [概觀] 以檢視狀態。 入口網站會顯示處於 [已訂購] 狀態的訂單。

當裝置準備完成時，資料複製就會從選取的儲存體帳戶開始。 入口網站會顯示 [**資料複製進行中**] 狀態的順序。

![已處理資料箱匯出訂單](media/data-box-deploy-export-ordered/azure-data-box-export-15b.png)

資料箱會從 (s) 複製來源儲存體帳戶的資料。 資料複製完成後，資料箱會被鎖定，而入口網站會顯示 [**複製已完成**] 狀態中的訂單。

![資料箱匯出資料複製完成](media/data-box-deploy-export-ordered/azure-data-box-export-15c.png)

如果裝置無法使用，您將會收到通知。 如果可使用服務，Microsoft 會識別要寄送的裝置，並準備出貨。 在裝置準備期間，會執行下列動作：

* 系統會針對與裝置相關聯的每個儲存體帳戶建立 SMB 共用。
* 針對每個共用，會產生例如使用者名稱和密碼的存取認證。
* 裝置已鎖定，且只能使用裝置解除鎖定密碼來存取。 若要取得密碼，您必須登入您的 Azure 入口網站帳戶，然後選取 [**裝置詳細資料**]。

Microsoft 接著會透過區域貨運公司來準備和分派您的裝置。 在裝置送達後，您會收到追蹤號碼。 入口網站會顯示訂單處於 [已分派] 狀態。

![已分派資料箱匯出訂單](media/data-box-deploy-export-ordered/azure-data-box-export-16.png)

如果選取 [自我管理的運送]，當您準備好從資料中心挑選裝置時，您會收到電子郵件通知，其中包含後續步驟。 如需與自我管理的運送相關的詳細資訊，請參閱[自我管理的運送](data-box-portal-customer-managed-shipping.md)。

![自我管理的運送可供取貨](media/data-box-deploy-export-ordered/azure-data-box-export-17.png)

## <a name="cancel-the-order"></a>取消訂單

若要取消此訂單，請在 Azure 入口網站中移至 [概觀]，然後從命令列選取 [取消]。

下訂單之後，您可以在訂單開始處理前的任何時間點取消它。

若要刪除已取消的訂單，請移至 [概觀]，然後從命令列選取 [刪除]。

## <a name="sample-xml-file"></a>範例 XML 檔

下列 xml 顯示在您選取 [**使用 xml**檔案] 選項時，匯出順序所使用之 xml 格式的 blob 名稱、blob 首碼和 Azure 檔案的範例：

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

關於 xml 檔案的一些重要重點：

* Xml 標記會區分大小寫，而且必須完全符合上述範例中所指定的內容。
* 開頭和結束記號必須相符。
* 不正確的 xml 標記或格式可能會導致資料匯出失敗。
* 如果 blob 和（或）檔案前置詞無效，將不會匯出任何資料。

### <a name="examples-of-valid-blob-paths"></a>有效 Blob 路徑的範例

下表顯示有效 Blob 路徑範例：

   | 選取器 | Blob 路徑 | 描述 |
   | --- | --- | --- |
   | 開頭為 |/ |匯出儲存體帳戶中的所有 Blob |
   | 開頭為 |/$root/ |匯出根容器中的所有 Blob |
   | 開頭為 |/containers |匯出任何容器中以前置詞**容器**開頭的所有 blob |
   | 開頭為 |/container-name/ |匯出容器**容器-名稱**中的所有 blob |
   | 開頭為 |/container-name/prefix |匯出容器容器中的所有 blob-開頭為前置詞**前置**詞的**名稱** |
   | 等於 |$root/logo.bmp |匯出根容器中的 Blob **logo.bmp** |
   | 等於 |8tbpageblob/mydata.txt |匯出容器**8tbpageblob**中的 blob **mydata.txt** |

## <a name="sample-log-files"></a>範例記錄檔

本節提供在匯出期間產生的範例記錄檔。 錯誤記錄檔會自動產生。 若要產生詳細資訊記錄檔，您必須在設定匯出順序時選取 [在 Azure 入口網站中**包含詳細資訊記錄**檔]。
如需有關複製和詳細資訊記錄檔的詳細資訊，請參閱[複製記錄](data-box-deploy-export-copy-data.md#copy-data-from-data-box)檔。

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
>
> * 匯出的必要條件
> * 排序資料箱進行匯出
> * 追蹤匯出順序
> * 取消匯出順序

請繼續進行下一個教學課程，以了解如何設定資料箱。

> [!div class="nextstepaction"]
> [設定您的 Azure 資料箱](./data-box-deploy-set-up.md)
