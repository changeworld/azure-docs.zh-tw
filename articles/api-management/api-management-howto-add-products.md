---
title: 教學課程 - 如何在 Azure API 管理中建立及發行產品
description: 在本教學課程中，您會在 Azure API 管理中建立及發行產品。 產品發行之後，開發人員就可以開始使用產品的 API。
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 2f298f240d8aa7a38b42a8c78ee3c90fe3423d10
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993545"
---
# <a name="tutorial-create-and-publish-a-product"></a>教學課程：建立和發行產品  

在 Azure API 管理中，[*產品*](api-management-terminology.md#term-definitions)包含一或多個 API，以及使用量配額與使用規定。 發行產品之後，開發人員便可訂閱產品，並開始使用產品的 API。  

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立和發行產品
> * 將 API 新增至產品

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="入口網站中的 API 管理產品":::


## <a name="prerequisites"></a>必要條件

+ 了解 [Azure API 管理術語](api-management-terminology.md)。
+ 完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。
+ 此外，請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

## <a name="create-and-publish-a-product"></a>建立和發行產品

1. 登入 Azure 入口網站，然後瀏覽至您的 API 管理執行個體。
1. 在左側導覽中，選取 [產品]  >  [+ 新增]。
1.  在 [新增產品] 視窗中，輸入下表所述的值，以建立您的產品。

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="在入口網站中新增產品":::

    | 名稱                     | 說明                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 顯示名稱             | 您想要其顯示在[開發人員入口網站](api-management-howto-developer-portal.md)中的名稱。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | 描述              | 提供產品的相關資訊，例如用途、提供存取權的 API，以及其他詳細資料。                                                                                                                                               |
    | State                    | 如果您想要發行產品，請選取 [發行]。 產品必須發行，才能呼叫產品中的 API。 依預設不會發行新產品，且只有 [Administrators] 群組才能看見。                                                                                      |
    | 需要訂用帳戶    | 如果使用者必須先訂閱才可使用產品，請加以選取。                                                                                                                                                                                                                                   |
    | 需要核准        | 如果您希望管理員檢閱並接受或拒絕對此產品的訂閱嘗試，請加以選取。 如果未選取，將會自動核准訂閱嘗試。                                                                                                                         |
    | 訂閱計數限制 | 選擇性限制多個訂閱同時進行的計數。                                                                                                                                                                                                                                |
    | 法律條款              | 您可以包含訂閱者必須接受才可使用產品的產品使用規定。                                                                                                                                                                                                             |
    | API                     | 選取一或多個 API。 您也可以在建立產品之後新增 API。 如需詳細資訊，請參閱本文稍後的[將API 新增至產品](#add-apis-to-a-product)。 |

3. 選取 [建立] 來建立新產品。

### <a name="add-more-configurations"></a>新增更多設定

儲存之後，繼續設定產品。 在您的 API 管理執行個體中，從 [產品] 視窗中選取產品。 新增或更新：


|項目   |描述  |
|---------|---------|
|設定     |    產品中繼資料和狀態     |
|API     |  與產品相關聯的 API       |
|[原則](api-management-howto-policies.md)     |  套用至產品 API 的原則      |
|存取控制     |  開發人員或來賓的產品可見度       |
|[訂用帳戶](api-management-subscriptions.md)    |    產品訂閱者     |

## <a name="add-apis-to-a-product"></a>將 API 加入至產品

產品是一或多個 API 的關聯。 您可以包括數個 API，並透過開發人員入口網站將它們提供給開發人員。 在產品建立期間，您可以新增一或多個現有的 API。 您可以在之後將 API 新增至產品中，可以從產品的 [設定] 頁面或建立 API 時進行新增。

開發人員必須先訂閱產品，才能取得 API 的存取權。 當他們訂閱時，就能取得適用於該產品中任何 API 的中訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此根據預設，您已訂閱每一個產品。

### <a name="add-an-api-to-an-existing-product"></a>將 API 新增至現有的產品


1. 在 API 管理執行個體的左側導覽中，選取 [產品]。
1. 選取產品，然後選取 [API]。
1. 選取 [+ 新增] 。
1. 選取一或多個 API，然後選取 [選取]。

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="將 API 新增至現有的產品":::

> [!TIP]
> 您可以透過 [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) 或 PowerShell 命令，使用自訂的訂用帳戶金鑰建立或更新使用者的產品訂用帳戶。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立和發行產品
> * 將 API 新增至產品

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [建立空白 API 和模擬 API 回應](mock-api-responses.md)
