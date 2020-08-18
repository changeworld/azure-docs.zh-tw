---
title: 在 Azure 資訊安全中心提供安全性連絡人詳細資料 | Microsoft Docs
description: 本文件說明如何在 Azure 資訊安全中心提供安全性連絡人詳細資料。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: dda61b81ee2c357ddac29701832fe4780ea06859
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516294"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>設定安全性警示的電子郵件通知 

為確保您組織中的適當人員會收到有關您環境中安全性警示的通知，請在 [ **電子郵件通知** 設定] 頁面中輸入其電子郵件地址。

設定您的通知時，您可以設定要傳送給特定個人的電子郵件，或將訂用帳戶的特定 Azure 角色傳送給任何人。 

為了避免警示疲勞，「安全性中心」會限制傳出郵件的數量。 針對每個訂用帳戶，「安全性中心」會傳送：

- **高嚴重性**警示每天最多**四**封電子郵件
- 針對**中嚴重性**警示，每天最多可有**兩**封電子郵件
- 針對**低嚴重性**警示，每天最多可有**一**封電子郵件


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="設定連絡人的詳細資料，將會收到有關安全性警示的電子郵件。" :::

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式推出|
|定價：|免費層|
|必要的角色和許可權：|**安全性系統管理員**<br>**訂用帳戶擁有者** |
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端<br>![Yes](./media/icons/yes-icon.png) US Gov (部分) <br>![No](./media/icons/no-icon.png) 中國 Gov，其他 Gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>設定警示的電子郵件通知 <a name="email"></a>

您可以將電子郵件通知傳送給個人或具有特定 Azure 角色的所有使用者。

1. 從安全性中心的 **定價 & 設定** ] 區域、相關的訂用帳戶，然後選取 [ **電子郵件通知**]。

1. 定義通知的收件者：

    - 從下拉式清單中，選取可用的角色。
    - 和/或輸入以逗號分隔的特定電子郵件地址。 您可以輸入的電子郵件地址數目沒有任何限制。

1. 若要將安全性連絡人資訊套用至您的訂用帳戶，請選取 [ **儲存**]。


## <a name="see-also"></a>請參閱
若要深入瞭解安全性警示，請參閱下列各項：

* [安全性警示-參考指南](alerts-reference.md) --瞭解您可能會在 Azure 資訊安全中心的威脅防護課程模組中看到的安全性警示
* [在 Azure 資訊安全中心中管理及回應安全性警示](security-center-managing-and-responding-alerts.md) --瞭解如何管理和回應安全性警示
* [工作流程自動化](workflow-automation.md) --使用自訂通知邏輯自動回應警示