---
title: 針對 Azure Data Factory UX 進行疑難排解
description: 瞭解如何對 Azure Data Factory UX 問題進行疑難排解。
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9f23155df6d9e63448b35974c331bf78c3e5f90c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426212"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>針對 Azure Data Factory UX 問題進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory UX 的常見疑難排解方法。

## <a name="adf-ux-not-loading"></a>ADF UX 未載入

> [!NOTE]
> Azure Data Factory UX 正式支援 Microsoft Edge 和 Google Chrome。 使用其他網頁瀏覽器可能會導致非預期或未記載的行為。

### <a name="third-party-cookies-blocked"></a>已封鎖協力廠商 cookie

ADF UX 會使用瀏覽器 cookie 來保存使用者會話，並啟用互動式開發和監視體驗。 您的瀏覽器可能會封鎖協力廠商 cookie，因為您使用 incognito 會話或啟用了 ad 封鎖程式。 封鎖協力廠商 cookie 可能會在載入入口網站時發生問題，例如重新導向至空白頁面， https://adf.azure.com/accesstoken.html 或收到警告訊息，指出已封鎖協力廠商 cookie。 若要解決此問題，請使用下列步驟，在您的瀏覽器上啟用協力廠商 cookie 選項：

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>允許所有 cookie

1. 在您的瀏覽器中造訪 **chrome://settings/cookies** 。
1. 選取 [ **允許所有 cookie** ] 選項 

    ![允許 Chrome 中的所有 Cookie](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. 重新整理 ADF UX，然後再試一次。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>只允許 ADF UX 使用 cookie
如果您不想允許所有的 cookie，可以選擇只允許 ADF UX：
1. 造訪 **chrome://settings/cookies**。
1. 選取 [在**永遠可使用 cookie 的網站**下**新增**] 選項 

    ![將 ADF UX 新增至 Chrome 中允許的網站](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. 新增 **adf.azure.com** 網站，檢查 **所有 cookie** 選項，然後儲存。 

    ![允許來自 ADF UX 網站的所有 cookie](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. 重新整理 ADF UX，然後再試一次。

### <a name="microsoft-edge"></a>Microsoft Edge

1. 在您的瀏覽器中造訪 **edge://settings/content/cookies** 。
1. 確定已啟用 [ **允許網站儲存和讀取 cookie 資料** ]，並已停用 [ **封鎖協力廠商 cookie** ] 選項 

    ![允許 Edge 中的所有 cookie](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. 重新整理 ADF UX，然後再試一次。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>只允許 ADF UX 使用 cookie

如果您不想允許所有的 cookie，可以選擇只允許 ADF UX：

1. 造訪 **edge://settings/content/cookies**。
1. 在 [ **允許** ] 區段下，選取 [ **新增** 並新增 **adf.azure.com** 網站]。 

    ![在 Edge 中將 ADF UX 新增至允許的網站](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. 重新整理 ADF UX，然後再試一次。

## <a name="connection-failed-on-adf-ux"></a>ADF UX 上的連接失敗

有時您會在 ADF UX 上看到「連線失敗」錯誤，類似于以下螢幕擷取畫面，請按一下 [ **測試**連線]、[ **預覽**] 等等。

![連接失敗](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

在此情況下，您可以在瀏覽器中嘗試使用 InPrivate 瀏覽模式來進行相同的操作。

如果仍然無法運作，請在瀏覽器中按 F12 以開啟 **開發人員工具**。 移至 [ **網路** ] 索引標籤，核取 [ **停**用快取]，重試失敗的作業，然後找出失敗的要求 (紅色) 。

![失敗的要求](media/data-factory-ux-troubleshoot-guide/failed-request.png)

然後，在此案例中尋找**主機名稱** (，從失敗要求的**要求 URL**中**dpnortheurope.svc.datafactory.azure.com**) 。

直接在瀏覽器的網址列中輸入 **主機名稱** 。 如果您在瀏覽器中看到404，這通常表示您的用戶端正常，且問題在 ADF 服務端。 提出支援票證，其中包含來自 ADF UX 錯誤訊息的 **活動識別碼** 。

![找不到資源](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

如果不是，或您在瀏覽器中看到類似的錯誤，這通常表示您有一些用戶端的問題。 進一步遵循疑難排解步驟。

![用戶端錯誤](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

開啟 **命令提示** 字元，然後輸入 **nslookup dpnortheurope.svc.datafactory.azure.com**。 一般的回應看起來應該如下所示：

![命令回應1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

如果您看見一般的 DNS 回應，請進一步聯絡您當地的 IT 支援，檢查防火牆設定是否已封鎖此主機名稱的 HTTPS 連線。 如果無法解決問題，請提出支援票證，其中包含來自 ADF UX 錯誤訊息的 **活動識別碼** 。

如果您看到其他任何內容，這通常表示在解析 DNS 名稱時，DNS 伺服器發生錯誤。 通常變更 ISP (網際網路服務提供者) 或 DNS (例如，到 Google DNS 8.8.8.8) 可能是可能的因應措施。 如果問題持續發生，您可以進一步嘗試 **nslookup datafactory.azure.com** 和 **nslookup azure.com** ，以查看 DNS 解析失敗的層級，並將所有資訊提交給您當地的 IT 支援或您的 ISP 進行疑難排解。 如果他們認為問題仍在 Microsoft 端，請提出支援票證，其中包含來自 ADF UX 錯誤訊息的 **活動識別碼** 。

![命令回應2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>變更資料集中的連結服務類型

檔案格式資料集可搭配所有檔案型連接器使用，例如，您可以在 Azure Blob 或 Azure Data Lake Storage Gen2 上設定 Parquet 資料集。 注意每個連接器都支援活動上不同的資料存放區相關設定，以及不同的應用程式模型。 

在 ADF 撰寫 UI 上，當您在活動中使用檔案格式資料集（包括複製、查閱、GetMetadata、刪除活動），以及在資料集中，您想要指向目前 (（例如從檔案系統切換至 ADLS Gen2) ）的連結服務時，您會看到下列警告訊息。 為了確保它是乾淨的參數，在您同意時，參考此資料集的管線和活動也會修改為使用新的類型，而且任何與新類型不相容的現有資料存放區設定都會被清除，因為不再適用。

若要深入瞭解每個連接器支援的資料存放區設定，您可以移至對應的連接器文章-> 複製活動屬性，以查看詳細的屬性清單。 請參閱 [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 檔案儲存體](connector-azure-file-storage.md)、 [檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

![警告訊息](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：

* [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory 的 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
* [Azure 影片](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
