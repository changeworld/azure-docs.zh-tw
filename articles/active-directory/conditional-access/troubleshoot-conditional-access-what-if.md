---
title: 使用"如果是什麼"工具對條件訪問進行故障排除 - Azure 活動目錄
description: 在哪裡可以找到應用了哪些條件訪問策略以及原因
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175786"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>使用"如果"工具對條件訪問進行故障排除

在嘗試瞭解策略在特定情況下被應用或未應用於使用者或策略將在已知狀態下應用時，條件訪問中的["說明"工具](what-if-tool.md)功能強大。

如果工具位於**Azure 門戶** > **Azure 活動目錄** > **條件訪問** > **中，則"如果"**

![條件訪問如果工具處於預設狀態](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> "如果"工具當前不以僅報告模式評估策略。

## <a name="gathering-information"></a>收集資訊

"如果"工具僅要求**使用者**開始使用。 

以下附加資訊是可選的，但有助於縮小特定案例的範圍。

* 雲端應用程式或動作
* IP 位址 
* Country
* 裝置平台
* 用戶端應用（預覽版）
* 設備狀態（預覽） 
* 登入風險

可以從使用者、其設備或 Azure AD 登錄日誌中收集此資訊。

## <a name="generating-results"></a>生成結果

輸入上一節中收集的條件，然後選擇 **"如果"** 以生成結果清單。 

您可以隨時選擇 **"重置"** 以清除任何條件輸入並返回到預設狀態。

## <a name="evaluating-results"></a>評估結果

### <a name="policies-that-will-apply"></a>將適用的策略

此清單將顯示在條件下將應用哪些條件訪問策略。 該清單將包括適用的授予和會話控制項。 示例包括需要多重要素驗證才能訪問特定應用程式。

### <a name="policies-that-will-not-apply"></a>不適用的策略

此清單將顯示條件訪問策略，如果應用了條件，則策略不適用。 該清單將包括任何策略及其不適用的原因。 示例包括可能從策略中排除的使用者和組。

## <a name="use-case"></a>使用案例

許多組織基於網路位置創建策略，允許受信任的位置並阻止不應進行訪問的位置。

要驗證配置是否已正確進行，管理員可以使用"假設"工具類比從應允許的位置和應被拒絕的位置進行訪問。

![如果工具顯示具有塊訪問的結果，該怎麼辦](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

在這種情況下，使用者將被禁止訪問其朝鮮之旅中的任何雲應用，因為 Contoso 已阻止從該位置訪問。

可以擴展此測試以合併其他資料點以縮小範圍。

## <a name="next-steps"></a>後續步驟

* [何謂條件式存取？](overview.md)
* [Azure Active Directory Identity Protection 是什麼？](../identity-protection/overview-v2.md)
* [什麼是裝置身分識別？](../devices/overview.md)
* [運作方式：Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
