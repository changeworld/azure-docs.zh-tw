---
title: 將您的 AppSource 套件儲存到 Azure 儲存體並使用 SAS 金鑰產生 URL
description: 詳細說明上傳並保護 AppSource 套件所需的步驟。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285362"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>將您的 AppSource 套件儲存到 Azure 儲存體並使用 SAS 金鑰產生 URL
=============================================================================

為維持檔案安全，所有夥伴都必須將其 AppSource 套件檔案儲存在 Azure Blob 儲存體帳戶中並使用 SAS 金鑰來共用它。 我們將會從您的 Azure 儲存體位置擷取套件檔案進行認證並使用它來進行 AppSource 試用。

使用下列步驟將您的套件上傳到 Blob 儲存體：

1. 移至 <https://azure.microsoft.com> 並建立免費試用或計費帳戶。

2. 登入 [Azure 入口網站](https://portal.azure.com/)。

3. 按一下 [+ 新增]**** 並移至 [資料 + 儲存體]**** 帳戶以建立新的儲存體帳戶。

   ![Microsoft Azure 入口網站上的 [資料 + 儲存體] 儲存體](media/CRMScreenShot7.png)

4. 輸入 [名稱]**** 與 [資源群組]**** 名稱，然後按一下 [建立]**** 按鈕。

   ![在 Microsoft Azure 入口網站上建立儲存體帳戶](media/CRMScreenShot8.png)

5. 瀏覽到您新建立的資源群組並建立新的 Blob 容器。

   ![使用 Microsoft Azure 入口網站以 Blob 形式上傳套件](media/CRMScreenShot9.png)

6. 下載並安裝 Microsoft [Azure 儲存體總管](https://storageexplorer.com/) (若您還沒有這樣做)。

7. 開啟 [儲存體總管] 並使用圖示來連線到您的 Azure 儲存體帳戶。

8. 瀏覽到您建立的 Blob 容器，然後按一下 [上傳]**** 以新增您的套件 zip 檔案。

   ![使用 Microsoft 儲存體總管上傳套件](media/CRMScreenShot10.png)

9. 以滑鼠右鍵按一下您的檔案，然後選取 [取得共用存取簽章]****。

   ![取得 Azure 檔案的共用存取簽章](media/CRMScreenShot11.png)

10. 修改 [到期時間]**** 將 SAS 的有效期設定為一個月，然後按一下 [建立]****。

    ![修改 Azure 檔案的 SAS 到期日](media/CRMScreenShot12.png)

11. 複製 URL 欄位並儲存它以供稍後使用。 當您建立關聯的供應項目時，必須輸入此 URL。 

    ![複製 Azure 檔案的 SAS URL](media/CRMScreenShot13.png)

