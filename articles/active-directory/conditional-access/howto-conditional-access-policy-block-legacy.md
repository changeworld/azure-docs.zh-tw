---
title: 條件式存取 - 封鎖舊式驗證 - Azure Active Directory
description: 建立自訂條件式存取原則來封鎖舊式驗證通訊協定
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cf241ec9a26ea2c9e877e40205065b6ad4e0a98
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641331"
---
# <a name="conditional-access-block-legacy-authentication"></a>條件式存取：封鎖舊式驗證

由於與舊版驗證通訊協定相關的風險增加，Microsoft 建議組織使用這些通訊協定封鎖驗證要求，並要求進行新式驗證。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

下列步驟將協助建立條件式存取原則，以封鎖舊式驗證要求。 此原則會以[報告專用模式](howto-conditional-access-report-only.md)啟動，讓管理員可以判斷對現有使用者的影響。 當管理員確信原則會如預期套用時，可以切換至 [開啟]，或藉由新增特定群組和排除其他群組以進行階段部署。

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下，選取 [使用者和群組]，並選擇必須維持使用舊版驗證功能的任何帳戶。 至少排除一個帳戶，以防您遭到鎖定。如果您未排除任何帳戶，將無法建立此原則。
   1. 選取 [完成] 。
1. 在 [雲端應用程式或動作] 下，選取 [所有雲端應用程式]。
   1. 選取 [完成] 。
1. 在 [條件] > [用戶端應用程式 (預覽)] 下，將 [設定] 設為 [是]。
   1. 僅勾選方塊 [行動裝置應用程式和桌面用戶端] > [其他用戶端]。
   1. 選取 [完成] 。
1. 在 [存取控制] > [授與] 下，選取 [封鎖存取]。
   1. 選取 [選取] 。
1. 確認您的設定，並將 [啟用原則] 設為 [報告專用]。
1. 選取 [建立]，以建立並啟用您的原則。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)

[如何將多功能裝置或應用程式設定為使用 Office 365 和 Microsoft 365 傳送電子郵件](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
