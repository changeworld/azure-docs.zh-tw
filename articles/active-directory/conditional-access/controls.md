---
title: Azure AD 條件式存取中的自訂控制項
description: 瞭解 Azure Active Directory 條件式存取中的自訂控制項如何運作。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59f83d232874a7702598b6ea5fdd458c101e7e79
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266573"
---
# <a name="custom-controls-preview"></a>自訂控制項 (預覽)

自訂控制項是 Azure Active Directory 的預覽功能。 使用自訂控制項時，系統會將使用者重新導向至相容的服務，以滿足 Azure Active Directory 之外的驗證需求。 為了滿足此控制項，會將使用者的瀏覽器重新導向至外部服務、執行任何必要的驗證，然後再重新導向回 Azure Active Directory。 Azure Active Directory 會驗證回應，而且如果使用者已成功通過驗證或驗證，使用者會繼續進行條件式存取流程。

> [!NOTE]
> 如需我們規劃自訂控制項功能之變更的詳細資訊，請參閱2020年2月的封存 [以瞭解新](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls)功能。

## <a name="creating-custom-controls"></a>建立自訂控制項

自訂控制項適用于一組有限的經過核准的驗證提供者。 若要建立自訂控制項，請先連絡您想要使用的提供者。 每個非 Microsoft 提供者都有自己的流程和需求，可供註冊、訂閱或以其他方式成為服務的一部分，並指出您想要與條件式存取整合。 屆時，提供者會提供您 JSON 格式的資料區塊。 這種資料可讓提供者和條件式存取與您的租使用者一起運作、建立新的控制項，並定義條件式存取如何分辨使用者是否已成功向提供者執行驗證。

複製 JSON 資料並貼到相關文字方塊中。 除非您明確瞭解正在進行的變更，否則請勿對 JSON 進行任何變更。 進行變更可能會讓提供者與 Microsoft 之間的連線中斷，進而可能將您的帳戶鎖定，讓您和您的使用者無法使用。

建立自訂控制項的選項位於 [**條件式存取**] 頁面的 [**管理**] 區段中。

![條件式存取中的自訂控制項介面](./media/controls/custom-controls-conditional-access.png)

按一下 [新增自訂控制項]**** 隨即會開啟刀鋒視窗，裡面有文字方塊可供輸入控制項的 JSON 資料。  

![新增自訂控制項](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>刪除自訂控制項

若要刪除自訂控制項，您必須先確定它不是在任何條件式存取原則中使用。 完成之後：

1. 移至 [自訂控制項] 清單
1. 按一下 [...]  
1. 選取 [刪除]  。

## <a name="editing-custom-controls"></a>編輯自訂控制項

若要編輯自訂控制項，您必須先刪除目前的控制項，然後使用更新後的資訊建立新的控制項。

## <a name="known-limitations"></a>已知限制

自訂控制項不能搭配需要 Azure Multi-Factor Authentication 的身分識別保護、Azure AD 自助式密碼重設 (SSPR) 、滿足多重要素驗證宣告需求、在 Intune 裝置註冊的過程中提升許可權身分識別管理員 (PIM) 中的角色，或將裝置加入 Azure AD 時使用。

## <a name="next-steps"></a>後續步驟

- [條件式存取一般原則](concept-conditional-access-policy-common.md)

- [僅限報告模式](concept-conditional-access-report-only.md)

- [使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
