---
title: 如何要求增加 Azure Cosmos DB 資源的配額
description: 瞭解如何要求增加 Azure Cosmos DB 資源的配額。 您也將瞭解如何啟用訂用帳戶來存取區域。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: cd2bc3ec7e70049ee7e2c700731515a272e541dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096195"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>如何要求增加 Azure Cosmos DB 資源的配額

Azure Cosmos DB 中的資源有 [預設的配額/限制](concepts-limits.md)。 不過，在某些情況下，您的工作負載所需的配額會超過預設值。 在這種情況下，您必須與 Azure Cosmos DB 小組聯繫，要求增加配額。 本文說明如何要求增加 Azure Cosmos DB 資源的配額。 您也將瞭解如何啟用訂用帳戶來存取區域。

## <a name="create-a-new-support-request"></a>建立新的支援要求

若要要求增加配額，您必須使用您的工作負載詳細資料來建立新的支援要求。 Azure Cosmos DB 團隊接著會評估您的要求並核准它。 使用下列步驟，從 Azure 入口網站建立新的支援要求：

1. 登入 Azure 入口網站。

1. 從左側功能表中，選取 [說明 **+ 支援** ]，然後選取 [ **新增支援要求**]。

1. 在 [ **基本** ] 索引標籤中，填入下列詳細資料：

   * 針對**問題類型**，選取 [服務與訂用帳戶限制 (配額)] 
   * 針對 [ **訂**用帳戶]，選取您要增加配額的訂用帳戶。
   * 針對 [ **配額類型**]，選取 [ **Cosmos DB**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="建立新的 Cosmos DB 支援要求增加配額":::

1. 在 [ **詳細資料** ] 索引標籤中，輸入對應至您配額要求的詳細資料。 此索引標籤上提供的資訊將用來進一步評估您的問題，並協助支援工程師針對問題進行疑難排解。

1. 在此表單中填入下列詳細資料：

   * **描述**：提供要求的簡短描述，例如您的工作負載，為什麼預設值不夠。 根據您想要增加配額的資源類型，您必須在 [ **描述** ] 欄位中提供下列詳細資料：

     **區域要求** 如果您的要求對應至將區域新增至允許清單，請務必提供下列值：

        * 區域名稱
        * 訂用帳戶識別碼

     **輸送量限制要求** 如果您的要求對應至增加輸送量的配額，請務必提供下列值：

        * 資料庫名稱
        * 訂用帳戶識別碼
        * 新的輸送量限制

     **資料庫帳戶限制要求** 如果您的要求對應至增加訂用帳戶中資料庫帳戶數目的配額，請務必提供下列值：

       * 訂用帳戶識別碼
       * 新增資料庫帳戶限制

   * 檔案**上傳**：上傳診斷檔案或您認為與支援要求相關的任何其他檔案。 若要深入瞭解檔案上傳的指引，請參閱 [Azure 支援]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) 文章。

   * **嚴重性**：根據業務影響，選擇其中一個可用的嚴重性層級。

   * **慣用的連絡人方法**：您可以選擇透過 **電子郵件** 或 **電話**聯絡。

1. 填寫剩餘的詳細資料，例如您的可用性、支援語言、連絡人資訊、電子郵件，以及表單上的電話號碼。

1. 選取 **[下一步]： [檢查 + 建立]**。 驗證所提供的資訊，然後選取 [ **建立** ] 以建立支援要求。

在24小時內，Azure Cosmos DB 支援小組將會評估您的要求，並取回您的要求。

## <a name="next-steps"></a>接下來的步驟

* 請參閱 [Azure Cosmos DB 預設服務配額](concepts-limits.md)
