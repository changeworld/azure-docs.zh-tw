---
title: 使用警示歸併規則，在 Azure 資訊安全中心中隱藏誤判為真或其他不必要的安全性警示。
description: 本文說明如何使用 Azure 資訊安全中心的隱藏規則來隱藏不需要的 Azure Defender 警示
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/10/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: c4eb30df74e2a8d6748ede987df0b1c41cff0ca3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448476"
---
# <a name="suppress-alerts-from-azure-defender"></a>隱藏 Azure Defender 的警示

此頁面說明如何使用警示隱藏規則，從 Azure Defender 抑制誤報或其他不想要的安全性警示。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|預覽|
|定價：|免費<br> (大部分的安全性警示僅適用于 Azure Defender) |
|必要的角色和許可權：|**安全性系統管理員** 和 **擁有** 者可以建立/刪除規則。<br>**安全性讀取者**和**讀者**可以檢視規則。|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||


## <a name="what-are-suppression-rules"></a>什麼是隱藏專案規則？

不同的 Azure Defender 方案會偵測環境中任何區域的威脅，並產生安全性警示。

當單一警示不需關注或不相關時，您可以手動將其關閉。 或者，使用歸併規則功能，在未來自動關閉類似的警示。 一般來說，您會使用歸併規則來執行下列動作：

- 隱藏您已識別為誤報的警示

- 隱藏太頻繁觸發的警示很有用

您的歸併規則會定義應自動關閉警示的準則。

> [!CAUTION]
> 隱藏安全性警示可降低 Azure Defender 的威脅防護效率。 您應該仔細檢查任何歸併規則的潛在影響，並在一段時間內加以監視。

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="建立警示隱藏規則":::

## <a name="create-a-suppression-rule"></a>建立隱藏專案規則

您可以使用幾種方式來建立規則以隱藏不必要的安全性警示：

- 若要隱藏管理群組層級的警示，請使用 Azure 原則
- 若要隱藏訂用帳戶層級的警示，您可以使用 Azure 入口網站或 REST API，如下所述

歸併規則只能關閉所選訂用帳戶中已觸發過的警示。

直接在 Azure 入口網站中建立規則：

1. 從資訊安全中心的 [安全性警示] 頁面：

    - 找出您不想再看到的特定警示，並從警示的省略號功能表 (...)，選取 [建立歸併規則]：

        [![**建立歸併規則** 選項](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - 或者，選取頁面頂端的 [歸併規則] 連結，然後從 [歸併規則] 頁面中選取 [建立新的歸併規則]：

        ![[建立新的歸併規則]** 按鈕](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. 在 [新增歸併規則] 窗格中，輸入新規則的詳細資料。
    - 您的規則可以關閉 [所有資源] 上的警示，如此您在未來就不會收到任何類似警示。     
    - 當警示與特定的 IP 位址、程序名稱、使用者帳戶、Azure 資源或位置相關時，您的規則就可以關閉**特定準則上**的警示。

    > [!TIP]
    > 如果您從特定警示開啟 [新增規則] 頁面，將會自動在您的新規則中設定警示和訂用帳戶。 如果您使用 [建立新的歸併規則] 連結，則選取的訂用帳戶會符合入口網站中目前的篩選條件。

    [![歸併規則建立窗格](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. 輸入規則的詳細資料：
    - **名稱** - 規則的名稱。 規則名稱開頭必須為字母或數字，長度要介於 2 至 50 個字元之間，且不得包含破折號 (-) 或底線 (_) 以外的符號。 
    - **狀態** - 啟用或停用。
    - **原因** - 如果不符合您的需求，請選取其中一個內建原因或 [其他]。
    - **到期日** - 規則的結束日期和時間。 規則最多可以執行六個月。
1. (選擇性) 使用 [模擬] 按鈕來測試規則，以查看此規則已啟用時，會關閉的警示數目。
1. 儲存規則。 


## <a name="edit-a-suppression-rules"></a>編輯隱藏專案規則

若要編輯您所建立的規則，請使用隱藏規則頁面。

1. 從資訊安全中心的 [安全性警示] 頁面中，選取頁面頂端的 [歸併規則] 連結。
1. [隱藏性規則] 頁面隨即開啟，其中包含所選訂用帳戶的所有規則。

    [![歸併規則清單](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. 若要編輯單一規則，請開啟規則的省略號功能表 (...)，然後選取 [編輯]。
1. 進行必要的變更，然後選取 [套用]。 

## <a name="delete-a-suppression-rule"></a>刪除隱藏專案規則

若要刪除您已建立的一或多個規則，請使用 [歸併規則] 頁面。

1. 從資訊安全中心的 [安全性警示] 頁面中，選取頁面頂端的 [歸併規則] 連結。
1. [隱藏性規則] 頁面隨即開啟，其中包含所選訂用帳戶的所有規則。
1. 若要刪除單一規則，請開啟規則的省略號功能表 (...)，然後選取 [刪除]。
1. 若要刪除多個規則，請選取要刪除之規則的核取方塊，然後選取 [刪除]。
    ![刪除一個或多個歸併規則](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="view-suppressed-alerts"></a>查看隱藏的警示

仍會產生符合已啟用歸併規則的警示，但其狀態會設定為 [關閉]。 您可以在 Azure 入口網站中看到狀態，或您如何存取資訊安全中心的安全性警示。 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 不會針對隱藏的警示建立事件。 針對其他 SIEM，您可以使用警示的狀態 (「關閉」) 來篩選隱藏的警示。

使用資訊安全中心的篩選器來檢視規則已關閉的警示。

* 在資訊安全中心的 [安全性警示] 頁面上，開啟篩選選項，然後選取 [關閉]。  

   [![檢視已關閉的警示](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="create-and-manage-suppression-rules-with-the-api"></a>使用 API 建立和管理隱藏專案規則

您可以透過資訊安全中心的 REST API 來建立、檢視或刪除警示歸併規則。 

REST API 中歸併規則的相關 HTTP 方法為：

- **PUT**：在指定的訂用帳戶中建立或更新歸併規則。

- **GET**：

    - 列出針對所指定訂用帳戶設定的所有規則。 這個方法會傳回適用規則的陣列。

    - 取得指定訂用帳戶特定規則的詳細資料。 這個方法會傳回一個歸併規則。

    - 模擬歸併規則仍在設計階段的影響。 此呼叫會識別當規則已在使用中時，會關閉哪些現有警示。

- **DELETE**：刪除現有的規則 (但不會變更其已關閉的警示狀態)。

如需完整詳細資料和使用方式範例，請參閱 [API 文件](https://docs.microsoft.com/rest/api/securitycenter/)。 


## <a name="next-steps"></a>後續步驟

本文說明 Azure 資訊安全中心內的歸併規則，其會自動關閉不必要的警示。

如需 Azure Defender 安全性警示的詳細資訊，請參閱下列頁面：

- [安全性警示和意圖終止鏈](alerts-reference.md) -您可能會從 Azure Defender 取得之安全性警示的參考指南。