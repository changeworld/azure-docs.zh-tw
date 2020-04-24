---
title: 遷移至 Azure 資源以供撰寫
titleSuffix: Azure Cognitive Services
description: 遷移至 Azure 撰寫資源金鑰。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: 679073715588a4a81e69e3e7ba2d18341b1bab4b
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096617"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>遷移至 Azure 資源撰寫金鑰

Language Understanding （LUIS）編寫驗證從電子郵件帳戶變更為 Azure 資源。 目前不需要切換至 Azure 資源，未來將會強制執行。

## <a name="why-migrate"></a>為何要移轉？

使用 Azure 資源進行撰寫，可讓您（身為資源的擁有者）控制撰寫的存取權。 您可以建立及命名撰寫資源，以管理不同的作者群組。

例如，您是2個 LUIS 應用程式的擁有者，而且您有不同的成員是每個應用程式的共同作業者。 您可以建立兩個不同的撰寫資源，並將每個應用程式指派給每個個別的資源。 然後將每個成員指派為適當撰寫資源的參與者，視其共同作業的應用程式而定。 Azure 撰寫資源會控制授權。

> [!Note]
> 在遷移之前，共同作者在 LUIS 應用層_級上稱為_共同作業者。 遷移之後，「_參與者_」的 azure 角色會用於相同的功能，但在 azure 資源層級上。

## <a name="what-is-migrating"></a>什麼是遷移？

遷移包括：

* LUIS、擁有者和參與者的所有使用者。
* **所有**應用程式。
* **單向**遷移。

擁有者無法選擇要遷移的應用程式子集，而且進程無法復原。

遷移不是：

* 收集共同作業者並自動移動或新增至 Azure 撰寫資源的程式。 身為應用程式擁有者，您必須完成此步驟。 此步驟需要適當資源的許可權。
* 建立和指派預測執行時間資源的進程。 如果您需要預測執行時間資源，這是[個別的進程](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)，而且不會變更。

## <a name="how-are-the-apps-migrating"></a>應用程式的遷移方式為何？

[LUIS 入口網站](https://www.luis.ai)提供了遷移程式。

在下列情況中，系統會要求您進行遷移：

* 您在電子郵件驗證系統上有用於撰寫的應用程式。
* 而且您是應用程式擁有者。

您可以取消視窗，以延遲遷移程式。 系統會定期要求您進行遷移，直到您遷移或通過遷移期限為止。 您可以從上方導覽列的鎖定圖示開始進行遷移程式。

## <a name="migration-for-the-app-owner"></a>應用程式擁有者的遷移

### <a name="before-you-migrate"></a>在您遷移之前

* **必要**，您必須擁有[Azure 訂](https://azure.microsoft.com/free/)用帳戶。 訂用帳戶程式的一部分需要帳單資訊。 不過，當您使用 LUIS 時，`F0`可以使用免費（）定價層。
* **（選擇性**）藉由匯出每個應用程式或使用匯出[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)，從 LUIS 入口網站的應用程式清單備份應用程式。
* **（選擇性**）儲存每個應用程式的 collaborator's 清單。 此電子郵件清單是在遷移過程中提供。


`F0` **撰寫您的 LUIS 應用程式是免費**的，由層級表示。 深入瞭解[定價層](luis-limits.md#key-limits)。

如果您沒有 Azure 訂用帳戶，請[註冊](https://azure.microsoft.com/free/)。

### <a name="migration-steps"></a>移轉步驟

請遵循[這些遷移步驟](luis-migration-authoring-steps.md)。

### <a name="after-you-migrate"></a>遷移之後

在遷移程式之後，您的所有 LUIS apps 現在都會指派給單一 LUIS 撰寫資源。

您可以從_LUIS 入口網站_中的 [**管理-> Azure 資源**] 頁面，建立更多撰寫資源並加以指派。

您可以在該資源的 [**存取控制（IAM）** ] 頁面上，從_Azure 入口網站_將參與者新增至撰寫資源。 如需詳細資訊，請參閱[新增參與者存取](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)。

|入口網站|用途|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* 建立預測和撰寫資源。<br>* 指派參與者。|
|[LUIS](https://www.luis.ai)|* 遷移至新的撰寫資源。<br>* 從 [**管理-> Azure 資源**] 頁面，指派或取消指派預測和撰寫資源給應用程式。|

## <a name="migration-for-the-app-contributor"></a>應用程式參與者的遷移

LUIS 的每個使用者都需要遷移，包括共同作業者/參與者。 共同作業者必須進行遷移，才能存取應用程式。

> [!Note]
> 如果 LUIS 應用程式的擁有者遷移並將共同作業者新增為 Azure 資源的參與者，則共同作業者仍無法存取應用程式，除非他們也進行遷移。

### <a name="before-the-app-is-migrated"></a>遷移應用程式之前

您可以選擇匯出您是共同作業者的應用程式，然後將應用程式匯入回 LUIS。 匯入程式會使用您為擁有者的新應用程式識別碼來建立新的應用程式。

### <a name="after-the-app-is-migrated"></a>在應用程式遷移之後

應用程式擁有者必須[將您的電子郵件新增至 Azure 撰寫資源，做為](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)共同作業者。

在遷移程式之後，您擁有的任何應用程式都可以在 LUIS 入口網站的 [**我的應用程式**] 頁面上取得。

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>針對 LUIS 撰寫的遷移程式進行疑難排解

* LUIS 撰寫金鑰只有在完成遷移程式之後，才會顯示在 LUIS 入口網站中。 如果您建立撰寫金鑰（例如使用 LUIS CLI），使用者仍然需要在 LUIS 入口網站中完成遷移程式。
* 如果遷移的使用者在其 azure 資源上將非遷移的使用者新增為參與者，則非遷移的使用者將無法存取應用程式，除非他們遷移。
* 如果非遷移的使用者不是任何應用程式的擁有者，但是其他人擁有的其他應用程式共同作業者，而且擁有者已完成遷移程式，則此使用者必須遷移才能存取應用程式。
* 如果未遷移的使用者已將另一個遷移的使用者新增為其應用程式的共同作業者，將會發生錯誤，因為您無法將已遷移的使用者新增為應用程式的共同作業者。 然後，非遷移的使用者必須完成遷移程式並建立 azure 資源，並將遷移後的使用者新增為該資源的參與者。

您會在遷移程式期間收到錯誤，如下所示：
* 您的訂用帳戶未授權您建立認知服務資源
* 您的遷移對任何應用程式執行時間會有負面影響。 在遷移時，會從您的應用程式移除任何共同作業者，並將您從其他應用程式移除為共同作業者。 此程式表示您指派的金鑰也會被移除。 如果您已在其他應用程式中指派金鑰，則會封鎖遷移。 請先移除您已安全指派的金鑰，再進行遷移。 如果您知道您所指派的金鑰未在執行時間中使用，則必須將它移除，才能在遷移中進行。

使用下列 URL 格式存取您應用程式的 Azure 資源清單：

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>後續步驟

* [如何將您的應用程式遷移至撰寫資源](luis-migration-authoring-steps.md)
