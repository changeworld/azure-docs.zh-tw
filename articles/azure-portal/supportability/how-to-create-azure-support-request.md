---
title: 如何建立 Azure 支援要求 | Microsoft Docs
description: 需要協助的客戶可以使用 Azure 入口網站來尋找自助式解決方案，以及建立和管理支援要求。
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248485"
---
# <a name="how-to-create-an-azure-support-request"></a>如何建立 Azure 支援要求

## <a name="overview"></a>概觀

Azure 可讓您建立及管理支援要求，也稱為支援票證。 您可以在[Azure 入口網站](https://portal.azure.com)中建立和管理要求，這會在本文中討論。 您也可以使用[Azure 支援票證 REST API](/rest/api/support)，以程式設計方式建立和管理要求。

> [!NOTE]
> Azure 入口網站 URL 專屬於您的組織部署所在的 Azure 雲端。
>
>* 商業用途的 Azure 入口網站為： [https://portal.azure.com](https://portal.azure.com)
>* 適用于德國的 Azure 入口網站為： [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 美國政府的 Azure 入口網站為： [https://portal.azure.us](https://portal.azure.us)
>
>

根據客戶的意見反應，我們已更新支援要求體驗，著重于三個主要目標：

* **簡化**：讓您輕鬆尋找並簡化您提交支援要求的方式。
* **整合**. 當您在疑難排解 Azure 資源的問題時，您可以輕鬆地開啟支援要求，而不需要切換內容。
* **有效率**：收集支援代理程式所需的重要資訊，以有效率地解決您的問題。

## <a name="getting-started"></a>開始使用

您可以在 Azure 入口網站中取得**協助 + 支援**。 它可從 [Azure 入口網站] 功能表、全域標頭或服務的 [資源] 功能表取得。 您必須擁有適當的許可權，才能提出支援要求。

### <a name="role-based-access-control"></a>角色型存取控制

若要建立支援要求，您必須是系統管理員，或被指派給訂用帳戶層級的[支援要求參與者](../../role-based-access-control/built-in-roles.md#support-request-contributor)角色。

### <a name="go-to-help--support-from-the-global-header"></a>前往全域標頭中的 [說明 + 支援]

若要從 Azure 入口網站中的任何位置啟動支援要求：

1. 選取 [ **？** ] 在全域標頭中。 然後選取 [說明 **+ 支援**]。

   ![說明與支援](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. 選取 [新增支援要求]。 遵循提示，為我們提供您問題的相關資訊。 我們會建議一些可能的解決方案、收集問題的詳細資料，並協助您提交和追蹤支援要求。

   ![新的支援要求](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>前往資源功能表中的 [說明 + 支援]

若要在資源的內容中啟動支援要求，您目前使用的是：

1. 從 [資源] 功能表的 [**支援 + 疑難排解**] 區段中，選取 [**新增支援要求**]。

   ![在內容中](./media/how-to-create-azure-support-request/incontext2lower.png)

2. 遵循提示，為我們提供您所遇到之問題的相關資訊。 當您從資源啟動支援要求程式時，會為您預先選取一些選項。

## <a name="create-a-support-request"></a>建立支援要求

我們會引導您完成一些步驟，以收集問題的相關資訊，並協助您解決問題。 下列各節將說明每個步驟。

### <a name="basics"></a>基本概念

支援要求程序的第一個步驟會收集您的問題和支援計劃的基本資訊。

在**新支援要求**的 [**基本**] 索引標籤上，使用選取器來啟動，以告訴我們問題所在。 首先，您會識別問題類型的一些一般分類，然後選擇相關的訂用帳戶。 選取服務，例如執行 Windows 的**虛擬機器**。 選取資源，例如虛擬機器的名稱。 以您自己的單字描述問題，然後**選取 [問題類型**] 以取得更具體的資訊。

![基本概念刀鋒視窗](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure 提供不受限制的訂用帳戶管理支援，其中包括計費、配額調整和帳戶傳輸。 如需技術支援，您必須加入支援計劃。 [深入了解支援計劃](https://azure.microsoft.com/support/plans)。
>
>

### <a name="solutions"></a>方案

收集基本資訊之後，接下來我們會向您示範可自行嘗試的解決方案。 在某些情況下，我們甚至可能會執行快速診斷。 解決方案是由 Azure 工程師撰寫，並可解決最常見的問題。

### <a name="details"></a>詳細資料

接下來，我們會收集有關問題的其他詳細資料。 在此步驟中提供完整且詳細的資訊，可協助我們將您的支援要求路由至正確的代理程式。

可能的話，請告訴我們問題的開始時間，以及重現它的任何步驟。 您可以上傳檔案，例如記錄檔或診斷的輸出。

在取得有關問題的所有資訊之後，請選擇如何取得支援。 在 [**詳細資料**] 的 [**支援方法**] 區段中，選取影響的嚴重性。 提供您偏好的連絡人方法，這是聯繫您的好時機，以及您的支援語言。

接下來，請完成 [**連絡人資訊**] 區段，以瞭解如何與您聯絡。

### <a name="review--create"></a>檢閱 + 建立

在每個索引標籤上完成所有必要的資訊，然後選取 [**審查 + 建立**]。 請檢查您將傳送給支援的詳細資料。 回到任何索引標籤，視需要進行變更。 當您滿意支援要求完成時，請選取 [**建立**]。

支援代理程式會使用您所指定的方法來聯絡您。 如需初始回應時間的詳細資訊，請參閱[支援範圍和回應](https://azure.microsoft.com/support/plans/response/)性。

## <a name="all-support-requests"></a>所有支援要求

您可以前往 [說明 **+ 支援**] >  [**所有支援要求**]，來查看支援要求的詳細資料和狀態。

![所有支援要求](./media/how-to-create-azure-support-request/allrequestslower.png)

在此頁面上，您可以依**訂**用帳戶、**建立**日期（UTC）和**狀態**來篩選支援要求。 此外，您可以在此頁面上排序和搜尋支援要求。

選取支援要求以查看詳細資料，包括嚴重性以及支援代理程式回應所需的預期時間。

如果您想要變更要求的嚴重性，請選取 [**業務影響**]。 從要指派的嚴重性清單中選擇。

> [!NOTE]
> 最高嚴重性層級取決於您的支援計劃。 [深入了解支援計劃](https://azure.microsoft.com/support/plans)。
>
>
若要深入瞭解 Azure 中的自助支援選項，請觀看這段影片：

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>後續步驟

* [將您的意見反應和建議傳送給我們](https://feedback.azure.com/forums/266794-support-feedback)
* 在[Twitter](https://twitter.com/azuresupport)上與我們互動
* 在[MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure)中取得對等的協助
* 在[Azure 支援常見問題](https://azure.microsoft.com/support/faq)中深入瞭解
