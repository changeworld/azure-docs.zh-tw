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
ms.openlocfilehash: 661d3845365778f7ef23cdd05b81b98c3bf84259
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519268"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>設定安全性警示的電子郵件通知 

為了確保您組織中的適當人員會收到有關您環境中安全性警示的通知，請在 [**電子郵件通知**設定] 頁面中輸入他們的電子郵件地址。

設定通知時，您可以設定要傳送給特定個人的電子郵件，或針對訂用帳戶具有特定 RBAC 角色的任何人。 

為避免警示疲勞，資訊安全中心限制傳出郵件的數量。 針對每個訂用帳戶，資訊安全中心傳送：

- 針對**高嚴重性**警示，每天最多**四**個電子郵件
- 針對**中嚴重性**警示，每天最多可有**兩**封電子郵件
- 針對**低嚴重性**警示，每天最多可有**一**封電子郵件

## <a name="availability"></a>可用性

- 發行階段：**正式推出**
- 必要角色：**安全性系統管理員**或訂用帳戶**擁有**者 
- 雲端：✔商用雲端✔ US Gov （部分）✘國家/地區/主權（中國 Gov，其他 Gov）


## <a name="set-up-email-notifications-for-alerts"></a>設定警示的電子郵件通知<a name="email"></a>

您可以將電子郵件通知傳送給個人或具有特定 RBAC 角色的所有使用者。

1. 從資訊安全中心的**定價 & 設定**] 區域、相關的訂用帳戶，然後選取 [**電子郵件通知**]。

1. 定義通知的收件者：

    - 從下拉式清單中，選取可用的角色。
    - 和/或輸入以逗號分隔的特定電子郵件地址。 您可以輸入的電子郵件地址數目沒有限制。

1. 若要將安全性連絡人資訊套用至您的訂用帳戶，請選取 [**儲存**]。


## <a name="see-also"></a>另請參閱
若要深入瞭解安全性警示，請參閱下列內容：

* [安全性警示-參考指南](alerts-reference.md)--瞭解您可能會在 Azure 資訊安全中心的威脅防護模組中看到的安全性警示
* [管理和回應 Azure 資訊安全中心中的安全性警示](security-center-managing-and-responding-alerts.md)--瞭解如何管理及回應安全性警示