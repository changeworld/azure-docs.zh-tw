---
title: Azure 實驗室服務中的教室實驗室-常見問題 |Microsoft Docs
description: 本文提供有關 Azure 實驗室服務中教室實驗室的常見問題（FAQ）的解答。
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77443494"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure 實驗室服務中的教室實驗室-常見問題（FAQ）
取得一些關於 Azure 實驗室服務中的教室實驗室最常見問題的解答。 

## <a name="quotas"></a>配額

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>每位使用者或每週的配額，或實驗室的整個持續時間？ 
您為實驗室設定的配額，適用于實驗室的整個持續時間。 而且， [vm 的排程執行時間](how-to-create-schedules.md)不會計入分配給使用者的配額。 該配額用於學生在排程時間以外花費於 VM 上的時間。  如需有關配額的詳細資訊，請參閱[設定使用者配額](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>如果教授開啟學生 VM，會影響學生配額嗎？ 
否。 不是。 當教授開啟學生 VM 時，不會影響配置給學生的配額。 

## <a name="schedules"></a>排程

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>設定排程時，實驗室中的所有 Vm 都會自動啟動嗎？ 
否。 並非所有 Vm。 僅依排程指派給使用者的 Vm。 未指派給使用者的 Vm 不會自動啟動。 這是設計的。 

## <a name="lab-accounts"></a>實驗室帳戶

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>為什麼因為位址範圍無法使用而無法建立實驗室？ 
教室實驗室可以在您于 Azure 入口網站中建立實驗室帳戶時指定的 IP 位址範圍內建立實驗室 Vm。 提供位址範圍時，每個在為實驗室 Vm 分配 512 IP 位址之後建立的實驗室。 實驗室帳戶的位址範圍必須夠大，才能容納您想要在實驗室帳戶下建立的所有實驗室。 

例如，如果您的區塊為/19-10.0.0.0/19，則此位址範圍會容納8192個 IP 位址和16個實驗室（8192/512 = 16 個實驗室）。 在此情況下，建立17個實驗室時，實驗室建立作業會失敗。

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我應該在組織的防火牆設定上開啟哪些埠範圍，以透過 RDP/SSH 連接到實驗室虛擬機器？

埠為：49152–65535。 教室實驗室位於負載平衡器後方。 每個實驗室都有一個公用 IP 位址，而實驗室中的每部虛擬機器都有一個唯一的埠。 

您也可以在 [Azure 入口網站的 [實驗室] 首頁的 [**虛擬機器集**區] 索引標籤上，查看每部虛擬機器的私人 IP 位址。 如果您重新發佈實驗室，實驗室的公用 IP 位址不會變更，但是實驗室中每部虛擬機器的私人 IP 和埠號碼都可以變更。 您可以在下列文章中深入瞭解： [Azure 實驗室服務的防火牆設定](how-to-configure-firewall-settings.md)。

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我應該在組織的防火牆設定上開啟哪些公用 IP 位址範圍，以透過 RDP/SSH 連接到實驗室虛擬機器？
請參閱[AZURE IP 範圍和服務標籤-公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)，其可為 Azure 中的資料中心提供公用 IP 位址範圍。 您可以開啟實驗室帳戶所在區域的 IP 位址。

## <a name="virtual-machine-images"></a>虛擬機器映像

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>身為實驗室建立者，為什麼我在建立新實驗室時，無法在 [虛擬機器映射] 下拉式清單中啟用其他映射選項？

當系統管理員將您新增為實驗室帳戶的實驗室建立者時，您就會獲得建立實驗室的許可權。 但是，您沒有許可權可編輯實驗室帳戶內的任何設定，包括已啟用的虛擬機器映射清單。 若要啟用其他映射，請洽詢您的實驗室帳戶管理員以執行此動作，或要求系統管理員將您新增為實驗室帳戶的參與者角色。 [參與者] 角色會授與您在實驗室帳戶中編輯虛擬機器映射清單的許可權。

## <a name="users"></a>使用者

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>教室實驗室中可以有多少使用者？
您最多可以新增400個使用者到教室實驗室。 

## <a name="blog-post"></a>Blog 文章
訂閱[Azure 實驗室服務的 blog](https://azure.microsoft.com/blog/tag/azure-lab-services/)。

## <a name="update-notifications"></a>更新通知
訂閱[實驗室服務更新](https://azure.microsoft.com/updates/?product=lab-services)，以隨時掌握實驗室服務的新功能。

## <a name="general"></a>一般
### <a name="what-if-my-question-isnt-answered-here"></a>如果這裡沒有解答我的問題該怎麼辦？
如果此處未列出您的問題，請讓我們知道，讓我們可以協助您找到答案。

- 將問題張貼在此常見問題集尾端。 
- 若要觸及更多物件，請在[Azure 實驗室服務上張貼問題-Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-lab-services)。 
- 對於功能要求，請將您的要求和想法提交到[Azure 實驗室服務-User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)。

