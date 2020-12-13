---
title: 登入 LUIS 入口網站
description: 如果您是登入 LUIS 入口網站的新使用者，登入體驗會根據您目前的使用者帳戶而稍有不同。
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: b8382b76496976054ebb452e39866765d986ccbb
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368168"
---
# <a name="sign-in-to-luis-portal"></a>登入 LUIS 入口網站

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

您可以使用本文來開始使用 LUIS 入口網站，並建立撰寫資源。 完成本文中的步驟之後，您將能夠建立併發布 LUIS 應用程式。

## <a name="access-the-portal"></a>存取入口網站


1. 若要開始使用 LUIS，請前往 [LUIS 入口網站](https://www.luis.ai)。 如果您還沒有訂用帳戶，系統會提示您建立 [免費帳戶](https://azure.microsoft.com//free/cognitive-services/) ，並回到入口網站。
2. 重新整理頁面，以新建立的訂用帳戶更新它
3. 從下拉式清單中選取您的訂用帳戶

    > [!div class="mx-imgBorder"]
    > ![選取訂用帳戶](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. 如果您的訂用帳戶位於另一個租使用者下，您將無法從現有的視窗切換租使用者。 您可以關閉此視窗，並選取最右邊的圖片，在頂端列中選取包含縮寫的最右邊的圖片，以切換租使用者。 按一下頂端的 **[選擇不同的撰寫資源** ]，重新開啟視窗。

    > [!div class="mx-imgBorder"]
    > ![切換目錄](./media/migrate-authoring-key/switch-directories.png)

5. 如果您有與訂用帳戶相關聯的現有 LUIS 撰寫資源，請從下拉式清單中選擇它。 您可以查看在此撰寫資源下建立的所有應用程式。
6. 如果沒有，請按一下此強制回應底部的 [ **建立新的撰寫資源** ]。
7.  在建立新的撰寫資源時，請提供下列資訊：

    > [!div class="mx-imgBorder"]
    > ![建立新資源](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **租使用者名稱** -與您的 Azure 訂用帳戶相關聯的租使用者。 您將無法從現有的視窗切換租使用者。 您可以關閉此視窗，並選取畫面右上角的 [圖片] （包含您的姓名縮寫）來切換租使用者。 選取頂端的 **[選擇不同的撰寫資源** ]，以重新開啟視窗。
    * **Azure 資源組名** -您在訂用帳戶中選擇的自訂資源組名。 資源群組可讓您將 Azure 資源分組以方便存取和管理。 如果您的訂用帳戶中目前沒有資源群組，您將無法在 LUIS 入口網站中建立一個資源群組。 移至 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) 建立一個，然後移至 LUIS 以繼續登入程式。
    * **Azure 資源名稱** -您選擇的自訂名稱，用來作為撰寫交易 URL 的一部分。 您的資源名稱只可包含英數位元、 `-` 和開頭或結尾不能是 `-` 。 如果名稱中包含任何其他符號，建立資源將會失敗。
    * **位置** -選擇以 LUIS 目前支援的 [三個撰寫位置](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions) 之一來撰寫您的應用程式，包括：美國西部、西歐和澳大利亞東部
    * **定價層** -依預設會選取 F0 撰寫定價層，因為這是建議的選項。 如果您要尋找額外的安全性層級，請從 Azure 入口網站建立 [客戶管理的金鑰](https://docs.microsoft.com/azure/cognitive-services/luis/luis-encryption-of-data-at-rest#customer-managed-keys-for-language-understanding) 。
8. 現在您已成功登入 LUIS。 您現在可以開始建立應用程式。

## <a name="troubleshooting"></a>疑難排解

* 建立新的資源時，請確定資源名稱只包含英數位元、'-'，而且不能以 '-' 開頭或結尾。 否則會失敗。
* 請確定您擁有訂用帳戶的 [適當許可權，才能建立 Azure 資源](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。 如果您沒有適當的許可權，請洽詢訂用帳戶的管理員，以提供您足夠的許可權。

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [啟動新的應用程式](luis-how-to-start-new-app.md)
