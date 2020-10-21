---
title: 如何取得 Avere vFXT for Azure 的支援
description: 瞭解如何透過 Azure 入口網站建立支援票證，以解決部署或使用 Avere vFXT for Azure 時可能發生的問題。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 8159d83624cdd474d91030d5376a3db447beffe1
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342343"
---
# <a name="get-help-with-your-system"></a>取得有關系統的協助

如需 Avere vFXT for Azure 系統的協助，以下是取得支援的方法：

* **Avere vFXT 問題** - 使用 Azure 入口網站，為 Avere vFXT 開立支援票證，如[下方](#open-a-support-ticket-for-your-avere-vfxt)所述。
* **配額** - 如果您有與配額相關的問題，請[要求增加配額](#request-a-quota-increase)
* **文件和範例** - 如果您發現本文件或範例中有任何問題，請捲動到存在問題的頁面底部，並使用 [意見反應]**** 區段搜尋現有的問題，如果需要，提出一個新問題。

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>為 Avere vFXT 開立支援票證

如果您在部署或使用 Avere vFXT 時遇到問題，請透過 Azure 入口網站要求協助。

請遵循下列步驟，確定您的支援票證已使用您叢集中的資源標記。 標記票證可協助我們將其發送至正確的技術資源。

1. 從 [https://portal.azure.com](https://portal.azure.com) 選取 **資源群組**。 流覽至包含發生問題之 vFXT 叢集的資源群組，然後按一下其中一個 Avere 叢集虛擬機器。

    ![Azure 入口網站資源群組「概觀」面板的螢幕擷取畫面，其中圈出特定的 VM](media/avere-vfxt-ticket-vm.png)

1. 在 [VM] 頁面中，向下捲動至左側面板下方，然後按一下 [新增支援要求]****。

    ![上一個螢幕擷取畫面中 VM 的 Azure 入口網站 VM 頁面的螢幕擷取畫面。 左側功能表會向下滾動，而 [新增支援要求] 則會被圈下。](media/avere-vfxt-ticket-request.png)

1. 在支援要求的第一頁上，選擇問題類型，並確認已選取正確的訂用帳戶。

   在 [ **服務**] 下，按一下 [ **所有服務** ]，然後查看 [ **儲存體** ] 以選擇 **Avere vFXT**。

   新增簡短摘要，然後選取 [問題類型]。

    ![Azure 入口網站中新的支援要求畫面的螢幕擷取畫面。 已選取 [基本] 索引標籤。 螢幕專案包括問題類型、訂用帳戶、服務、摘要和問題類型。](media/ticket-basics.png)

   按一下 [下一步] 以繼續。

1. 支援表單的第二頁包含根據您的摘要描述來修正問題的建議。 如果您仍然需要建立支援票證，請按一下底部的 [ **下一步]** 按鈕。

   ![[新增支援要求] 畫面的螢幕擷取畫面，其中已選取 [方案] 索引標籤。 中間的文字欄位具有「建議的方案」標題，並說明可能的補救方式。](media/ticket-solutions.png)

1. 在第三個頁面中提供詳細資料，包括叢集的相關資訊、發生問題的時間、嚴重性，以及如何與您聯繫。 填寫資訊，然後按一下底部的 [ **下一步]** 按鈕。

   ![[新增支援要求] 畫面的螢幕擷取畫面，其中已選取 [詳細資料] 索引標籤。 資訊欄位會組織成類別的 [問題詳細資料]、[支援方法] 和 [連絡人資訊]。](media/ticket-details.png)

1. 檢查最後一個頁面上的資訊，然後按一下 [ **建立**]。 確認與票證編號將會傳送至您的電子郵件地址，且支援人員成員將會與您連絡。

## <a name="request-a-quota-increase"></a>要求增加配額

若要了解部署 Avere vFXT for Azure 所需的元件，請閱讀 [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。 您可以從 Azure 入口網站[要求增加配額](../azure-portal/supportability/resource-manager-core-quotas-request.md)。