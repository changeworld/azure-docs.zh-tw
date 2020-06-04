---
title: 遷移至 Azure 撰寫資源
titleSuffix: Azure Cognitive Services
description: 遷移至 Azure 撰寫資源。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 62f049624f4ad7ff7ce6d685b08c54875aab86f0
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84345218"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>遷移至 Azure 撰寫資源的步驟

從 Language Understanding （LUIS）入口網站，遷移您擁有的所有應用程式，以使用 Azure 撰寫資源。

## <a name="prerequisites"></a>必要條件

* **（選擇性**）藉由匯出每個應用程式或使用匯出[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)，從 LUIS 入口網站的應用程式清單備份應用程式。
* **（選擇性**）儲存每個應用程式的 collaborator's 清單。 所有共同作業者都可以在遷移過程中傳送電子郵件。
* **必要**，您必須擁有[Azure 訂](https://azure.microsoft.com/free/)用帳戶。 訂用帳戶程式的一部分需要帳單資訊。 不過，當您使用 LUIS 時，您可以使用免費（F0）定價層。 當您的使用量增加時，您最終可能會發現需要付費層。

如果您沒有 Azure 訂用帳戶，請[註冊](https://azure.microsoft.com/free/)。

## <a name="access-the-migration-process"></a>存取遷移程式

每週會提示您遷移應用程式。 您可以取消此視窗而不進行遷移。 如果您想要在下一個排定的期間之前遷移，可以從 LUIS 入口網站頂端工具列上的**Azure**圖示開始進行遷移程式。

> [!div class="mx-imgBorder"]
> ![遷移圖示](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>應用程式擁有者開始進行遷移流程

如果您是任何 LUIS 應用程式的擁有者，就可以使用此遷移程式。

1. 登入[LUIS 入口網站](https://www.luis.ai)並同意使用條款。
1. [遷移] 快顯視窗可讓您在稍後繼續進行遷移或遷移。 選取 [**立即遷移**]。 如果您選擇稍後進行遷移，您有9個月的時間可遷移至 Azure 中的新撰寫金鑰。

    ![在 [遷移程式] 中的第一個快顯視窗，選取 [立即遷移]。](./media/migrate-authoring-key/migrate-now.png)

1. （選擇性）如果您的任何應用程式有共同作業者，系統會提示您**傳送電子郵件給他們**，讓他們知道遷移的相關資訊。 這是選擇性步驟。

    將您的帳戶遷移至 Azure 之後，共同作業者就無法再使用您的應用程式。

    針對每個共同作業者和應用程式，預設的電子郵件應用程式會以輕量格式的電子郵件開啟。 您可以在傳送電子郵件前先進行編輯。

    電子郵件範本包含正確的應用程式識別碼和應用程式名稱。

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. 選擇使用現有的撰寫資源或建立新的撰寫資源，以建立 LUIS 撰寫資源。

    > [!div class="mx-imgBorder"]
    > ![建立撰寫資源](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. 在下一個視窗中，輸入您的資源金鑰資訊。 輸入資訊之後，請選取 [**建立資源**]。 每個訂用帳戶的每個區域可以有10個免費的撰寫資源。

    ![建立撰寫資源](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    在**建立新的撰寫資源**時，請提供下列資訊：

    * **資源名稱** - 您選擇的自訂名稱，用來作為撰寫和預測端點查詢 URL 的一部分。
    * **租用戶** - Azure 訂用帳戶所關聯的租用戶。
    * **訂用帳戶名稱** - 要作為資源收費對象的訂用帳戶。
    * **資源群組** - 您選擇或建立的自訂資源群組名稱。 資源群組可讓您將 Azure 資源分組以方便存取和管理。
    * **位置** - 位置選擇是以**資源群組**選取項目作為依據。
    * **定價層** - 定價層會決定每秒和每月的交易上限。

1. 驗證您的撰寫資源並**立即遷移**。

    ![建立撰寫資源](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. 建立撰寫資源時，會顯示成功訊息。 選取 [**關閉**] 以關閉快顯視窗。

    ![已成功建立您的撰寫資源。](./media/migrate-authoring-key/migration-success.png)

    [**我的應用程式**] 清單會顯示遷移至新撰寫資源的應用程式。

    您不需要知道撰寫資源的金鑰，即可在 LUIS 入口網站中繼續編輯您的應用程式。 如果您想要以程式設計方式編輯應用程式，您需要撰寫金鑰值。 這些值會顯示在 LUIS 入口網站的 [**管理-> Azure 資源**] 頁面上，也可以在資源的 [**金鑰**] 頁面上的 Azure 入口網站中取得。

1. 存取您的應用程式之前，請先選取訂用帳戶，然後 LUIS 撰寫資源]，以查看您可以撰寫的應用程式。

    > [!div class="mx-imgBorder"]
    > ![選取 [訂用帳戶] 和 [LUIS 撰寫資源]，以查看您可以撰寫的應用程式。](./media/create-app-in-portal-select-subscription-luis-resource.png)

## <a name="app-contributor-begins-the-migration-process"></a>應用程式參與者開始進行遷移流程

遵循與應用程式擁有者相同的步驟來進行遷移。 此程式會建立種類的新撰寫資源 `LUIS.Authoring` 。

您必須遷移您的帳戶，才能將其新增為其他人所擁有之已遷移應用程式的參與者。

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>在遷移程式之後，將參與者新增至您的撰寫資源

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

瞭解[如何新增參與者](luis-how-to-collaborate.md)。

## <a name="troubleshooting-errors-with-the-migration-process"></a>針對遷移程式的錯誤進行疑難排解

如果您在 `MissingSubscriptionRegistration` LUIS 入口網站中于遷移程式期間收到含有紅色通知列的錯誤，請在[Azure 入口網站](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)或[Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli)中建立認知服務資源。 深入瞭解[此錯誤的原因](../../azure-resource-manager/templates/error-register-resource-provider.md#cause)。

## <a name="next-steps"></a>後續步驟


* 查看撰寫和執行時間金鑰的相關[概念](luis-concept-keys.md)
* 回顧[如何指派金鑰](luis-how-to-azure-subscription.md)和加入[參與者](luis-how-to-collaborate.md)
