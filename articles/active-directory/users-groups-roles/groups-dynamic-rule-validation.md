---
title: 驗證動態群組成員身份(預覽)的規則 - Azure AD |微軟文件
description: 如何根據 Azure 活動目錄中的動態組的成員身份規則測試成員。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115517"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>在 Azure 活動目錄中驗證動態群組成員身份規則(預覽)

Azure 活動目錄 (Azure AD) 現在提供了驗證動態組規則的方法(在公共預覽中)。 在「**驗證規則」** 選項卡上,您可以針對範例組成員驗證動態規則,以確認規則按預期工作。 創建或更新動態組規則時,管理員希望知道使用者或設備是組的成員。 這有助於評估使用者或設備是否符合規則條件,並有助於在不需要成員資格時進行故障排除。

## <a name="step-by-step-walk-through"></a>分步演練

要開始,請轉到**Azure 的目錄** > **群組**。 選擇現有動態組或創建新的動態組,然後單擊動態成員身份規則。 然後,您可以看到 **『驗證規則』** 選項卡。

![尋找「驗證規則」選項卡,然後從現有規則開始](./media/groups-dynamic-rule-validation/validate-tab.png)

在 **「驗證規則」** 選項卡上,您可以選擇使用者以驗證其成員資格。 一次可以選擇 20 個使用者或設備。

![新增使用者以驗證現有規則](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

從選取器中選擇使用者或設備後,**選擇**,驗證將自動啟動,並顯示驗證結果。

![檢視規則驗證結果](./media/groups-dynamic-rule-validation/validate-tab-results.png)

結果將判斷使用者是否是組的成員。 如果規則無效或存在網路問題,則結果將顯示為 **「未知」**。 如果**未知**,詳細的錯誤消息將描述問題和所需的操作。

![檢視規則驗證結果的詳細資訊](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

您可以修改規則,並觸發成員資格的驗證。 要查看使用者為何不是組的成員,請單擊"查看詳細資訊",驗證詳細資訊將顯示組成規則的每個表達式的結果。 按下 **「確定」** 退出。

## <a name="next-steps"></a>後續步驟

- [群組的動態成員資格規則](groups-dynamic-membership.md)
