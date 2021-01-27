---
title: 設定 Azure 資訊安全中心警示的電子郵件通知
description: 了解如何針對安全性警示，微調 Azure 資訊安全中心送出的電子郵件類型。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2020
ms.author: memildin
ms.openlocfilehash: 72ded01b141aafb7fd3e4d761882a10eaf0c4b33
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920404"
---
# <a name="configure-email-notifications-for-security-alerts"></a>設定安全性警示的電子郵件通知 

安全性警示需要觸及貴組織中的適當人員。 根據預設，每當針對訂用帳戶觸發高嚴重性警示時，資訊安全中心就會傳送電子郵件給訂用帳戶擁有者。 本頁面說明如何自訂這些通知。

若要針對通知電子郵件定義自己的喜好設定，Azure 資訊安全中心的 [電子郵件通知] 設定頁面可讓您選擇：

- **誰應該收到通知** - 可以將電子郵件傳送給首選的人員，或是具有訂用帳戶指定 Azure 角色的任何人。 
- **他們應該收到什麼通知** - 修改資訊安全中心應送出通知的嚴重性層級。

為避免警示疲勞，資訊安全中心會限制傳出郵件的數量。 針對每個訂用帳戶，資訊安全中心會傳送：

- 最多每 **6 小時** 一封 (每天 4 封) 有關 **高嚴重性** 警示的電子郵件
- 最多每 **12 小時** 一封 (每天 2 封) 有關 **中嚴重性** 警示的電子郵件
- 最多每 **24 小時** 一封有關 **低嚴重性** 警示的電子郵件

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="設定要接收安全性警示相關電子郵件的連絡人詳細資料。" :::
 
## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|公開上市 (GA) |
|定價：|免費|
|必要的角色和權限：|**安全性系統管理員**<br>**訂用帳戶擁有者** |
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>自訂安全性警示電子郵件通知<a name="email"></a>

您可以將電子郵件通知傳送給個人或具有特定 Azure 角色的所有使用者。

1. 從資訊安全中心的 [定價與設定] 區域，選取相關的訂用帳戶，然後選取 [電子郵件通知]。

1. 使用下列其中一個或兩個選項，為您的通知定義收件者：

    - 從下拉式清單中，選取可用的角色。
    - 輸入以逗號分隔的特定電子郵件地址。 您可以輸入的電子郵件地址數沒有限制。

1. 若要將安全性連絡人資訊套用至您的訂用帳戶，請選取 [儲存]。


## <a name="see-also"></a>另請參閱
如要深入了解安全性警示，請參閱下列頁面：

- [安全性警示 - 參考指南](alerts-reference.md) -- 了解可能會在 Azure 資訊安全中心威脅防護模組中看到的安全性警示
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示
- [工作流程自動化](workflow-automation.md) -- 使用自訂通知邏輯自動回應警示