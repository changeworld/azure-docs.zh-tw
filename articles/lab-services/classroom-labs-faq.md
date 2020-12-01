---
title: Azure 實驗室服務中的實驗室-常見問題 |Microsoft Docs
description: 本文針對 Azure 實驗室服務中的實驗室，提供常見問題的解答 (常見問題) 。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 757af8f30e9a71a3889d9f625c87a002af2e1302
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437179"
---
# <a name="labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure 實驗室服務中的實驗室-常見問題 (常見問題) 
針對 Azure 實驗室服務中的實驗室，取得一些最常見問題的解答。 

## <a name="quotas"></a>配額

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>配額的設定基準是每位使用者、每週，還是每個完整的實驗室持續時間？ 
您為實驗室設定的配額適用於完整實驗室持續時間內的每位學生。 而且，[VM 的排程執行時間](how-to-create-schedules.md)不會計入分配給使用者的配額中。 該配額用於學生在排程時間以外花費於 VM 上的時間。  如需配額的詳細資訊，請參閱[為使用者設定配額](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>如果授課者開啟學生 VM，是否會影響學生配額？ 
否。 不會。 當授課者開啟學生 VM 時，並不會影響配置給學生的配額。 

## <a name="schedules"></a>排程

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>設定了排程時，實驗室中的所有 VM 是否都會自動啟動？ 
否。 並非所有 VM。 只有依排程指派給使用者的 VM。 未指派給使用者的 VM 則不會自動啟動。 這是經過設計的。 

## <a name="lab-accounts"></a>實驗室帳戶

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>為何會因為位址範圍無法使用而無法建立實驗室？ 

實驗室可以在您于 Azure 入口網站中建立實驗室帳戶時指定的 IP 位址範圍內建立實驗室 Vm。 提供了位址範圍時，之後所建立的每個實驗室會為實驗室 VM 配置 512 個 IP 位址。 實驗室帳戶的位址範圍必須夠大，才能容納您想要在實驗室帳戶下建立的所有實驗室。 

例如，如果您的區塊為 /19 - 10.0.0.0/19，則此位址範圍可容納 8192 個 IP 位址和 16 個實驗室 (8192/512 = 16 個實驗室)。 在此情況下，在建立第 17 個實驗室時，建立作業會失敗。

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我應該在組織的防火牆設定上開啟哪些連接埠範圍，才能透過 RDP/SSH 連線到實驗室虛擬機器？

這些連接埠如下：49152–65535。 實驗室位於負載平衡器後方。 每個實驗室都有一個公用 IP 位址，而實驗室中的每部虛擬機器都有一個唯一的連接埠。 

您也可以在 Azure 入口網站的實驗室首頁，於 [虛擬機器集區] 索引標籤上查看每部虛擬機器的私人 IP 位址。 如果您重新發佈實驗室，實驗室的公用 IP 位址不會變更，但是實驗室中每部虛擬機器的私人 IP 和連接埠號碼都會變更。 您可以在下列文章中深入了解：[Azure 實驗室服務的防火牆設定](how-to-configure-firewall-settings.md)。

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我應該在組織的防火牆設定上開啟哪個公用 IP 位址範圍，才能透過 RDP/SSH 連線到實驗室虛擬機器？
請參閱 [Azure IP 範圍和服務標籤 - 公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)，其中提供了 Azure 資料中心的公用 IP 位址範圍。 您可以為實驗室帳戶所在的區域開啟 IP 位址。

## <a name="virtual-machine-images"></a>虛擬機器映像

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>身為實驗室建立者，為何我無法在建立新實驗室時，於 [虛擬機器映像] 下拉式清單中啟用其他映像選項？

當系統管理員將您新增為實驗室帳戶的實驗室建立者時，您便獲得建立實驗室的權限。 但是，您沒有權限可編輯實驗室帳戶內的任何設定，包括已啟用的虛擬機器映像清單。 若要啟用其他映像，請連絡實驗室帳戶管理員來為您進行，或要求系統管理員將您新增為實驗室帳戶的參與者角色。 參與者角色會授與您在實驗室帳戶中編輯虛擬機器映像清單的權限。

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>是否可以將其他磁碟連結至虛擬機器？
否。 您無法將額外的磁碟連結到教室實驗室中的 VM。 

## <a name="users"></a>使用者

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>教室實驗室可以容納多少名使用者？
您最多可以在教室實驗室中新增 400 名使用者。 

## <a name="blog-post"></a>部落格文章
請訂閱 [Azure 實驗室服務部落格](https://aka.ms/azlabs-blog)。

## <a name="update-notifications"></a>更新通知
請訂閱[實驗室服務更新](https://azure.microsoft.com/updates/?product=lab-services)，以隨時掌握實驗室服務的新功能。

## <a name="general"></a>一般
### <a name="what-if-my-question-isnt-answered-here"></a>如果這裡沒有解答我的問題該怎麼辦？
如果這裡未列出您的問題，請告訴我們，好讓我們能協助您找到答案。

- 將問題張貼在此常見問題集尾端。 
- 若要接觸更廣泛的對象，請將問題張貼到 [Azure 實驗室服務 - 技術社群論壇](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices)。 
- 若要提出功能要求，請將您的要求和想法提交到 [Azure 實驗室服務 - User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)。

