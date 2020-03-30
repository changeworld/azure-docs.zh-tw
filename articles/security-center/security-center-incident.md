---
title: 在 Azure 安全中心管理安全事件 |微軟文檔
description: 本文檔可説明您使用 Azure 安全中心來管理安全事件。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415674"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>管理 Azure 安全中心中的安全事件

即使是最熟練的安全分析師，對安全警報進行會審和調查也非常耗時，對許多人來說，甚至很難知道從哪裡開始。 透過使用[分析](security-center-detection-capabilities.md)來連結不同[安全性警示](security-center-managing-and-responding-alerts.md)之間的資訊，資訊安全中心可以提供關於攻擊活動和所有相關警示的單一檢視，讓您快速了解攻擊者所採取的動作以及受到影響的資源。

本主題介紹安全中心中的事件，以及如何使用修復其警報。

## <a name="what-is-a-security-incident"></a>什麼是安全性事件？

在資訊安全中心內，安全性事件是符合[攻擊鏈](alerts-reference.md#intentions)模式之資源的所有警示彙總。 事件將顯示在["安全警報"](security-center-managing-and-responding-alerts.md)清單中。 按一下事件以查看相關警報，從而使您能夠獲取有關每個事件的詳細資訊。

## <a name="managing-security-incidents"></a>管理安全性事件

1. 在"安全中心"儀表板上，按一下 **"安全警報"** 磁貼。 事件和警報已列出。 請注意，安全性事件描述具有不同於其他警示的圖示。

    ![查看安全事件](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 要查看詳細資訊，請按一下事件。 **檢測到的安全事件**邊欄選項卡顯示更多詳細資訊。 "**常規資訊**"部分可以深入瞭解觸發安全警報的原因。 它顯示目標資源、源 IP 位址（如果適用）、警報仍處於活動狀態以及有關如何修復的建議等資訊。  

    ![回應 Azure 安全中心中的安全事件](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. 要獲取有關每個警報的詳細資訊，請按一下警報。 資訊安全中心會根據安全性警示，建議您不同的補救方法。

   > [!NOTE]
   > 相同的警報可以作為事件的一部分存在，也可以作為獨立警報可見。

    ![警示詳細資料](./media/security-center-incident/security-center-incident-alert.png)

1. 按照為每個警報提供的修正步驟操作。


## <a name="see-also"></a>另請參閱
在本文件中，您已了解如何使用資訊安全中心的安全性事件功能。 如需相關資訊，請參閱下列各項：

* [Azure 安全中心中的威脅保護](threat-protection.md)
* [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
* [管理安全性警示](security-center-managing-and-responding-alerts.md)