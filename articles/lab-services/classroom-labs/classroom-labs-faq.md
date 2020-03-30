---
title: Azure 實驗室服務中的課堂實驗室 » 常見問題解答 |微軟文檔
description: 本文提供了有關 Azure 實驗室服務中教室實驗室的常見問題 （FAQ） 的解答。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443494"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure 實驗室服務中的課堂實驗室 • 常見問題 （FAQ）
獲取有關 Azure 實驗室服務中教室實驗室的一些最常見問題的解答。 

## <a name="quotas"></a>配額

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>每個使用者、每週或整個實驗室的持續時間的配額？ 
為實驗室設置的配額適用于整個實驗的整個期間每個學生。 而且[，VM 的計畫執行時間](how-to-create-schedules.md)不計入分配給使用者的配額。 該配額用於學生在排程時間以外花費於 VM 上的時間。  有關配額的詳細資訊，請參閱[為使用者設置配額](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>如果教授打開學生 VM，這會影響學生配額嗎？ 
否。 不是的。 當教授打開學生 VM 時，它不會影響分配給學生的配額。 

## <a name="schedules"></a>排程

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>設置計畫時，實驗室中的所有 VM 是否自動啟動？ 
否。 不是所有的 VM。 僅按計劃分配給使用者的 VM。 未分配給使用者的 VM 不會自動啟動。 是設計設計的 

## <a name="lab-accounts"></a>實驗室帳戶

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>為什麼由於位址範圍不可用而無法創建實驗室？ 
課堂實驗室可以在您在 Azure 門戶中創建實驗室帳戶時指定的 IP 位址範圍內創建實驗室 VM。 提供位址範圍時，為實驗室 VM 分配了 512 個 IP 位址後創建的每個實驗室。 實驗室帳戶的位址範圍必須足夠大，以容納您打算在實驗室帳戶下創建的所有實驗室。 

例如，如果您有一個塊 /19 - 10.0.0.0/19，此位址範圍可容納 8192 個 IP 位址和 16 個實驗室（8192/512 = 16 個實驗室）。 在這種情況下，實驗室創建在 17 日實驗室創建時失敗。

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我應該在組織的防火牆設置上打開哪些埠範圍，以便通過 RDP/SSH 連接到實驗室虛擬機器？

埠為：49152*65535。 教室實驗室在負載等化器後面。 每個實驗室都有一個公共 IP 位址，實驗室中的每個虛擬機器都有一個唯一的埠。 

您還可以在 Azure 門戶中實驗室的主頁的**虛擬機器池**選項卡上查看每個虛擬機器的私人 IP 位址。 如果重新發佈實驗室，實驗室的公共 IP 位址將不會更改，但實驗室中每個虛擬機器的專用 IP 和埠號可能會更改。 您可以在本文中瞭解詳細資訊[：Azure 實驗室服務的防火牆設置](how-to-configure-firewall-settings.md)。

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我應該在組織的防火牆設置上打開哪些公共 IP 位址範圍，以便通過 RDP/SSH 連接到實驗室虛擬機器？
請參閱[Azure IP 範圍和服務標記 – 公共雲](https://www.microsoft.com/download/details.aspx?id=56519)，它為 Azure 中的資料中心提供公共 IP 位址範圍。 您可以打開實驗室帳戶所在的區域的 IP 位址。

## <a name="virtual-machine-images"></a>虛擬機器映像

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>作為實驗室建立者，為什麼在創建新實驗室時，我不能在虛擬機器映射下拉清單中啟用其他映射選項？

當管理員將您作為實驗室建立者添加到實驗室帳戶時，您被授予創建實驗室的許可權。 但是，您無權編輯實驗室帳戶中的任何設置，包括啟用的虛擬機器映射的清單。 要啟用其他映射，請與實驗室帳戶管理員聯繫，以便為您執行此操作，或要求管理員將您作為參與者角色添加到實驗室帳戶。 "參與者"角色將授予您在實驗室帳戶中編輯虛擬機器映射清單的許可權。

## <a name="users"></a>使用者

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>有多少使用者可以在教室實驗室？
您最多可以將 400 個使用者添加到教室實驗室。 

## <a name="blog-post"></a>部落格文章
訂閱[Azure 實驗室服務博客](https://azure.microsoft.com/blog/tag/azure-lab-services/)。

## <a name="update-notifications"></a>更新通知
訂閱[實驗室服務更新](https://azure.microsoft.com/updates/?product=lab-services)，隨時瞭解實驗室服務中的新功能。

## <a name="general"></a>一般
### <a name="what-if-my-question-isnt-answered-here"></a>如果這裡沒有解答我的問題該怎麼辦？
如果您的問題未在此處列出，請告訴我們，以便我們可以説明您找到答案。

- 將問題張貼在此常見問題集尾端。 
- 要覆蓋更廣泛的受眾，在[Azure 實驗室服務 - 堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-lab-services)上發佈問題。 
- 對於功能請求，請將請求和想法提交給[Azure 實驗室服務 - 使用者語音](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)。

