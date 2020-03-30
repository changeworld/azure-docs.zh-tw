---
title: 如何建立 Azure 支援要求 | Microsoft Docs
description: 需要幫助的客戶可以使用 Azure 門戶查找自助服務解決方案以及創建和管理支援請求。
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248485"
---
# <a name="how-to-create-an-azure-support-request"></a>如何建立 Azure 支援要求

## <a name="overview"></a>總覽

Azure 使您能夠創建和管理支援請求（也稱為支援票證）。 您可以在[Azure 門戶](https://portal.azure.com)中創建和管理請求，本文將介紹這一點。 您還可以使用[Azure 支援票證 REST API](/rest/api/support)以程式設計方式創建和管理請求。

> [!NOTE]
> Azure 門戶 URL 特定于部署組織的 Azure 雲。
>
>* 用於商業用途的 Azure 門戶是：[https://portal.azure.com](https://portal.azure.com)
>* 德國的 Azure 門戶是：[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 美國政府的 Azure 門戶為：[https://portal.azure.us](https://portal.azure.us)
>
>

根據客戶回函，我們更新了支援請求體驗，專注于三個主要目標：

* **簡化 ：** 使支援和故障排除易於查找，並簡化提交支援請求的方式。
* **集成**：在未切換上下文的情況下，在排除 Azure 資源的問題時，可以輕鬆地打開支援請求。
* **高效**：收集支援代理有效解決問題所需的關鍵資訊。

## <a name="getting-started"></a>開始使用

您可以在 Azure 門戶中獲取**説明和支援**。 它在 Azure 門戶功能表、全域標頭或服務的資源功能表中可用。 在提交支援請求之前，您必須具有適當的許可權。

### <a name="role-based-access-control"></a>角色型存取控制

要創建支援請求，您必須是管理員，或者被分配到訂閱級別的["支援請求參與者"](../../role-based-access-control/built-in-roles.md#support-request-contributor)角色。

### <a name="go-to-help--support-from-the-global-header"></a>轉到"説明" 從全域標頭獲得支援

要從 Azure 門戶中的任意位置啟動支援請求，請執行以下任務：

1. 選擇 **？** 在全域標頭中。 然後選擇 **"説明+支援**"。

   ![說明與支援](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. 選取 [新增支援要求]****。 按照提示向我們提供有關您的問題的資訊。 我們將建議一些可能的解決方案，收集有關問題的詳細資訊，並説明您提交和跟蹤支援請求。

   ![新的支援要求](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>轉到資源功能表中的説明和支援

要在資源上下文中啟動支援請求，您當前正在處理：

1. 在資源功能表中，在 **"支援 + 故障排除"** 部分中，選擇 **"新建支援請求**"。

   ![在內容中](./media/how-to-create-azure-support-request/incontext2lower.png)

2. 按照提示向我們提供有關您遇到的問題的資訊。 從資源啟動支援請求過程時，會為您預先選擇某些選項。

## <a name="create-a-support-request"></a>建立支援要求

我們將引導您完成一些步驟，收集有關您的問題的資訊，並説明您解決問題。 每個步驟都介紹在以下各節中。

### <a name="basics"></a>基本概念

支援要求程序的第一個步驟會收集您的問題和支援計劃的基本資訊。

在新**支援請求**的 **"基本"** 選項卡上，使用選擇器開始告訴我們有關問題。 首先，您將為問題類型標識一些常規類別，並選擇相關的訂閱。 選擇服務，例如運行**Windows 的虛擬機器**。 選擇資源，如虛擬機器的名稱。 用您自己的詞語描述問題，然後**選擇問題類型**以變得更加具體。

![基本概念刀鋒視窗](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure 提供對訂閱管理的無限制支援，包括計費、配額調整和帳戶轉移。 如需技術支援，您必須加入支援計劃。 [深入了解支援計劃](https://azure.microsoft.com/support/plans)。
>
>

### <a name="solutions"></a>方案

收集基本資訊後，我們接下來將向您展示自行嘗試的解決方案。 在某些情況下，我們甚至可以運行快速診斷。 解決方案由 Azure 工程師編寫，將解決最常見的問題。

### <a name="details"></a>詳細資料

接下來，我們收集有關問題的其他詳細資訊。 在此步驟中提供全面而詳細的資訊有助於我們將您的支援請求路由到正確的代理。

如果可能，請告訴我們問題何時開始，以及重現問題的任何步驟。 您可以上載檔，例如日誌檔或診斷輸出。

獲得有關問題的所有資訊後，選擇如何獲得支援。 在 **"詳細資訊****"的"支援方法**"部分中，選擇影響的嚴重性。 提供您首選的連絡方式、與您聯繫的好時機以及您的支援語言。

接下來，完成**聯繫資訊**部分，以便了解如何與您聯繫。

### <a name="review--create"></a>檢閱 + 建立

在每個選項卡上填寫所有必需的資訊，然後選擇 **"審閱 + 創建**"。 檢查您將發送給支援部門的詳細資訊。 如果需要，返回任何選項卡進行更改。 滿足支援請求完成後，請選擇 **"創建**"。

支援代理將使用您指示的方法與您聯繫。 有關初始回應時間的資訊，請參閱[支援範圍和回應能力](https://azure.microsoft.com/support/plans/response/)。

## <a name="all-support-requests"></a>所有支援要求

您可以通過訪問 **"説明 + 支援** >  **所有支援"請求**來查看支援請求的詳細資訊和狀態。

![所有支援要求](./media/how-to-create-azure-support-request/allrequestslower.png)

在此頁上，您可以按**訂閱**、**創建**日期 （UTC） 和**狀態**篩選支援請求。 此外，您可以在此頁面上排序和搜尋支援要求。

選擇支援請求以查看詳細資訊，包括嚴重性以及支援代理回應所需的預期時間。

如果要變更要求的嚴重性，請選擇 **"業務影響**"。 從要分配的分量清單中選擇。

> [!NOTE]
> 最高嚴重性層級取決於您的支援計劃。 [深入了解支援計劃](https://azure.microsoft.com/support/plans)。
>
>
要瞭解有關 Azure 中的自助支援選項的詳細資訊，請觀看此視頻：

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>後續步驟

* [向我們發送您的回饋和建議](https://feedback.azure.com/forums/266794-support-feedback)
* [在推特上與我們](https://twitter.com/azuresupport)互動
* 在[MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure)中從您的同行那裡獲得説明
* 在[Azure 支援常見問題解答](https://azure.microsoft.com/support/faq)中瞭解更多資訊
