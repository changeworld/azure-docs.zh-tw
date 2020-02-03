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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 551167cda28a2bb6007e66c1b4b458a0a7b2e396
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718022"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure 實驗室服務中的教室實驗室-常見問題（FAQ）
取得一些關於 Azure 實驗室服務中的教室實驗室最常見問題的解答。 

## <a name="quotas"></a>配額

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>每位使用者或每週的配額，或實驗室的整個持續時間？ 
您為實驗室設定的配額，適用于實驗室的整個持續時間。 而且， [vm 的排程執行時間](how-to-create-schedules.md)不會計入分配給使用者的配額。 該配額用於學生在排程時間以外花費於 VM 上的時間。  如需有關配額的詳細資訊，請參閱[設定使用者配額](how-to-configure-student-usage.md#set-quotas-for-users)。

## <a name="schedules"></a>排程

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>設定排程時，實驗室中的所有 Vm 都會自動啟動嗎？ 
否。 並非所有 Vm。 僅依排程指派給使用者的 Vm。 未指派給使用者的 Vm 不會自動啟動。 這是設計的。 

## <a name="lab-accounts"></a>實驗室帳戶

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>為什麼因為位址範圍無法使用而無法建立實驗室？ 
教室實驗室可以在您于 Azure 入口網站中建立實驗室帳戶時指定的 IP 位址範圍內建立實驗室 Vm。 提供位址範圍時，每個在為實驗室 Vm 分配 512 IP 位址之後建立的實驗室。 實驗室帳戶的位址範圍必須夠大，才能容納您想要在實驗室帳戶下建立的所有實驗室。 

例如，如果您的區塊為/19-10.0.0.0/19，則此位址範圍會容納8192個 IP 位址和16個實驗室（8192/512 = 16 個實驗室）。 在此情況下，建立17個實驗室時，實驗室建立作業會失敗。

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我應該在組織的防火牆設定上開啟哪些埠範圍，以透過 RDP/SSH 連接到實驗室虛擬機器？

埠為：49152–65535。 教室實驗室位於負載平衡器後方，因此實驗室中的所有虛擬機器都有單一 IP 位址，而實驗室中的每部虛擬機器都有唯一的埠。 每次重新發佈實驗室時，埠號碼和公用 IP 位址都可以變更。

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我應該在組織的防火牆設定上開啟哪些公用 IP 位址範圍，以透過 RDP/SSH 連接到實驗室虛擬機器？
請參閱[AZURE IP 範圍和服務標籤-公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)，其可為 Azure 中的資料中心提供公用 IP 位址範圍。 您可以開啟實驗室帳戶所在區域的 IP 位址。

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

