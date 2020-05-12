---
title: 使用警示隱藏規則，在 Azure 資訊安全中心中隱藏誤報或其他不必要的安全性警示。
description: 本文說明如何使用 Azure 資訊安全中心的隱藏規則來隱藏不必要的安全性警示。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 5a5d6a96bd73a67c2611e7f334eba3daeda031ba
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007696"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>隱藏來自 Azure 資訊安全中心威脅防護的警示

此頁面說明如何使用警示隱藏規則，在 Azure 資訊安全中心中隱藏誤報或其他不必要的安全性警示。

## <a name="availability"></a>可用性

- 發行狀態：**預覽**
- 必要的角色：安全性系統管理員和擁有者可以建立/刪除規則。 安全性讀取者和讀者可以查看規則。
- 雲端：全部（全球、國家、政府和主權）


## <a name="introduction-to-suppression-rules"></a>隱藏規則簡介

Azure 資訊安全中心的威脅防護元件會偵測環境中任何區域的威脅，並產生安全性警示。

當單一警示不是有趣或相關時，您可以手動將它關閉。 或者，使用隱藏規則功能，在未來自動關閉類似的警示。 一般來說，您會使用隱藏規則來執行下列動作：

- 隱藏已識別為誤報的警示

- 隱藏太常觸發的警示

您的隱藏專案規則會定義應自動關閉警示的準則。

> [!CAUTION]
> 隱藏安全性警示可減少資訊安全中心的威脅防護。 您應該仔細檢查任何隱藏規則的潛在影響，並在一段時間內加以監視。

[![具有警示隱藏選項的 Azure 資訊安全中心安全性警示頁面](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>建立隱藏專案規則

有幾種方式可以建立規則來隱藏不必要的安全性警示：

- 若要隱藏管理群組層級的警示，請使用 Azure 原則

- 若要隱藏訂用帳戶層級的警示，您可以使用 Azure 入口網站或 REST API，如下所述

隱藏式規則只能關閉已在選取的訂閱上觸發的警示。

若要直接在 Azure 入口網站中建立規則：

1. 從資訊安全中心的 [安全性警示] 頁面：

    - 找出您不想再看到的特定警示，並從警示的省略號功能表（...）選取 [**建立隱藏專案規則**]：

        [![* * 建立隱藏規則 * * 選項](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - 或者，選取頁面頂端的 [**隱藏規則**] 連結，然後從 [隱藏規則] 頁面中選取 [**建立新的隱藏專案規則**]：

        ![建立新的隱藏專案規則 * * 按鈕](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. 在 [新增隱藏規則] 窗格中，輸入新規則的詳細資料。

    - 您的規則可以關閉**所有資源**上的警示，如此一來，您就不會在未來收到任何警示。 
    
    - 當您的規則與特定的 IP 位址、進程名稱、使用者帳戶、Azure 資源或位置相關時，您可以**根據特定準則**來關閉警示。

    > [!TIP]
    > 如果您從特定警示開啟 [新增規則] 頁面，警示和訂用帳戶將會自動在您的新規則中設定。 如果您使用 [**建立新的隱藏] 規則**連結，選取的訂用帳戶會符合入口網站中目前的篩選準則。

    [![隱藏規則建立窗格](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. 輸入規則的詳細資料：

    - **名稱**-規則的名稱。 規則名稱必須以字母或數位開頭，介於2到50個字元之間，且不包含連字號（-）或底線（_）以外的符號。 
    - **狀態**為已啟用或已停用。
    - **原因**-如果不符合您的需求，請選取其中一個內建原因或 [其他]。
    - **到期日**-規則的結束日期和時間。 規則最多可以執行六個月。

1. （選擇性）使用 [**模擬**] 按鈕測試規則，以查看如果此規則已啟用，有多少警示已關閉。

1. 儲存規則。 

## <a name="editing-suppression-rules"></a>編輯隱藏專案規則

若要編輯您已建立的規則，請使用 [隱藏規則] 頁面。

1. 從資訊安全中心的 [安全性警示] 頁面中，選取頁面頂端的 [**隱藏規則**] 連結。

1. [隱藏規則] 頁面隨即開啟，並根據目前選取的訂用帳戶來列出所有可用的規則。 

    [![隱藏專案規則清單](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. 若要編輯單一規則，請開啟規則的省略號功能表（...），然後選取 [**編輯**]。

1. 進行必要的變更，然後**選取 [** 套用]。 

## <a name="deleting-suppression-rules"></a>刪除隱藏專案規則

若要刪除您已建立的一或多個規則，請使用 [隱藏規則] 頁面。

1. 從資訊安全中心的 [安全性警示] 頁面中，選取頁面頂端的 [**隱藏規則**] 連結。

1. [隱藏規則] 頁面隨即開啟，並根據目前選取的訂用帳戶來列出所有可用的規則。 

1. 若要刪除單一規則，請開啟規則的省略號功能表（...），然後選取 [**刪除**]。

1. 若要刪除多個規則，請選取要刪除之規則的核取方塊，然後選取 [**刪除**]。

    ![刪除一個或多個隱藏專案規則](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>查看已隱藏的警示

仍會產生符合已啟用隱藏專案規則的警示，但其狀態會設定為 [已**關閉**]。 您可以在 Azure 入口網站中看到狀態，或存取您資訊安全中心的安全性警示。 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)不會針對隱藏的警示建立事件。 針對其他 Siem，您可以使用警示的狀態（「已關閉」）來篩選隱藏的警示。

使用資訊安全中心的篩選器來查看規則已關閉的警示。

* 從資訊安全中心的 [安全性警示] 頁面開啟篩選選項，然後選取 [已**解除**]。  

   [![查看已關閉的警示](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>使用 API 建立和管理隱藏專案規則

您可以透過資訊安全中心的 REST API 來建立、查看或刪除警示隱藏規則。 

REST API 中隱藏規則的相關 HTTP 方法為：

- **PUT**：在指定的訂用帳戶中建立或更新隱藏專案規則。

- **GET**：

    - 列出針對指定的訂用帳戶所設定的所有規則。 這個方法會傳回適用規則的陣列。

    - 取得指定訂用帳戶的特定規則詳細資料。 這個方法會傳回一個隱藏專案規則。

    - 模擬隱藏項規則在設計階段的影響。 此呼叫會識別當規則已在使用中時，已關閉哪些現有警示。

- **DELETE**：刪除現有的規則（但不會變更其已關閉的警示狀態）。

如需完整詳細資料和使用方式範例，請參閱[API 檔](https://docs.microsoft.com/api/securitycenter/)。 


## <a name="next-steps"></a>後續步驟

本文說明 Azure 資訊安全中心中的隱藏規則，其會自動關閉不需要的警示。

如需 Azure 資訊安全中心中安全性警示的詳細資訊，請參閱下列頁面：

- [安全性警示和意圖終止鏈](alerts-reference.md)-您可能會在 Azure 資訊安全中心的威脅防護課程模組中看到的安全性警示參考指南。
- [Azure 資訊安全中心中的威脅防護](threat-protection.md)-描述您的環境中，由 Azure 資訊安全中心的威脅防護模組監視的許多層面。