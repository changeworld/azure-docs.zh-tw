---
title: 適用于虛擬機器的 Azure Automanage 常見問題
description: 適用于虛擬機器的 Azure Automanage 常見問題的解答。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 89ad5c2b973a74bc9fa0f6ca2f59f92a26b666e7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891453"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>適用于 Vm 的 Azure Automanage 的常見問題

本文提供一些關於 [Azure Automanage 的虛擬機器](automanage-virtual-machines.md)最常見問題的解答。

若本文中未提及您的 Azure 問題，請前往 [MSDN 及 Stack Overflow 上的 Azure 論壇](https://azure.microsoft.com/support/forums/)。 您可以在這些論壇中張貼您的問題，或將問題貼到 [Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 您也可以提交 Azure 支援要求。 若要提交支援要求，請在 [ [Azure 支援] 頁面](https://azure.microsoft.com/support/options/)上選取 [ **取得支援** ]。


## <a name="azure-automanage-for-virtual-machines"></a>適用于虛擬機器的 Azure Automanage

**啟用 Azure Automanage 所需的必要條件有哪些？**

以下是啟用 Azure Automanage 的必要條件：
- 僅限 Windows Server Vm
- Vm 必須正在執行
- Vm 必須位於支援的區域
- 使用者必須有正確的許可權
- 僅限非擴展集 Vm
- Automanage 目前不支援沙箱訂閱

**啟用 Automanage 所需的 Azure RBAC 許可權為何？**

如果您要在具有現有 Automanage 帳戶的 VM 上啟用 Automanage，您需要有 VM 所在資源群組的「參與者」角色。

如果您在啟用時使用新的 Automanage 帳戶，您必須擁有「擁有者」角色，或具有訂用帳戶的「參與者」和「使用者存取系統管理員」角色。


**支援哪些區域？**

支援下欄區域中的 Vm：西歐、美國東部、美國西部2、加拿大中部美國中西部。


**Azure Automanage 會自動化哪些功能？**

Automanage 會在 VM 的整個生命週期中註冊、設定及監視 [此處](virtual-machines-best-practices.md)所列的服務。

**Azure Automanage 是否適用于已啟用 Azure Arc 的 Vm？**

Automanage 目前不支援已啟用 Arc 的 Vm。

**我可以自訂 Azure Automanage 上的設定嗎？**

客戶可以透過設定喜好設定來自訂特定服務的設定，例如 Azure 備份保留。 如需可變更之設定的完整清單，請參閱 [此處](virtual-machines-best-practices.md)的檔。


**Azure Automanage 是否適用于 Linux 和 Windows Vm？**

Automanage 目前支援 Windows Server Azure Vm。


**我可以選擇性地只在一組 Vm 上套用 Automanage 嗎？**

您可以在選取的新的和現有的 Vm 上，透過 click 和 point 簡單性來啟用 Automanage。 Automanage 也可以在任何時間停用。


**Azure Automanage 是否支援虛擬機器擴展集中的 Vm？**

否，Azure Automanage 目前不支援虛擬機器擴展集中的 Vm。


**Azure Automanage 需要多少費用？**

Azure Automanage 免費提供公開預覽。 附加的 Azure 資源（例如 Azure 備份）將會產生費用。


**我可以透過 Azure 原則套用 Automanage 嗎？**

是的，我們有內建原則會自動將 Automanage 套用至定義範圍內的所有 Vm。 您也會指定設定設定檔 (DevTest 或生產) 以及 Automanage 帳戶。 [在這裡](virtual-machines-policy-enable.md)深入瞭解如何透過 Azure 原則啟用 Automanage。


**什麼是 Automanage 帳戶？**

Automanage 帳戶是 MSI (受控服務識別) ，可提供安全性內容或執行自動化作業所使用的身分識別。


**啟用 Automanage 時，除了選取的 VM () 之外，是否會影響任何額外的 Vm？**

如果您的 VM 已連結至現有的 Log Analytics 工作區，我們將會重複使用該工作區來套用這些解決方案：變更追蹤、清查和更新管理。 所有連線到該工作區的 Vm 都會啟用這些解決方案。


**我可以變更 VM 的設定檔嗎？**

目前，您必須停用該 VM 的 Automanage，然後使用所需的設定設定檔和喜好設定來重新啟用 Automanage。


**如果已針對服務設定我的 VM （例如更新管理），將 Automanage 重新設定它嗎？**
否，Automanage 不會重新設定。 我們將開始監視與該服務相關聯的資源，以進行漂移。


**為什麼我的 VM 在 Automanage 入口網站中的狀態失敗？**

如果您看到狀態為 [ *失敗* ]，您可以透過您的 VM 所在的資源群組進行部署的疑難排解。 移至 [ **資源群組** ]，選取您的資源群組，按一下 [ **部署** ]，並在該處查看 *失敗* 的狀態以及錯誤詳細資料。

**如何取得 Automanage 的疑難排解支援？**

您可以提出 [技術支援案例的票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 針對 [ **服務** ] 選項，在 [ *監視和管理* ] 區段底下搜尋並選取 [ *Automanage* ]。


## <a name="next-steps"></a>後續步驟

請嘗試在 Azure 入口網站中啟用虛擬機器的 Automanage。

> [!div class="nextstepaction"]
> [針對 Azure 入口網站中的虛擬機器啟用 Automanage](quick-create-virtual-machines-portal.md)