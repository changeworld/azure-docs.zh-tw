---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763352"
---
Microsoft 驗證程式庫 (MSAL) 應用程式會產生可協助診斷問題的記錄訊息。 應用程式可透過幾行程式碼來設定記錄，並針對詳細資料層級和是否要記錄個人和組織資料具有自訂控制。 建議您建立 MSAL 記錄回呼，並提供使用者在有驗證問題時提交記錄的方式。

## <a name="logging-levels"></a>記錄層級

MSAL 提供數個層級的記錄詳細資料：

- 錯誤：指出發生錯誤，並產生錯誤。 用於偵測和識別問題。
- 警告：不一定會發生錯誤或失敗，但可用於診斷和查明問題。
- Info： MSAL 將記錄適用于資訊目的的事件，而不一定是為了進行調試。
- 詳細資訊：預設值。 MSAL 會記錄程式庫行為的完整詳細資料。

## <a name="personal-and-organizational-data"></a>個人和組織資料

根據預設，MSAL 記錄器不會捕捉任何高度機密的個人或組織資料。 如果您決定這樣做，程式庫會提供選項讓您記錄個人和組織資料。

下列各節提供更多有關 MSAL 應用程式錯誤記錄的詳細資料。