---
title: 商業 marketplace 方案中的 Azure 資料表 |Azure Marketplace
description: 設定 Azure Blob 的潛在客戶管理
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285243"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Azure Blob 的潛在客戶管理指示

>[!Caution]
>用來處理來自 marketplace 供應專案之潛在客戶的 Azure Blob 選項已淘汰。 如果您目前有使用 Azure Blob 潛在客戶管理設定發佈的供應專案，您就不會再收到潛在客戶。 請將您的潛在客戶管理設定更新為任何其他潛在客戶管理選項。 瞭解 [[潛在客戶管理] 登陸頁面](./commercial-marketplace-get-customer-leads.md)上的其他選項。

如果合作夥伴中心未明確支援您的客戶關係管理（CRM）系統來接收 Azure Marketplace 和 AppSource 潛在客戶，您可以使用 Azure Blob 來處理這些潛在客戶。 然後您可以選擇匯出資料，並將它匯入到您的 CRM 系統。 本文中的指示將提供您建立 Azure 儲存體帳戶的程式，以及該帳戶下的 Azure Blob。 此外，您可以使用 Microsoft Flow 建立新的流程，以在您的供應專案收到潛在客戶時傳送電子郵件通知。


## <a name="how-to-configure-azure-blob"></a>如何設定 Azure Blob

1. 如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
1. 當您的 Azure 帳戶處於作用中狀態時，請登入[Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站中，使用下列程式來建立儲存體帳戶。  
    1. 選取左側功能表列中的 [ **+ 建立資源**]。  **新**的窗格（blade）就會顯示在右邊。
    2. 在 [**新增**] 窗格中選取 [**儲存體**]。  **精選**清單會顯示在右側。
    3. 選取要開始帳戶建立的**儲存體帳戶**。  遵循[建立儲存體帳戶一](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)文中的指示。

    ![建立 Azure 儲存體帳戶的步驟](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    如需儲存體帳戶的詳細資訊，請選取[快速入門教學](https://docs.microsoft.com/azure/storage/)課程。  如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

4. 等到您的儲存體帳戶布建完成，通常需要幾分鐘的時間。  接著，選取 [**查看您的所有資源**]，或從 Azure 入口網站的左側導覽功能表列中選取 [**所有資源**]，以從 Azure 入口網站的**首頁**存取您的儲存體帳戶。

    ![存取您的 Azure 儲存體帳戶](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 從 [儲存體帳戶] 窗格中，選取 [**存取金鑰**]，並複製金鑰的*連接字串*值。 儲存此值，因為這是您必須在發佈入口網站中提供的*儲存體帳戶連接字串*值，才能接收 marketplace 供應專案的潛在客戶。

     連接字串的範例如下：

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 從 [儲存體帳戶] 頁面中，選取 [ **blob**]。

   ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. 一旦在 [blob] 頁面上，選取 [ **+ 容器**] 按鈕。

8. 輸入新容器的**名稱**。 容器名稱必須是小寫，以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。 如需關於容器和 Blob 名稱的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

    儲存此值，因為這是您必須在發佈入口網站中提供的*容器名稱*值，才能接收 marketplace 供應專案的潛在客戶。

9. 將容器的公用存取層級設定為 [**私用（沒有匿名存取）**]。

10. 選取 [確定]  以建立容器。

    ![新增容器](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>設定您的供應專案以將潛在客戶傳送至 Azure Blob

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時，請遵循下列步驟：

1. 流覽至供應專案的 [**供應專案設定**] 頁面。
2. 選取 [潛在客戶管理] 區段下的 **[連接]** 。

    ![連接供應專案](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. 在 [連線詳細資料] 快顯視窗中，選取 [ **Azure Blob** ] 作為 [潛在客戶目的地]。

    ![連接詳細資料](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 提供您遵循這些指示所取得的**容器名稱**和**儲存體帳戶連接字串**。

    * 容器名稱範例：`marketplaceleadcontainer`
    * 儲存體帳戶連接字串範例： `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![連接詳細資料](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. 選取 [儲存]  。

    > [!NOTE]
    > 您必須先完成供應專案的其餘部分設定並加以發佈，才能接收供應專案的潛在客戶。


