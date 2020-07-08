---
title: 使用 Azure Blob 儲存體進行潛在客戶管理 - Microsoft 商業市集
description: 了解如何使用 Azure Blob 來針對 Microsoft AppSource 和 Azure Marketplace 設定潛在客戶
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: dsindona
ms.openlocfilehash: 471675de58161fc0c0110e2968089da062c6da87
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960557"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>使用 Azure Blob 儲存體來管理商業市集潛在客戶

>[!Caution]
>適用於 Azure Blob 儲存體的商業市集支援已經淘汰，且已不是處理您供應項目之潛在客戶的可用選項。 如果您目前有已針對 Azure Blob 設定潛在客戶管理的商業市集供應項目，您將不會繼續收到潛在客戶。 請將您的潛在客戶管理設定更新為任何其他潛在客戶管理選項。 在[潛在客戶管理登陸頁面](./commercial-marketplace-get-customer-leads.md)上了解其他選項。

 如果您的客戶關係管理 (CRM) 系統在合作夥伴中心中尚未明確支援接收 Microsoft AppSource 和 Azure Marketplace 潛在客戶，您可以使用 Azure Blob 儲存體。 您接著可以選擇匯出該資料，並將其匯入到您的 CRM 系統。 此文章中的指示將會引導您完成建立 Azure 儲存體帳戶，並在該帳戶底下建立 Blob 的程序。 此外，您可以使用 Power Automate 建立新的流程，來讓系統在您的供應項目接收到潛在客戶時傳送電子郵件通知。

>[!NOTE]
>若要使用在這些指示中所用 Power Automate 連接器，需具備 Power Automate 的付費訂用帳戶。 請先確認您的帳戶是否符合此條件，再遵循此文章中的指示。

## <a name="configure-azure-blob-storage"></a>設定 Azure Blob 儲存體

1. 如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。

2. 在您的 Azure 帳戶啟用之後，請登入 [Azure 入口網站](https://portal.azure.com)。

3. 在 Azure 入口網站中，使用下列程序建立儲存體帳戶。  
    1. 在左側功能表列中，選取 [+ 建立資源]。  [新增] 窗格 (刀鋒視窗) 將會顯示於右側。
    2. 在 [新增] 窗格中，選取 [儲存體]。  [精選] 清單會顯示於右側。
    3. 選取 [儲存體帳戶] 以開始建立帳戶。  遵循[建立儲存體帳戶](../../storage/common/storage-quickstart-create-account.md?tabs=azure-portal) \(部分機器翻譯\) 一文中的指示。

    ![建立 Azure 儲存體帳戶的步驟](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    如需有關儲存體帳戶的詳細資訊，請參閱此[快速入門](../../storage/blobs/storage-quickstart-blobs-portal.md)。  如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

4. 等候系統將您的儲存體帳戶佈建完成，此程序通常需要花費幾分鐘的時間。  然後從 Azure 入口網站的 [首頁] 存取您的儲存體帳戶，方法是選取 [查看所有資源]，或是從 Azure 入口網站的左側導覽功能表列選取 [所有資源]。

    ![存取您的 Azure 儲存體帳戶](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 從您的儲存體帳戶窗格，選取 [存取金鑰] 並複製該金鑰的 [連接字串] 值。 請儲存此值，因為其為您必須在發佈入口網站中加以提供，以便接收您市集供應項目之潛在客戶的「儲存體帳戶連接字串」。

     以下是連接字串的範例：

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 從您的儲存體帳戶頁面，選取 [Blob]。

   ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. 在 Blob 頁面上，選取 [+ 容器] 按鈕。

8. 輸入新容器的 [名稱]。 容器名稱必須是小寫，以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。 如需關於容器和 Blob 名稱的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

    請儲存此值，因為其為您必須在發佈入口網站中加以提供，以便接收您市集供應項目之潛在客戶的「容器名稱」。

9. 將容器的公用存取層級設定為 [私人 (無匿名存取)]。

10. 選取 [確定] 以建立容器。

    ![新增容器](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>設定您的供應項目以將潛在客戶傳送到 Azure Blob 儲存體

當您準備好在發佈入口網站中設定供應項目的潛在客戶管理資訊時，請遵循下列步驟：

1. 瀏覽到您供應項目的 [供應項目設定] 頁面。
2. 在 [潛在客戶] 區段底下，選取 [連線]。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="潛在客戶":::

3. 在 [連線詳細資料] 快顯視窗上，選取 [Azure Blob] 作為 [潛在客戶目的地]。

    ![連線詳細資料](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 提供您透過遵循這些指示所取得的 [容器名稱] 和 [儲存體帳戶連接字串]。

    * 容器名稱範例：`marketplaceleadcontainer`
    * 儲存體帳戶連接字串範例：`DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![連線詳細資料](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. 選取 [儲存]。

    > [!NOTE]
    > 您必須完成供應項目的其餘設定並加以發佈，才能接收到該供應項目的潛在客戶。


