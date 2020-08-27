---
title: 條件式存取 What If 工具-Azure Active Directory
description: 瞭解如何瞭解您的條件式存取原則對您環境的影響。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: db76ec2bc2fc8640214f962a809fee559b4c4af3
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948193"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>在條件式存取中使用 What If 工具進行疑難排解

[條件式存取](./overview.md) 是 Azure Active Directory (Azure AD) 的功能，可讓您控制授權使用者如何存取您的雲端應用程式。 您如何知道環境中的條件式存取原則會有什麼預期？ 若要回答這個問題，您可以使用 **條件式存取 What If 工具**。

本文說明如何使用此工具來測試您的條件式存取原則。

## <a name="what-it-is"></a>內容

**條件式存取 What If 原則工具**可讓您瞭解條件式存取原則對您環境的影響。 這個工具並非要讓您以手動方式執行多個登入來測試原則，而是要讓您評估使用者的模擬登入。 該模擬可評估此登入對原則所造成的影響，並產生模擬報告。 報表不只會列出已套用的條件式存取原則，也會列出 [傳統原則](policy-migration.md#classic-policies) （如果有的話）。    

**What If**工具提供一種方式，可快速判斷適用于特定使用者的原則。 例如，如果您需要針對問題進行疑難排解，就可以使用這項資訊。    

## <a name="how-it-works"></a>運作方式

在 **條件式存取 What If 工具**中，您必須先設定要模擬之登入案例的設定。 這些設定包括：

- 您要測試的使用者 
- 使用者會嘗試存取的雲端應用程式
- 要在什麼樣的條件下執行雲端應用程式的存取
     
在下一個步驟中，您可以起始模擬執行來評估設定。 只有已啟用的原則才會參與評估執行。

評估完成時，此工具會產生一份受影響原則的報告。 若要收集有關條件式存取原則的詳細資訊， [條件式存取深入解析和報告活頁簿](howto-conditional-access-insights-reporting.md) 可以在僅限報表模式下提供原則的其他詳細資料，以及目前啟用的原則。

## <a name="running-the-tool"></a>執行工具

您可以在 Azure 入口網站的 [**[條件式存取-原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)**] 頁面上找到**What If**工具。

若要啟動工具，請在原則清單頂端的工具列中按一下 [ **What If**]。

![What If](./media/what-if-tool/01.png)

您必須先進行設定，才能執行評估。

## <a name="settings"></a>設定

本節為您提供模擬執行設定的相關資訊。

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>User

您只能選取一個使用者。 這是唯一的必要欄位。

### <a name="cloud-apps"></a>雲端應用程式

這項設定的預設值是 [所有雲端應用程式]****。 此預設設定會對環境中的所有可用原則進行評估。 您可以將此範圍縮小至會影響特定雲端應用程式的原則。

### <a name="ip-address"></a>IP 位址

IP 位址是用來模擬[位置條件](location-condition.md)的單一 IPv4 位址。 此位址代表使用者用來登入之裝置的網際網路對向位址。 例如，您可以瀏覽至[我的 IP 位址為何](https://whatismyipaddress.com)來確認裝置的 IP 位址。    

### <a name="device-platforms"></a>裝置平台

這項設定會模擬[裝置平台條件](concept-conditional-access-conditions.md#device-platforms)，並代表**所有平台 (包括不支援平台)** 的同等項目。 

### <a name="client-apps"></a>用戶端應用程式

這項設定會模擬[用戶端應用程式條件](concept-conditional-access-conditions.md#client-apps)。
根據預設，此設定會導致系統評估已選取 [瀏覽器]**** 和 (或) [行動裝置應用程式和桌面用戶端]**** 的所有原則。 此外，還會偵測強制執行 **Exchange ActiveSync (EAS)** 的原則。 您可以選取下列選項來縮小此設定的範圍：

- **瀏覽器**：可評估所有至少已選取 [瀏覽器]**** 的原則。 
- **行動裝置應用程式和桌面用戶端**：可評估所有至少已選取 [行動裝置應用程式和桌面用戶端]**** 的原則。 

### <a name="sign-in-risk"></a>登入風險

這項設定會模擬[登入風險條件](concept-conditional-access-conditions.md#sign-in-risk)。   

## <a name="evaluation"></a>評估 

您可以按一下 [ **What If**開始進行評估。 評估結果所提供的報告中會包含： 

![What If](./media/what-if-tool/03.png)

- 環境中是否存在傳統原則的指標
- 套用至使用者的原則
- 未套用至使用者的原則

如果選取的雲端應用程式中存在[傳統原則](policy-migration.md#classic-policies)，系統就會向您顯示指標。 按一下指標，系統就會將您重新導向至傳統原則頁面。 在傳統原則頁面中，您可以移轉傳統原則，也可以直接加以停用。 關閉此頁面就會回到評估結果。

在套用至所選使用者的原則清單中，您也可以找到使用者必須滿足的 [grant 控制項](concept-conditional-access-grant.md) 和 [會話控制項](concept-conditional-access-session.md) 清單。

在未套用至使用者的原則清單中，您也可以找到未套用這些原則的原因。 每個所列出原則的原因各代表其第一個未符合的條件。 原則已停用是之所以未套用原則的可能原因之一，因為已停用的原則不會進行進一步的評估。   

## <a name="next-steps"></a>接下來的步驟

- 有關條件式存取原則應用程式的詳細資訊，可以使用 [條件式存取深入解析和報告](howto-conditional-access-insights-reporting.md)來使用原則報告模式。
- 如果您已準備好設定環境的條件式存取原則，請參閱 [條件式存取一般原則](concept-conditional-access-policy-common.md)。