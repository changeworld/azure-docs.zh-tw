---
title: 如何建立 Azure 支援要求
description: 需要協助的客戶可以使用 Azure 入口網站來尋找自助式解決方案，以及建立和管理支援要求。
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 06/25/2020
ms.openlocfilehash: 11ca7925ce1664b5586ab8ec0fb523a2d562ee80
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745582"
---
# <a name="create-an-azure-support-request"></a>建立 Azure 支援要求

Azure 可讓您建立及管理支援要求，也稱為支援票證。 您可以在 [Azure 入口網站](https://portal.azure.com)中建立及管理要求，如本文所述。 您也可以使用 [Azure 支援票證 REST API](/rest/api/support)，以程式設計方式建立和管理要求。

> [!NOTE]
> Azure 入口網站 URL 是您的組織部署所在的 Azure 雲端專用。
>
>* 商業用途的 Azure 入口網站是：[https://portal.azure.com](https://portal.azure.com)
>* 德國的 Azure 入口網站是：[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 美國政府的 Azure 入口網站是：[https://portal.azure.us](https://portal.azure.us)

支援要求體驗著重于三個主要目標：

* **簡化**：將支援和疑難排解的尋找變得更容易，並簡化提交支援要求的方式。
* **整合**：當您針對 Azure 資源進行疑難排解時，可以很簡單地開啟支援要求，而不需要轉換操作情境。
* **有效**︰收集支援工程師所需的重要資訊，有效率地解決問題。

Azure 為訂用帳戶管理提供無限制的支援，包括帳單、配額調整、帳戶轉換等。 如需技術支援，您必須加入支援計劃。 如需詳細資訊，請參閱[比較支援方案](https://azure.microsoft.com/support/plans)。

## <a name="getting-started"></a>開始使用

移至 Azure 入口網站中的 [說明 + 支援]。 您可以從 Azure 入口網站的功能表、全域標頭、或服務的資源功能表前往 [說明 + 支援]。 您必須擁有適當的權限，才能提出支援要求。

### <a name="azure-role-based-access-control"></a>Azure 角色型存取控制

若要建立支援要求，您必須是[擁有者](../../role-based-access-control/built-in-roles.md#owner)、[參與者](../../role-based-access-control/built-in-roles.md#contributor)，或是獲派訂用帳戶層級的[支援要求參與者](../../role-based-access-control/built-in-roles.md#support-request-contributor)角色。 若要建立不含訂用帳戶的支援要求，例如 Azure Active Directory 案例，您必須是系統 [管理員](../../active-directory/roles/permissions-reference.md)。

### <a name="go-to-help--support-from-the-global-header"></a>從全域標頭進入 [說明 + 支援]

從 Azure 入口網站中任意處開啟支援要求：

1. 選取 [?] (位於全域標頭中)。 然後選取 [說明 + 支援]。

   ![說明與支援](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. 選取 [新增支援要求]。 遵循提示來提供問題的相關資訊。 我們會建議一些可能的解決辦法，收集有關問題的詳細資訊，並協助您提交及追蹤支援要求。

   ![新的支援要求](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>從資源功能表前往 [說明 + 支援]

若要在資源的操作情境中啟動支援要求，您目前要處理：

1. 從支援功能表，在 [支援 + 疑難排解] 區段中，選取 [新的支援要求]。

   ![在內容中](./media/how-to-create-azure-support-request/incontext2lower.png)

1. 遵循提示，提供您問題的相關資訊給我們。 當您從資源啟動支援要求程序時，系統會為您預先選擇一些選項。

## <a name="create-a-support-request"></a>建立支援要求

我們將引導您完成一些步驟，以收集有關問題的資訊，並協助您解決問題。 以下各節說明每個步驟。

### <a name="basics"></a>基本概念

支援要求程序的第一個步驟會收集您的問題和支援計劃的基本資訊。

在 [新的支援要求] 的 [基本資訊] 中，使用選取器開始描述問題。 首先，您要區別問題類型的一些常規分類，然後選擇相關的訂用帳戶。 選取服務，例如執行 **Windows 的虛擬機器**。 選取資源，例如，您的虛擬機器名稱。 以您自己的單字描述問題，然後選取 [ **問題類型** ] 和 [ **問題子類型** ] 以取得更具體的資訊。

![基本概念刀鋒視窗](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>方案

收集基本資訊後，我們接著會向您顯示您可自行嘗試的解決辦法。 在某些情況下，我們甚至會執行快速診斷。 解決辦法是由 Azure 工程師編寫，可解決大多數的常見問題。

### <a name="details"></a>詳細資料

接下來，我們會收集有關該問題的更多詳細資訊。 在這個步驟中提供完整且詳細的資訊，可協助我們將您的支援要求路由至正確的工程師。

1. 如果可能，請告訴我們問題何時開始發生以及能重現問題的任何步驟。 您可以上傳檔案，例如記錄檔、診斷的輸出。 如需檔案上傳的詳細資訊，請參閱檔案 [上傳指導方針](how-to-manage-azure-support-request.md#file-upload-guidelines)。

1. 提供有關問題的所有資訊之後，請選擇如何獲得支援。 在 [詳細資料] 的 [支援方法] 區段中，選取影響的嚴重性。 最大嚴重性層級取決於您的 [支援方案](https://azure.microsoft.com/support/plans)。

    預設會選取 [ **共用診斷資訊** ] 選項。 這可讓 Azure 支援從您的 Azure 資源收集 [診斷資訊](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 。 在某些情況下，預設不會選取第二個問題，例如要求存取虛擬機器的記憶體。

1. 提供您偏好的聯絡方法、適當的聯絡時間以及您的支援語言。

1. 然後，完成 [聯絡資訊] 區段讓我們知道如何聯絡您。

### <a name="review--create"></a>檢閱 + 建立

完成每個索引標籤中的所有必要資訊，然後選取 [檢閱 + 建立]。 請查看您將傳送以支援的詳細資料。 如有需要，可回到任何索引標籤進行所需的變更。 當您滿意支援要求的填寫內容後，選取 [建立]。

支援工程師會使用您所指定的方法來與您聯繫。 如需初始回應時間的詳細資訊，請參閱 [支援範圍和回應](https://azure.microsoft.com/support/plans/response/)性。


## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 中的自助式支援選項，請看這支影片：

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

若要深入瞭解，請遵循下列連結：

* [如何管理 Azure 支援要求](how-to-manage-azure-support-request.md)
* [Azure 支援票證 REST API](/rest/api/support)
* [將您的意見反應和建議傳送給我們](https://feedback.azure.com/forums/266794-support-feedback)
* 在 [Twitter](https://twitter.com/azuresupport) 上與我們互動
* 在 [Microsoft 問與答頁面](/answers/products/azure)中從同儕獲得幫助
* 深入瞭解 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq)