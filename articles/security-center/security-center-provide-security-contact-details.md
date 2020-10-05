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
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: aa35d1325e339af515c8bfc052d1af524b464e09
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446014"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>設定安全性警示的電子郵件通知 

為了確保您組織中的適當人員會收到環境中安全性警示的相關通知，請在 [電子郵件通知] 設定頁面中輸入他們的電子郵件地址。

設定通知時，您可以設定將電子郵件傳送給特定個人，或傳送給訂用帳戶上具有特定 Azure 角色的任何人。 

為避免警示疲勞，資訊安全中心會限制傳出郵件的數量。 針對每個訂用帳戶，資訊安全中心會傳送：

- 每天最多**四封**有關**高嚴重性**警示的電子郵件
- 每天最多**兩封**有關**中嚴重性**警示的電子郵件
- 每天最多**一封**有關**低嚴重性**警示的電子郵件


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="設定要接收安全性警示相關電子郵件的連絡人詳細資料。" :::

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|免費|
|必要的角色和權限：|**安全性系統管理員**<br>**訂用帳戶擁有者** |
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov (部分)<br>![否](./media/icons/no-icon.png) 中國 Gov、其他 Gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>設定警示的電子郵件通知<a name="email"></a>

您可以將電子郵件通知傳送給個人或具有特定 Azure 角色的所有使用者。

1. 從資訊安全中心的 [定價與設定] 區域，選取相關的訂用帳戶，然後選取 [電子郵件通知]。

1. 定義通知的收件者：

    - 從下拉式清單中，選取可用的角色。
    - 以及/或輸入以逗號分隔的特定電子郵件地址。 您可以輸入的電子郵件地址數沒有限制。

1. 若要將安全性連絡人資訊套用至您的訂用帳戶，請選取 [儲存]。


## <a name="see-also"></a>另請參閱
如要深入了解安全性警示，請參閱下列主題：

* [安全性警示 - 參考指南](alerts-reference.md) - 了解可能會在 Azure 資訊安全中心威脅防護模組中看到的安全性警示
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示
* [工作流程自動化](workflow-automation.md) -- 使用自訂通知邏輯自動回應警示