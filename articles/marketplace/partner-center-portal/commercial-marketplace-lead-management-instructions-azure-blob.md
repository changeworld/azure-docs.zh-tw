---
title: 商業市場程式中的 Azure 表*Azure 應用商店
description: 配置 Azure Blob 的潛在客戶管理
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285243"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Azure Blob 的潛在客戶管理說明

>[!Caution]
>處理市場產品/服務的潛在客戶的 Azure Blob 選項已被棄用。 如果當前已發佈具有 Azure Blob 潛在客戶管理配置的產品/服務，則不再接收客戶潛在客戶。 請將潛在客戶管理配置更新為任何其他潛在客戶管理選項。 瞭解[潛在客戶管理登錄頁上](./commercial-marketplace-get-customer-leads.md)的其他選項。

如果合作夥伴中心中沒有明確支援客戶關係管理 （CRM） 系統以接收 Azure 應用商店和 AppSource 潛在客戶，則可以使用 Azure Blob 來處理這些潛在客戶。 然後，您可以選擇匯出資料並將其導入 CRM 系統。 本文中的說明將為您提供創建 Azure 存儲帳戶和該帳戶下的 Azure Blob 的過程。 此外，您可以使用 Microsoft Flow 創建新流，在產品/服務收到潛在客戶時發送電子郵件通知。


## <a name="how-to-configure-azure-blob"></a>如何配置 Azure Blob

1. 如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
1. Azure 帳戶處於活動狀態後，請登錄到 Azure[門戶](https://portal.azure.com)。
1. 在 Azure 門戶中，使用以下過程創建存儲帳戶。  
    1. 選擇 "在左側功能表列中**創建資源**"。  "**新建**"窗格（邊欄）將向右顯示。
    2. 在 **"新建"** 窗格中選擇 **"存儲**"。  **右側將顯示"精選**"清單。
    3. 選擇 **"存儲帳戶**"以開始創建帳戶。  按照文章"[創建存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)"中的說明進行操作。

    ![建立 Azure 儲存體帳戶的步驟](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    有關存儲帳戶的詳細資訊，請選擇[快速入門教程](https://docs.microsoft.com/azure/storage/)。  如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

4. 等待存儲帳戶預配，此過程通常需要幾分鐘時間。  然後，通過選擇 **"查看所有資源**"或從 Azure 門戶的左側導航功能表列中選擇 **"所有資源**"，從 Azure 門戶的**主頁**訪問存儲帳戶。

    ![訪問 Azure 存儲帳戶](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 從存儲帳戶窗格中，選擇 **"訪問金鑰"** 並複製金鑰的連接*字串*值。 保存此值，因為這是存儲*帳戶連接字串*值，您需要在發佈門戶中提供該值才能接收市場產品的潛在客戶。

     連接刺痛的一個示例是：

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 從存儲帳戶頁面中，選擇**Blob**。

   ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. 在 Blob 上一次，選擇 **"+ 容器"** 按鈕。

8. 鍵入新容器**的名稱**。 容器名稱必須是小寫，以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。 有關容器和 blob 名稱的詳細資訊，請參閱[命名和引用容器、blob 和中繼資料](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

    保存此值，因為這是您需要在發佈門戶中提供的*容器名稱*值，以便接收市場產品/服務的潛在客戶。

9. 將對容器的公共存取層級設置為 **"私有"（無匿名存取）。**

10. 選取 [確定]**** 以建立容器。

    ![新容器](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>將產品/服務配置為將潛在客戶發送到 Azure Blob

準備好在發佈門戶中配置產品/服務的首席管理資訊時，請按照以下步驟操作：

1. 導航到產品 **/服務"產品/服務"設置**頁面。
2. 在"潛在客戶管理"部分下選擇 **"連接**"。

    ![連接優惠](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. 在"連接詳細資訊"快顯視窗上，為潛在客戶目標選擇**Azure Blob。**

    ![連接詳細資訊](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 提供您按照這些說明提供的**容器名稱**和**存儲帳戶連接字串**。

    * 容器名稱示例：`marketplaceleadcontainer`
    * 存儲帳戶連接字串示例：`DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`![連接詳細資訊](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. 選取 [儲存]****。

    > [!NOTE]
    > 您必須完成配置產品/服務的其餘部分併發布它，然後才能收到產品/服務的潛在客戶。


