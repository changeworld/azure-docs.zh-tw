---
title: 針對條件式存取的登入問題進行疑難排解-Azure Active Directory
description: 本文說明當條件式存取原則導致非預期的結果時該怎麼辦
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f722977329bd5d79d4d0e410a29c730faf00c5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145088"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>對使用條件式存取的登入問題進行疑難排解

本文中的資訊可用來針對與條件式存取相關的非預期登入結果，使用錯誤訊息和 Azure AD 登入記錄進行疑難排解。

## <a name="select-all-consequences"></a>選取 [所有] 結果

條件式存取架構可為您提供絕佳的設定彈性。 不過，絕佳的彈性也表示您應該在釋出每個設定原則之前仔細檢查，以避免產生非預期的結果。 在此情況下，您應該特別注意影響整個集合的指派，例如 **all users / groups / cloud apps**。

組織應避免下列設定：

**針對所有使用者、所有雲端應用程式：**

- **封鎖存取** -此設定會封鎖您的整個組織。
- **要求裝置必須標示為符合規範** -針對尚未註冊其裝置的使用者，此原則會封鎖所有存取權，包括對 Intune 入口網站的存取權。 如果您是沒有已註冊裝置的系統管理員，此原則會阻擋您回到 Azure 入口網站來變更此原則。
- **需要混合式 Azure AD 加入網域的裝置** -此原則封鎖存取也有可能封鎖組織中所有使用者的存取權（如果他們沒有已加入混合式 Azure AD 的裝置）。
- **需要應用程式保護原則** -如果您沒有 Intune 原則，此原則封鎖存取也有可能封鎖您組織中所有使用者的存取權。 如果您的系統管理員沒有具有 Intune 應用程式保護原則的用戶端應用程式，此原則會封鎖您回到 Intune 和 Azure 等入口網站。

**針對所有使用者、所有雲端應用程式、所有裝置平台：**

- **封鎖存取** -此設定會封鎖您的整個組織。

## <a name="conditional-access-sign-in-interrupt"></a>條件式存取登入中斷

第一種方式是檢查顯示的錯誤訊息。 針對使用網頁瀏覽器登入時的問題，錯誤頁面本身會有詳細的資訊。 這項資訊本身可能會描述問題是什麼，而且可能會建議解決方案。

![需要登入符合錯誤規範的裝置](./media/troubleshoot-conditional-access/image1.png)

在上述錯誤中，訊息指出只能從符合公司行動裝置管理原則的裝置或用戶端應用程式存取應用程式。 在此情況下，應用程式和裝置不符合該原則。

## <a name="azure-ad-sign-in-events"></a>Azure AD 登入事件

取得登入中斷之詳細資訊的第二種方法是檢查 Azure AD 登入事件，以查看已套用的條件式存取原則或原則，以及原因。

按一下 [初始錯誤] 頁面中的 [詳細 **資料** ]，即可找到有關問題的詳細資訊。 按一下 [ **更多詳細資料** ] 會顯示疑難排解資訊，當您 Azure AD 搜尋使用者看到的特定失敗事件，或向 Microsoft 開啟支援事件時，這會很有説明。

![從條件式存取中斷的網頁瀏覽器登入的詳細資料。](./media/troubleshoot-conditional-access/image2.png)

以找出已套用的條件式存取原則或原則，以及為何要執行下列動作。

1. 以全域管理員、安全性系統管理員或全域讀取者身分登入 **Azure 入口網站** 。
1. 流覽至**Azure Active Directory**登  >  **入**。
1. 找出登入要檢查的事件。 新增或移除篩選和資料行，以篩選出不必要的資訊。
   1. 新增篩選以縮小範圍：
      1. 當您有要調查的特定事件時的相互**關聯識別碼**。
      1. **條件式存取** 可查看原則失敗和成功。 將篩選範圍設為僅顯示失敗以限制結果。
      1. 使用者**名稱**，以查看與特定使用者相關的資訊。
      1. 範圍設定為有問題之時間範圍的**日期**。

   ![在登入記錄中選取條件式存取篩選](./media/troubleshoot-conditional-access/image3.png)

1. 一旦找到對應到使用者登入失敗的登入事件，請選取 [ **條件式存取** ] 索引標籤。[條件式存取] 索引標籤會顯示導致登入中斷的特定原則或原則。
   1. [ **疑難排解和支援** ] 索引標籤中的資訊可能會清楚指出登入失敗的原因，例如不符合合規性需求的裝置。
   1. 若要進一步調查，請按一下 **原則名稱**，向下切入原則的設定。 按一下 **原則名稱** 將會顯示所選原則的原則設定使用者介面，以便進行審查和編輯。
   1. 您也可以在 [**基本資訊**]、[**位置**]、[**裝置資訊**]、[**驗證詳細資料**]，以及登入事件的**其他詳細資料**索引標籤中，取得用於條件式存取原則評估的**用戶端使用者**和**裝置詳細資料**。

### <a name="policy-details"></a>原則詳細資料

在登入事件中選取原則右邊的省略號，會顯示原則詳細資料。 這會提供系統管理員有關成功套用原則的其他資訊。

   ![[登入事件條件式存取] 索引標籤](./media/troubleshoot-conditional-access/image5.png)

   ![ (預覽) 的原則詳細資料](./media/troubleshoot-conditional-access/policy-details.png)

左邊提供在登入時收集的詳細資料，右邊提供這些詳細資料是否符合套用的條件式存取原則需求的詳細資料。 條件式存取原則只適用于滿足或未設定所有條件時。

如果事件中的資訊不足以瞭解登入結果或調整原則來取得所需的結果，則可能會開啟支援事件。 流覽至該登入事件的 [ **疑難排解和支援** ] 索引標籤，然後選取 [ **建立新的支援要求**]。

![登入事件的 [疑難排解和支援] 索引標籤](./media/troubleshoot-conditional-access/image6.png)

提交事件時，請在事件提交詳細資料中提供登入事件的要求識別碼和時間和日期。 此資訊可讓 Microsoft 支援服務找出您所關心的事件。

### <a name="conditional-access-error-codes"></a>條件式存取錯誤代碼

| 登入錯誤碼 | 錯誤字串 |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>如果您被鎖定 Azure 入口網站，該怎麼辦？

如果您因為條件式存取原則中的設定不正確而遭到鎖定 Azure 入口網站：

- 確認您的組織中是否有其他系統管理員尚未遭到封鎖。 具有 Azure 入口網站存取權的系統管理員可以停用影響您登入的原則。 
- 如果組織中沒有任何系統管理員可以更新原則，請提交支援要求。 Microsoft 支援人員可以在確認更新導致無法存取的條件式存取原則時，進行審查。

## <a name="next-steps"></a>後續步驟

- [Azure Active Directory 入口網站中的登入活動報告](../reports-monitoring/concept-sign-ins.md)
- [使用 What If 工具對條件式存取進行疑難排解](troubleshoot-conditional-access-what-if.md)
