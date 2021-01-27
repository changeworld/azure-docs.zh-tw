---
title: 如何管理設定？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 如何在自訂翻譯工具中管理設定、建立工作區、共用工作區，以及管理訂用帳戶金鑰。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895776"
---
# <a name="how-to-manage-settings"></a>如何管理設定

在 [自訂翻譯設定] 頁面中，您可以共用您的工作區、修改 Translator 訂用帳戶金鑰，以及刪除工作區。

若要存取設定頁面：

1. 登入[自訂翻譯工具](https://portal.customtranslator.azure.ai/)入口網站。
2. 在自訂翻譯工具入口網站中，按一下資訊看板中的齒輪圖示。

    ![設定連結](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>建立 Translator 訂用帳戶的關聯

您必須擁有與工作區相關聯的 Translator 訂用帳戶金鑰，才能定型或部署模型。

如果您沒有訂用帳戶，請遵循下列步驟：

1. 訂閱以建立翻譯工具資源。 請依照 [如何註冊 translator](../translator-how-to-signup.md) 來訂閱和取得 translator 金鑰。
2. 記下 Translator 訂用帳戶的金鑰。 可用的值為 Key1 或 Key2。
3. 瀏覽回自訂翻譯工具入口網站。

## <a name="create-a-new-workspace"></a>建立新的工作區

1. 按一下「自訂翻譯」提要欄位中的 [+ 建立工作區] 按鈕。

    ![建立新的工作區](media/how-to/create-new-workspace.png)

2. 在對話方塊中，輸入新工作區的名稱。
3. 按一下 [下一步]。
4. 選擇訂用帳戶類型。
5. 選取訂用帳戶區域。 建立 Translator 資源金鑰時，區域必須符合選取的區域。
6. 輸入 translator 訂用帳戶的金鑰，然後按一下 [儲存] 按鈕。

    ![建立新的工作區對話方塊](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>自訂翻譯不支援為翻譯工具文字的 API 資源建立工作區 (也稱為 在 [啟用的 VNET](../../../api-management/api-management-using-with-vnet.md)內建立的 Azure 訂用帳戶金鑰) 。

### <a name="modify-existing-key"></a>修改現有金鑰

1. 瀏覽至工作區的 [設定] 頁面。
2. 按一下 [變更金鑰]

    ![如何新增訂用帳戶金鑰](media/how-to/how-to-add-subscription-key.png)

3. 在對話方塊中，輸入 Translator 訂用帳戶的金鑰，然後按一下 [儲存] 按鈕。

    ![如何新增訂用帳戶金鑰組話框](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>管理工作區

工作區是用來撰寫和建置自訂翻譯系統的工作區域。 工作區可包含多個專案、模型和文件。

如果您工作的不同部分需要與不同的人員共用，建立多個工作區可能有所幫助。

## <a name="share-your-workspace"></a>共用您的工作區

在「自訂翻譯工具」中，如果您工作的不同部分需要與不同的人員共用，您可以與他人共用您的工作區。

1. 瀏覽至工作區的 [設定] 頁面。
2. 按一下 [共用設定] 區段中的 [新增人員] 按鈕。

    ![共用工作區](media/how-to/share-workspace.png)

3. 在對話方塊中，輸入可共用此工作區的電子郵件地址清單 (以逗點分隔)。 請確實以人員用來登入自訂翻譯工具的電子郵件地址作為您的共用對象。 然後，選取適當的共用許可權層級，然後按一下 [儲存] 按鈕。

    ![[共用工作區] 對話方塊](media/how-to/share-workspace-dialog.png)

4. 如果您的工作區仍使用預設名稱「我的工作區」，您必須先加以變更，再共用您的工作區。
5. 按一下 [儲存]。

## <a name="sharing-permissions"></a>共用權限

1. **讀取者：** 工作區中的讀取者將可檢視工作區中的所有資訊。

2. **編輯者：** 工作區中的編輯者將可新增文件、定型模型，以及刪除文件和專案。 他們可以新增訂用帳戶金鑰，但無法修改工作區的共用對象、刪除工作區或變更工作區名稱。

3. **擁有者：** 擁有者具有工作區的完整權限。

## <a name="change-sharing-permission"></a>變更共用權限

共用工作區時，[共用設定] 區段會顯示與此工作區共用的所有電子郵件地址。 如果您具有工作區的擁有者存取權，您可以變更每個電子郵件地址的現有共用權限。

1. 在每個電子郵件的 [共用設定] 區段中，下拉式功能表會顯示目前的許可權層級。

2. 按一下下拉式功能表，然後選取要指派給該電子郵件地址的新權限層級。

    ![共用權限設定](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>釘選您的工作區

您的第一個建立的工作區預設為釘選。 每次登入時，您釘選的工作區都會顯示在網站負載上。 如果您已建立許多工作區，而且想要在登入時將其中一個預設值設為預設值，您必須將其釘選。

1. 在提要欄位中，按一下您要釘選的工作區名稱。
2. 流覽至工作區的 [設定] 頁面。
3. 按一下釘選圖示。

    ![釘選工作區](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>後續步驟

- 瞭解 [如何建立您的工作區和專案](workspace-and-project.md)