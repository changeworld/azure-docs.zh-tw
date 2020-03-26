---
title: 使用 Azure 入口網站管理 Azure 檔案共用的快速入門
description: 使用此快速入門了解如何使用 Azure 入口網站來管理 Azure 檔案服務。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fef3daf6e9e535736002e309e3d27491364dc553
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "71260297"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立及管理 Azure 檔案共用 
[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 Azure 檔案共用可在 Windows、Linux 和 macOS 中掛接。 本指南會逐步說明透過 [Azure 入口網站](https://portal.azure.com/)來使用 Azure 檔案共用的基本概念。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-storage-account"></a>建立儲存體帳戶
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用
若要建立 Azure 檔案共用：

1. 從儀表板中選取儲存體帳戶。
2. 在儲存體帳戶頁面上的 [服務]  區段中，選取 [檔案]  。
    ![儲存體帳戶之服務區段的螢幕擷取畫面；選取檔案服務](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. 在 [檔案服務]  頁面頂端的功能表上，按一下 [檔案共用]  。 [新增檔案共用]  頁面隨即顯示。
4. 在 [名稱]  中輸入 *myshare*。
5. 按一下 [確定]  以建立 Azure 檔案共用。

共用名稱必須全部使用小寫字母、數字和單一連字號，但開頭不可以是連字號。 如需有關為檔案共用與檔案命名的完整詳細資料，請參閱 [命名和參考共用、目錄、檔案及中繼資料](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

## <a name="use-your-azure-file-share"></a>使用您的 Azure 檔案共用
Azure 檔案服務提供兩個在 Azure 檔案共用中使用檔案和資料夾的方法：業界標準[伺服器訊息區 (SMB) 通訊協定](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)和[檔案 REST 通訊協定](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api)。 

若要使用 SMB 掛接檔案共用，請根據您的作業系統參閱下列文件：
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>從 Azure 入口網站使用 Azure 檔案共用
透過 Azure 入口網站提出的所有要求都是使用 REST API 進行，讓您不需 SMB 存取權，即可在用戶端上建立、修改及刪除檔案和目錄。 您可以直接使用 File REST 通訊協定 (也就是自己製作 REST HTTP 呼叫)，但最常見的 File REST 通訊協定使用方式 (除了使用 Azure 入口網站之外) 是使用 [Azure PowerShell 模組](storage-how-to-use-files-powershell.md)、[Azure CLI](storage-how-to-use-files-cli.md) 或「Azure儲存體 SDK」，這些都能以您選擇的指令碼/程式設計語言為 File REST 通訊協定提供良好的包裝函式。 

我們預期人們使用 Azure 檔案服務時，都會想透過 SMB 通訊協定來與 Azure 檔案共用搭配使用，因為這可讓他們使用預期能使用的現有應用程式和工具，但使用檔案 REST API 比使用 SMB 好的原因有很多個，例如：

- 您需要能隨時隨地從沒有 SMB 存取的膝上型電腦、平板電腦或行動裝置快速變更 Azure 檔案共用。
- 您需要從無法掛接 SMB 共用的用戶端執行指令碼或應用程式，例如未將連接埠 445 解除封鎖的內部部署用戶端。
- 您要利用無伺服器資源，例如 [Azure Functions](../../azure-functions/functions-overview.md)。 

下列範例會示範如何透過檔案 REST 通訊協定，來使用 Azure 入口網站操作 Azure 檔案共用。 

您現在已建立 Azure 檔案共用，並且可使用 SMB 將檔案共用掛接在 [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md) 或 [macOS](storage-how-to-use-files-mac.md) 上。 或者，您可以使用 Azure 入口網站來處理 Azure 檔案共用。 

#### <a name="create-a-directory"></a>建立目錄
若要在 Azure 檔案共用的根目錄建立名為 *myDirectory* 的新目錄：

1. 在 [檔案服務]  頁面上，選取 **myshare** 檔案共用。 檔案共用的頁面隨即開啟。
2. 在頁面頂端的功能表上，選取 [+ 新增目錄]  。 [新增目錄]  頁面隨即顯示。
3. 輸入 *myDirectory*，然後按一下 [確定]  。

#### <a name="upload-a-file"></a>上傳檔案 
若要示範如何上傳檔案，您必須先建立或選取要上傳的檔案。 您可以用您適用的任何方式來執行這項操作。 選取您要上傳的檔案之後：

1. 按一下 **myDirectory** 目錄。 **myDirectory** 面板隨即開啟。
2. 在頂端的功能表中，按一下 [上傳]  。 [檔案上傳]  面板隨即開啟。  
    ![上傳檔案面板的螢幕擷取畫面](media/storage-how-to-use-files-portal/upload-file-1.png)

3. 按一下資料夾圖示開啟視窗，以瀏覽您的本機檔案。 
4. 選取檔案，然後按一下 [開啟]  。 
5. 在 [上傳檔案]  頁面中確認檔案名稱，然後按一下 [上傳]  。
6. 完成時，檔案應會出現在 [myDirectory]  頁面的清單中。

#### <a name="download-a-file"></a>下載檔案
您可以在檔案上按一下滑鼠右鍵，以下載您所上傳的檔案複本。 按一下 [下載] 按鈕後，確切的經驗將取決於您所使用的作業系統和瀏覽器。

## <a name="clean-up-resources"></a>清除資源
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [什麼是 Azure 檔案服務？](storage-files-introduction.md)
