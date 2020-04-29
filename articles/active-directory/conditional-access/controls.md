---
title: Azure AD 條件式存取中的自訂控制項
description: 瞭解 Azure Active Directory 條件式存取中的自訂控制項如何工作。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050541"
---
# <a name="custom-controls-preview"></a>自訂控制項 (預覽)

自訂控制項是 Azure Active Directory 的預覽功能。 使用自訂控制項時，系統會將您的使用者重新導向至相容的服務，以滿足 Azure Active Directory 以外的驗證需求。 為了滿足此控制項，會將使用者的瀏覽器重新導向至外部服務、執行任何必要的驗證，然後重新導向回到 Azure Active Directory。 Azure Active Directory 會驗證回應，如果使用者已成功驗證或驗證，使用者會繼續在條件式存取流程中執行。

> [!NOTE]
> 如需有關我們計畫自訂控制項功能之變更的詳細資訊，請參閱2020年2月的[新功能更新](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)。

## <a name="creating-custom-controls"></a>建立自訂控制項

自訂控制項適用于一組有限的已核准驗證提供者。 若要建立自訂控制項，請先連絡您想要使用的提供者。 每個非 Microsoft 提供者都有自己的進程和需求，可註冊、訂閱或成為服務的一部分，並指出您想要與條件式存取整合。 屆時，提供者會提供您 JSON 格式的資料區塊。 此資料可讓提供者和條件式存取與您的租使用者一起工作，建立新的控制項，並定義條件式存取如何判斷您的使用者是否已成功執行提供者驗證。

複製 JSON 資料並貼到相關文字方塊中。 除非您明確瞭解您所進行的變更，否則請勿對 JSON 進行任何變更。 進行變更可能會讓提供者與 Microsoft 之間的連線中斷，進而可能將您的帳戶鎖定，讓您和您的使用者無法使用。

建立自訂控制項的選項位於 [**條件式存取**] 頁面的 [**管理**] 區段中。

![控制](./media/controls/82.png)

按一下 [新增自訂控制項]**** 隨即會開啟刀鋒視窗，裡面有文字方塊可供輸入控制項的 JSON 資料。  

![控制](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>刪除自訂控制項

若要刪除自訂控制項，您必須先確定它並未用於任何條件式存取原則中。 完成之後：

1. 移至 [自訂控制項] 清單
1. 按一下 [...]  
1. 選取 [刪除]  。

## <a name="editing-custom-controls"></a>編輯自訂控制項

若要編輯自訂控制項，您必須先刪除目前的控制項，然後使用更新後的資訊建立新的控制項。

## <a name="known-limitations"></a>已知限制

自訂控制項不能與身分識別保護的自動化搭配使用，需要 Azure 多重要素驗證、Azure AD 自助式密碼重設（SSPR）、滿足多重要素驗證宣告需求，或提升特殊許可權身分識別管理員（PIM）中的角色。

## <a name="next-steps"></a>後續步驟

- [條件式存取的一般原則](concept-conditional-access-policy-common.md)

- [僅限報告模式](concept-conditional-access-report-only.md)

- [使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
