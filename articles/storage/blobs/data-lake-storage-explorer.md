---
title: 搭配使用 Azure 儲存體總管與 Azure Data Lake Storage Gen2
description: 使用 Azure 儲存體總管可在已啟用階層命名空間 (HNS) 的儲存體帳戶中，管理目錄和檔案和目錄存取控制清單 (ACL) 。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 088618c0f23f5cfd1b14c3c946ef735fadb893d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086093"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure 儲存體總管來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 ACL (機器翻譯)

本文說明如何使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 來建立和管理已啟用階層命名空間 (HNS) 之儲存體帳戶中的目錄、檔案和許可權。

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](data-lake-storage-quickstart-create-account.md)指示以建立帳戶。
> * Azure 儲存體總管安裝在本機電腦上。 若要安裝適用於 Windows、Macintosh 或 Linux 的 Azure 儲存體總管，請參閱 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="sign-in-to-storage-explorer"></a>登入儲存體總管

您第一次啟動儲存體總管時，[Microsoft Azure 儲存體總管 - 連線]**** 視窗會隨即出現。 雖然儲存體總管提供數種方式可連線到儲存體帳戶，但目前只有一種方式支援管理 ACL。

|Task|目的|
|---|---|
|新增 Azure 帳戶 | 將您重新導向至貴組織的登入頁面，向 Azure 進行驗證。 如果想要管理及設定 ACL，這是目前唯一支援的驗證方法。|
|使用連接字串或共用存取簽章 URI | 可用來直接存取容器，或是使用 SAS 權杖或共用連接字串的儲存體帳戶。 |
|使用儲存體帳戶名稱和金鑰| 使用儲存體帳戶的儲存體名稱和儲存體帳戶金鑰來連線到 Azure 儲存體。|

選取 [新增 Azure 帳戶]  ，然後按一下 [登入]  。遵循螢幕上的提示來登入 Azure 帳戶。

![[Microsoft Azure 儲存體總管 - 連線] 視窗](media/storage-quickstart-blobs-storage-explorer/connect.png)

完成連線時，會載入 Azure 儲存體總管，並顯示 [總管]  索引標籤。 這個檢視可讓您深入了解您所有的 Azure 儲存體帳戶，以及透過 [Azure 儲存體模擬器](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 帳戶或 [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 環境設定的本機儲存體。

![[Microsoft Azure 儲存體總管 - 連線] 視窗](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>建立容器

容器會保存目錄和檔案。 若要建立一個，請展開您在後續步驟中建立的儲存體帳戶。 選取 [Blob 容器]  ，然後以滑鼠右鍵按一下 [建立 Blob 容器]  。 輸入容器的名稱。 請參閱 [建立容器](storage-quickstart-blobs-dotnet.md#create-a-container) 一節，以取得命名容器的規則和限制清單。 完成時，請按 **enter** 以建立容器。 成功建立容器之後，它會顯示在所選儲存體帳戶的 [ **Blob 容器** ] 資料夾底下。

![Microsoft Azure 儲存體總管-建立容器](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>建立目錄

若要建立目錄，請選取您在繼續步驟中建立的容器。 在 [容器] 功能區中，選擇 [ **新增資料夾** ] 按鈕。 輸入您目錄的名稱。 完成時，請按 **enter** 以建立目錄。 成功建立目錄之後，它會出現在編輯器視窗中。

![Microsoft Azure 儲存體總管-建立目錄](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>將 Blob 上傳到目錄

在 [目錄] 功能區上，選擇 [ **上傳** ] 按鈕。 這項作業可讓您選擇上傳資料夾或檔案。

選擇要上傳的檔案或資料夾。

![Microsoft Azure 儲存體總管 - 上傳 blob](media/data-lake-storage-explorer/upload-file.png)

當您選取 [確定]  時，所選取的檔案會排入要上傳的佇列中，每個檔案都會上傳。 上傳完成時，結果會顯示在 [活動]  視窗。

## <a name="view-blobs-in-a-directory"></a>檢視目錄中的 Blob

在 [Azure 儲存體總管]**** 應用程式中，選取儲存體帳戶底下的目錄。 主窗格會顯示所選目錄中的 blob 清單。

![Microsoft Azure 儲存體總管 - 列出目錄中的 blob](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>下載 Blob

若要使用 **Azure 儲存體總管**下載檔案，並選取檔案，請從功能區選取 [ **下載** ]。 檔案對話方塊隨即開啟，讓您可以輸入檔案名稱。 選取 [ **儲存** ] 以開始將檔案下載到本機位置。

## <a name="managing-access"></a>管理存取

您可以在容器根目錄設定權限。 若要這樣做，您必須使用有權執行此動作的個別帳戶 (而非使用連接字串) 登入 Azure 儲存體總管。 以滑鼠右鍵按一下容器，然後選取 [管理權限]****，顯示 [管理權限]**** 對話方塊。

![Microsoft Azure 儲存體總管 - 管理目錄存取](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

[管理權限]**** 對話方塊可讓您管理擁有者和擁有者群組的權限。 它也可以讓您將新的使用者和群組新增到存取控制清單，方便之後管理他們的權限。

若要將新的使用者或群組加到存取控制清單，請選取 [新增使用者或群組]**** 欄位。

輸入和您想要新增至清單的相對應 Azure Active Directory (AAD) 項目，然後選取 [新增]****。

使用者或群組現在將會出現在 [使用者和群組:]**** 欄位中，讓您可以開始管理他們的權限。

> [!NOTE]
> 最佳做法和建議是在 AAD 中建立安全性群組，並維護群組的權限，而不是維護個別使用者的權限。 如需這項建議和其他最佳做法的詳細資料，請參閱 [Data Lake Storage Gen2 的最佳做法](data-lake-storage-best-practices.md)。

您可以指派兩種權限：存取 ACL 和預設 ACL。

* **存取**：存取物件的存取 acl 控制存取權。 檔案和目錄均有存取 ACL。

* **預設值**：與目錄相關聯之 acl 的範本，可決定在該目錄下建立之任何子專案的存取 acl。 檔案沒有預設 ACL。

在這兩個類別內，您可以在檔案或目錄上指派三個許可權： **讀取**、 **寫入**和 **執行**。

>[!NOTE]
> 在這裡選取的項目並不會在目錄內部目前現有的任何項目上設定使用權限。 如果檔案已經存在，必須移至每個項目手動設定使用權限。

您可以管理個別目錄及個別檔案的使用權限，也就是允許您進行更細部的存取控制。 管理目錄和檔案使用權限的程序與上面的描述相同。 以滑鼠右鍵按一下您想要管理使用權限的檔案或目錄，然後依照相同程序進行作業。

## <a name="next-steps"></a>接下來的步驟

瞭解 Data Lake Storage Gen2 中的存取控制清單。

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
