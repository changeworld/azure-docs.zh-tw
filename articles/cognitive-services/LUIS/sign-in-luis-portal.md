---
title: 登入 LUIS 入口網站
description: 如果您是登入 LUIS 入口網站的新使用者，登入體驗會根據您目前的使用者帳戶而稍有不同。
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: 22f5ab332c52a3b567b37089def6e4a494a15d29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309467"
---
# <a name="sign-in-to-luis-portal"></a>登入 LUIS 入口網站

如果您是登入 LUIS 入口網站的新使用者，登入體驗會根據您目前的使用者帳戶而稍有不同：
  * 與 Azure 訂用帳戶相關聯
  * 未與 Azure 訂用帳戶建立關聯

## <a name="determine-account-type"></a>判斷帳戶類型

當您第一次登入 LUIS 入口網站時，請使用下列視覺指示器來決定帳戶類型。

### <a name="account-without-azure-subscription"></a>沒有 Azure 訂用帳戶的帳戶

未與 Azure 訂用帳戶相關聯的帳戶，在右上方導覽列中會有 Azure 圖示。 一旦您遷移至相關聯的帳戶類型，就不會再顯示圖示。

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="使用 Azure 圖示 LUIS 巡覽列的部分螢幕擷取畫面。":::

### <a name="account-with-azure-subscription"></a>使用 Azure 訂用帳戶的帳戶

與 Azure 訂用帳戶相關聯的帳戶，可讓您選取要使用的訂用帳戶和資源。

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="使用 Azure 圖示 LUIS 巡覽列的部分螢幕擷取畫面。":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>使用與 Azure 訂用帳戶相關聯的帳戶登入

1. 登入 [LUIS 入口網站](https://www.luis.ai) ，並同意使用規定。

1. 您將會有兩個註冊選項：

    * 繼續使用 Azure 資源，這是建議的路徑，很快就會是唯一可用的路徑。 當您在訂用帳戶中選擇現有的資源，或建立新的資源時，此路徑可讓您將 LUIS 帳戶連結至 Azure 撰寫資源。 這相當於註冊遷移，而不需要在稍後進行 [遷移](luis-migration-authoring.md#what-is-migration) 程式。 所有使用者都必須在2020年11月2日之前進行遷移。

    * 繼續使用入門或試用版金鑰。 此路徑可讓您使用所提供的入門或試用版資源來登入 LUIS，而不需要建立任何資源。 如果您選擇此路徑，最終將需要 [遷移您的帳戶](luis-migration-authoring.md#migration-steps) ，並將您的應用程式連結至撰寫資源。 這就是為什麼建議您選擇繼續使用 Azure 資源的路徑。

    [深入瞭解撰寫和入門金鑰](luis-how-to-azure-subscription.md#luis-resources)。 這兩個資源提供您1000000的免費撰寫交易和1000個免費的預測端點交易。

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="使用 Azure 圖示 LUIS 巡覽列的部分螢幕擷取畫面。":::

1. 使用現有的撰寫資源

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="使用 Azure 圖示 LUIS 巡覽列的部分螢幕擷取畫面。":::

    如果您在訂用帳戶中已經有 LUIS 撰寫資源，並且在登入期間將一個資源與您的 LUIS 帳戶建立關聯，請選擇 [ **使用現有的撰寫資源** ] 選項，並提供下列資訊：

    * **租用戶** - Azure 訂用帳戶所關聯的租用戶。 您將無法從現有的視窗切換租使用者。 您可以藉由選取最右邊的圖片（包含您在頂端列的縮寫）來切換租使用者。
    * **訂** 用帳戶名稱-將與資源相關聯的訂用帳戶。 如果您有多個屬於您租使用者的訂用帳戶，請從下拉式清單中選取您想要的訂用帳戶。
    * **資源名稱** -您想要與您的帳戶相關聯的撰寫資源。

    > [!Note]
    > 如果您要查看的撰寫資源在下拉式清單中呈現灰色，這表示您已登入不同的區域入口網站。 [瞭解區域入口網站的概念](luis-reference-regions.md#luis-authoring-regions)。

1. 建立新的撰寫資源

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="使用 Azure 圖示 LUIS 巡覽列的部分螢幕擷取畫面。":::

    在**建立新的撰寫資源**時，請提供下列資訊：

    * **租用戶** - Azure 訂用帳戶所關聯的租用戶。 您將無法從現有的視窗切換租使用者。 您可以藉由選取最右邊的圖片（包含您在頂端列的縮寫）來切換租使用者。
    * **資源名稱** -您選擇的自訂名稱，用來作為撰寫交易 URL 的一部分。 您的資源名稱只能包含英數位元、'-'，且開頭或結尾不能是 '-'。 如果名稱中包含任何其他符號，建立資源將會失敗。
    * **訂** 用帳戶名稱-將與資源相關聯的訂用帳戶。 如果您有多個屬於您租使用者的訂用帳戶，請從下拉式清單中選取您想要的訂用帳戶。
    * **資源群組** -您在訂用帳戶中選擇的自訂資源組名。 資源群組可讓您將 Azure 資源分組以方便存取和管理。 如果您的訂用帳戶中目前沒有資源群組，您將無法在 LUIS 入口網站中建立一個資源群組。 移至 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) 建立一個，然後移至 LUIS 以繼續登入程式。

1. 選擇您的路徑之後，可能需要幾秒鐘的時間，才會出現表示「您的帳戶已成功遷移」的符號。 選取 [ **繼續**] 以完成。

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="使用 Azure 圖示 LUIS 巡覽列的部分螢幕擷取畫面。":::

    > [!Note]
    > 如果您的訂用帳戶和至少一個在入口網站中註冊所在區域的撰寫資源，您可能會自動登入 LUIS，並與資源建立關聯，而不需要選擇要進入哪一個路徑。


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>使用未與 Azure 訂用帳戶相關聯的使用者帳戶登入

1. 登入 [LUIS 入口網站](https://www.luis.ai) ，並確認您同意使用條款。

1. 選取 [ **繼續**] 以完成。 您將會使用試用/入門金鑰自動登入。 這表示，最終您將需要 [遷移您的帳戶](luis-migration-authoring.md#migration-steps) ，並將您的應用程式連結至撰寫資源。 若要進行遷移程式，您將需要登入 [Azure 免費試用版](https://azure.microsoft.com/free/)。

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="使用 Azure 圖示 LUIS 巡覽列的部分螢幕擷取畫面。":::

## <a name="troubleshooting"></a>疑難排解

* 如果您在與您要登入的入口網站不同的區域中，從 Azure 入口網站建立撰寫資源，撰寫資源將呈現灰色。
* 建立新的資源時，請確定資源名稱只包含英數位元、'-'，而且不能以 '-' 開頭或結尾。 否則會失敗。
* 請確定您擁有訂用帳戶的 [適當許可權，才能建立 Azure 資源](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。 如果您沒有適當的許可權，請洽詢訂用帳戶的管理員，以提供您足夠的許可權。

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [啟動新的應用程式](luis-how-to-start-new-app.md)
