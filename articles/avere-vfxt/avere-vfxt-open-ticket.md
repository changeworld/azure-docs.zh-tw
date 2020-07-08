---
title: 如何取得 Avere vFXT for Azure 的支援
description: 開立有關 Avere vFXT for Azure 支援票證的說明
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85847339"
---
# <a name="get-help-with-your-system"></a>取得有關系統的協助

如需 Avere vFXT for Azure 系統的協助，以下是取得支援的方法：

* **Avere vFXT 問題** - 使用 Azure 入口網站，為 Avere vFXT 開立支援票證，如[下方](#open-a-support-ticket-for-your-avere-vfxt)所述。
* **配額** - 如果您有與配額相關的問題，請[要求增加配額](#request-a-quota-increase)
* **文件和範例** - 如果您發現本文件或範例中有任何問題，請捲動到存在問題的頁面底部，並使用 [意見反應]**** 區段搜尋現有的問題，如果需要，提出一個新問題。

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>為 Avere vFXT 開立支援票證

如果您在部署或使用 Avere vFXT 時遇到問題，請透過 Azure 入口網站要求協助。

請遵循下列步驟，確定您的支援票證已使用您叢集中的資源標記。 標記票證可協助我們將其發送至正確的技術資源。

1. 在中 [https://portal.azure.com](https://portal.azure.com) ，選取 [**資源群組**]。 流覽至包含發生問題之 vFXT 叢集的資源群組，然後按一下其中一個 Avere 叢集虛擬機器。

    ![Azure 入口網站資源群組「概觀」面板的螢幕擷取畫面，其中圈出特定的 VM](media/avere-vfxt-ticket-vm.png)

1. 在 [VM] 頁面中，向下捲動至左側面板下方，然後按一下 [新增支援要求]****。

    ![上一個螢幕擷取畫面中 VM 的 Azure 入口網站 VM 頁面的螢幕擷取畫面。 左側功能表會滾動至底部，而 [新增支援要求] 則會被圈起來。](media/avere-vfxt-ticket-request.png)

1. 在支援要求的第一頁上，選擇問題類型，並確認已選取正確的訂用帳戶。

   在 [**服務**] 底下，按一下 [**所有服務**]，然後查看 [**儲存體**] 下的 [ **Avere vFXT**。

   新增簡短摘要，然後選取 [問題類型]。

    ![Azure 入口網站中新的支援要求畫面的螢幕擷取畫面。 [基本] 索引標籤已選取。 螢幕專案包括 [問題類型]、[訂閱]、[服務]、[摘要] 和 [問題類型]。](media/ticket-basics.png)

   按一下 [下一步] 以繼續。

1. 支援表單的第二個頁面包含根據摘要描述來修正問題的建議。 如果您仍然需要建立支援票證，請按一下底部的 [**下一步]** 按鈕。

   ![[新增支援要求] 畫面的螢幕擷取畫面，其中已選取 [解決方案] 索引標籤。 中間的文字欄位會有標題為「建議的解決方案」，並說明可能的補救方式。](media/ticket-solutions.png)

1. 在第三個頁面中，提供詳細資料-這包括叢集的相關資訊、問題發生的時間、嚴重性，以及如何與您聯絡。 填入資訊，然後按一下底部的 [**下一步]** 按鈕。

   ![[新增支援要求] 畫面的螢幕擷取畫面，其中已選取 [詳細資料] 索引標籤。 資訊欄位會組織成「問題詳細資料」、「支援方法」和「連絡人資訊」分類。](media/ticket-details.png)

1. 查看最後一頁的資訊，然後按一下 [**建立**]。 確認與票證編號將會傳送至您的電子郵件地址，且支援人員成員將會與您連絡。

## <a name="request-a-quota-increase"></a>要求增加配額

若要了解部署 Avere vFXT for Azure 所需的元件，請閱讀 [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。 您可以從 Azure 入口網站[要求增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。
