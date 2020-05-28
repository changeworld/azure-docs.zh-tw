---
title: 如何管理設定？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 如何在自訂翻譯工具中管理設定、建立工作區、共用工作區，以及管理訂用帳戶金鑰。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 50bff8e55d1b125f56696ac485bc1e05193dea55
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992941"
---
# <a name="how-to-manage-settings"></a>如何管理設定

在 [自訂翻譯工具設定] 頁面中，您可以建立新的工作區、共用您的工作區，以及新增或修改您的 Microsoft 翻譯訂用帳戶金鑰。

若要存取設定頁面：

1. 登入[自訂翻譯工具](https://portal.customtranslator.azure.ai/)入口網站。
2. 在自訂翻譯工具入口網站中，按一下資訊看板中的齒輪圖示。

    ![設定連結](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>建立 Microsoft Translator 訂用帳戶的關聯

您需要有與工作區相關聯的 Translator 訂用帳戶金鑰，才能定型或部署模型。

如果您沒有訂用帳戶，請依照下列步驟執行：

1. 訂閱翻譯工具。 本文說明如何訂閱翻譯工具。
2. 記下翻譯工具訂用帳戶的金鑰。 可用的值為 Key1 或 Key2。
3. 瀏覽回自訂翻譯工具入口網站。

### <a name="add-existing-key"></a>新增現有金鑰

1.    流覽至工作區的 [設定] 頁面。
2.    按一下 [新增金鑰]

   ![如何新增訂用帳戶金鑰](media/how-to/how-to-add-subscription-key.png)

3. 在對話方塊中，輸入翻譯工具訂用帳戶的金鑰，然後按一下 [新增] 按鈕。

    ![如何新增訂用帳戶金鑰](media/how-to/how-to-add-subscription-key-dialog.png)
4.    新增金鑰之後，您可以隨時修改或刪除金鑰。

   ![新增之後的訂用帳戶金鑰](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>管理您的工作區

工作區是用來撰寫和建置自訂翻譯系統的工作區域。 工作區可包含多個專案、模型和文件。

如果您工作的不同部分需要與不同的人員共用，建立多個工作區可能有所幫助。

## <a name="create-a-new-workspace"></a>建立新的工作區

1.    流覽至工作區的 [設定] 頁面。
2.    按一下 [建立新的工作區] 區段中的 [新增工作區] 按鈕。

   ![建立新的工作區](media/how-to/create-new-workspace.png)

4.    在對話方塊中，輸入新工作區的名稱。
5.    按一下 [建立]。

   ![建立新的工作區對話方塊](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>共用您的工作區

在「自訂翻譯工具」中，如果您工作的不同部分需要與不同的人員共用，您可以與他人共用您的工作區。

1.    流覽至工作區的 [設定] 頁面。
2.    按一下 [共用設定] 區段中的 [共用] 按鈕。

   ![共用工作區](media/how-to/share-workspace.png)

3.    在對話方塊中，輸入可共用此工作區的電子郵件地址清單 (以逗點分隔)。 請確實以人員用來登入自訂翻譯工具的電子郵件地址作為您的共用對象。 然後，選取共用權限的適當層級。

4.    如果您的工作區仍然具有預設名稱「我的工作區」，您必須在共用您的工作區之前，先加以變更。
5.    按一下 [儲存]。

## <a name="sharing-permissions"></a>共用權限

1.    **讀取者：** 工作區中的讀取者將可檢視工作區中的所有資訊。

2.    **編輯者：** 工作區中的編輯者將可新增文件、定型模型，以及刪除文件和專案。 他們可以新增訂用帳戶金鑰，但無法修改工作區的共用對象、刪除工作區或變更工作區名稱。

3.    **擁有者：** 擁有者具有工作區的完整權限。

## <a name="change-sharing-permission"></a>變更共用權限

共用工作區時，[共用設定] 區段會顯示與此工作區共用的所有電子郵件地址。 如果您具有工作區的擁有者存取權，您可以變更每個電子郵件地址的現有共用權限。

1.    在每個電子郵件的 [共用設定] 區段中，下拉式功能表會顯示目前的許可權等級。

2.    按一下下拉式功能表，然後選取要指派給該電子郵件地址的新權限層級。

   ![共用權限設定](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>後續步驟

- 了解[如何移轉您的工作區和專案](how-to-migrate.md) (從 [Microsoft Translator 中樞](https://hub.microsofttranslator.com))
