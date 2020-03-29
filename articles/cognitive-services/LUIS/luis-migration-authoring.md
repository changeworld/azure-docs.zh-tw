---
title: 遷移到 Azure 資源進行創作
titleSuffix: Azure Cognitive Services
description: 遷移到 Azure 創作資源金鑰。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194504"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>遷移到 Azure 資源創作金鑰

語言理解 （LUIS） 創作身份驗證從電子郵件帳戶更改為 Azure 資源。 雖然當前不需要，但將來將強制執行切換到 Azure 資源。

## <a name="why-migrate"></a>為何要移轉？

使用 Azure 資源進行創作允許您作為資源的擁有者來控制對創作的訪問。 您可以創建創作資源並將其命名為創作資源，以管理不同的作者組。

例如，您是 2 個 LUIS 應用的擁有者，並且每個應用都有不同的成員是協作者。 您可以創建兩個不同的創作資源，並將每個應用分配給每個單獨的資源。 然後，根據每個成員協作使用的應用，將每個成員分配為適當創作資源的參與者。 Azure 創作資源控制授權。

> [!Note]
> 在遷移之前，共同作者稱為 LUIS 應用級別的_協作者_。 遷移後，_參與者_的 Azure 角色用於相同的功能，但在 Azure 資源級別上。

## <a name="what-is-migrating"></a>什麼是遷移？

遷移包括：

* LUIS 的所有使用者、擁有者和貢獻者。
* **所有**應用。
* **單向**遷移。

擁有者無法選擇要遷移的應用子集，並且該過程不可逆。

遷移不是：

* 收集協作者並自動移動或添加到 Azure 創作資源的過程。 作為應用擁有者，您需要完成此步驟。 此步驟需要對相應資源的許可權。
* 創建和分配預測運行時資源的過程。 如果需要預測運行時資源，這是[一個單獨的進程](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)，並且保持不變。

## <a name="how-are-the-apps-migrating"></a>應用程式如何遷移？

[LUIS 門戶](https://www.luis.ai)提供遷移過程。

如果：

* 電子郵件身份驗證系統上有用於創作的應用。
* 您是應用程式擁有者。

您可以通過取消視窗來延遲遷移過程。 定期要求您遷移，直到遷移或遷移截止時間過去。 可以從頂部巡覽列的鎖定圖示開始遷移過程。

## <a name="migration-for-the-app-owner"></a>應用擁有者的遷移

### <a name="before-you-migrate"></a>遷移前

* **必需**，您需要具有[Azure 訂閱](https://azure.microsoft.com/free/)。 訂閱過程的一部分確實需要計費資訊。 但是，您可以使用 LUIS 時的免費`F0`（ ） 定價層。
* **或者，** 通過匯出每個應用或使用匯出[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)從 LUIS 門戶的應用清單中備份應用。
* **可以選擇**，保存每個應用程式的協作者清單。 此電子郵件清單作為遷移過程的一部分提供。


**創作您的 LUIS 應用程式是免費的**，由`F0`層指示。 [瞭解有關定價層的更多。](luis-boundaries.md#key-limits)

如果沒有 Azure 訂閱，[請註冊](https://azure.microsoft.com/free/)。

### <a name="migration-steps"></a>移轉步驟

按照[這些遷移步驟](luis-migration-authoring-steps.md)操作。

### <a name="after-you-migrate"></a>遷移後

遷移過程後，所有 LUIS 應用現在都分配給單個 LUIS 創作資源。

您可以創建更多創作資源，並從_LUIS 門戶_中的 **"管理-> Azure 資源**"頁進行分配。

您可以在該資源的**存取控制 （IAM）** 頁上從_Azure 門戶_向創作資源添加參與者。 有關詳細資訊，請參閱[添加參與者存取權限](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)。

|入口網站|目的|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|• 創建預測和創作資源。<br>• 分配貢獻者。|
|[路易士](https://www.luis.ai)|• 遷移到新的創作資源。<br>• 從**管理 -> Azure 資源**頁分配或取消分配預測和創作資源到應用。|

## <a name="migration-for-the-app-contributor"></a>應用參與者的遷移

LUIS 的每個使用者都需要遷移，包括協作者/貢獻者。 協作者必須遷移才能訪問應用。

> [!Note]
> 如果 LUIS 應用的擁有者遷移並添加了協作者作為 Azure 資源上的參與者，則協作者仍無權訪問該應用，除非它們也遷移。

### <a name="before-the-app-is-migrated"></a>遷移應用之前

您可以選擇匯出您是協作者的應用，然後將應用導入 LUIS。 導入過程創建具有新應用 ID 的新應用，您是該應用 ID 的擁有者。

### <a name="after-the-app-is-migrated"></a>遷移應用後

應用擁有者需要[將電子郵件添加到 Azure 創作資源作為協作者](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)。

遷移過程後，您擁有的任何應用都可以在 LUIS 門戶的"**我的應用"** 頁面上使用。

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>為 LUIS 創作解決遷移過程的疑難排解

* LUIS 創作金鑰僅在遷移過程完成後在 LUIS 門戶中可見。 如果創建創作金鑰（例如使用 LUIS CLI），則使用者仍然需要在 LUIS 門戶中完成遷移過程。
* 如果遷移的使用者在其 Azure 資源上添加非遷移使用者作為參與者，則非遷移使用者將無權訪問應用，除非它們遷移。
* 如果非遷移使用者不是任何應用的擁有者，而是其他人擁有的其他應用的協作者，並且擁有者已經歷遷移過程，則此使用者將需要遷移才能訪問這些應用。
* 如果非遷移使用者將另一個遷移使用者作為協作者添加到其應用，則會發生錯誤，因為您將無法將遷移的使用者作為協作者添加到應用。 然後，非遷移使用者必須完成遷移過程並創建 Azure 資源，並將遷移的使用者添加為該資源的參與者。

如果在以下情況：以下情況，則在遷移過程中收到錯誤：
* 您的訂閱不授權您創建認知服務資源
* 您的遷移會對任何應用程式運行時產生負面影響。 遷移時，任何協作者都將從你的應用中刪除，您將從其他應用中刪除作為協作者。 此過程意味著您分配的金鑰也會被刪除。 如果您在其他應用中分配了金鑰，則遷移將被阻止。 在遷移之前，請安全地刪除您分配的金鑰。 如果您知道分配的金鑰在運行時未使用，則需要將其刪除才能在遷移中進行。

使用以下 URL 格式訪問應用的 Azure 資源清單：

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>後續步驟

* [如何將應用遷移到創作資源](luis-migration-authoring-steps.md)
