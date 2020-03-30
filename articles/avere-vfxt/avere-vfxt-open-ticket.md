---
title: 如何取得 Avere vFXT for Azure 的支援
description: 開立有關 Avere vFXT for Azure 支援票證的說明
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252554"
---
# <a name="get-help-with-your-system"></a>取得有關系統的協助

有關 Azure 系統的 Avere vFXT 的説明，以下是獲得支援的方法：

* **Avere vFXT 問題** - 使用 Azure 入口網站，為 Avere vFXT 開立支援票證，如[下方](#open-a-support-ticket-for-your-avere-vfxt)所述。
* **配額** - 如果您有與配額相關的問題，請[要求增加配額](#request-a-quota-increase)
* **文件和範例** - 如果您發現本文件或範例中有任何問題，請捲動到存在問題的頁面底部，並使用 [意見反應]**** 區段搜尋現有的問題，如果需要，提出一個新問題。

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>為 Avere vFXT 開立支援票證

如果您在部署或使用 Avere vFXT 時遇到問題，請透過 Azure 入口網站要求協助。

請遵循下列步驟，確定您的支援票證已使用您叢集中的資源標記。 標記票證可協助我們將其發送至正確的技術資源。

1. 從[https://portal.azure.com](https://portal.azure.com)中選擇**資源組**。 流覽到出現問題的 vFXT 群集的資源組，然後按一下其中一個 Avere 群集虛擬機器。

    ![Azure 入口網站資源群組「概觀」面板的螢幕擷取畫面，其中圈出特定的 VM](media/avere-vfxt-ticket-vm.png)

1. 在 [VM] 頁面中，向下捲動至左側面板下方，然後按一下 [新增支援要求]****。

    ![上一個螢幕擷取畫面中 VM 的 Azure 入口網站 VM 頁面的螢幕擷取畫面。 左側功能表滾動到底部，"新支援請求"圈。](media/avere-vfxt-ticket-request.png)

1. 在支援請求的第一頁上，選擇問題類型並確保選擇了正確的訂閱。

   在 **"服務**"下，按一下 **"所有服務**"，並在 **"存儲"** 下查看以選擇**Avere vFXT**。

   添加簡短摘要，然後選擇問題類型。

    ![Azure 門戶中新支援請求螢幕的螢幕截圖。 選擇了"基本"選項卡。 螢幕專案包括問題類型、訂閱、服務、摘要和問題類型。](media/ticket-basics.png)

   按 **[下一步]**，繼續進行。

1. 支援表單的第二頁包含基於摘要描述修復問題的建議。 如果仍需要創建支援票證，請按一下底部的 **"下一步**"按鈕。

   ![選中"解決方案"選項卡的新支援請求螢幕的螢幕截圖。 中間的文字欄位標題為"建議的解決方案"，並解釋了可能的補救措施。](media/ticket-solutions.png)

1. 在第三頁中，提供詳細資訊 - 這包括有關群集、出現問題的時間、嚴重性以及如何與您聯繫的資訊。 填寫資訊，然後按一下底部的 **"下一步**"按鈕。

   ![選中"詳細資訊"選項卡的新支援請求螢幕的螢幕截圖。 資訊欄位分為"問題詳細資訊"、"支援方法"和"聯繫資訊"類別。](media/ticket-details.png)

1. 查看最終頁面上的資訊，然後按一下"**創建**"。 確認與票證編號將會傳送至您的電子郵件地址，且支援人員成員將會與您連絡。

## <a name="request-a-quota-increase"></a>要求增加配額

若要了解部署 Avere vFXT for Azure 所需的元件，請閱讀 [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。 您可以從 Azure 入口網站[要求增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。
