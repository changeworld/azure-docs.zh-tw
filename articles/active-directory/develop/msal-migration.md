---
title: '遷移至 Microsoft 驗證程式庫 (MSAL) '
titleSuffix: Microsoft identity platform
description: 瞭解 Microsoft 驗證程式庫 (MSAL) 與 Azure AD 驗證程式庫 (ADAL) 以及如何遷移至 MSAL 之間的差異。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f84e479c4780437b09ab463781edbb52d8dfdfaa
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255620"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>將應用程式遷移至 Microsoft 驗證程式庫 (MSAL) 

許多開發人員都已使用 Azure Active Directory Authentication 程式庫 (ADAL) 來建立和部署應用程式。 我們現在建議使用 Microsoft 驗證程式庫 (MSAL) 來驗證和授權 Azure AD 實體。

藉由使用 MSAL，而不是 ADAL：

- 您可以驗證更廣泛的身分識別集：
  - Azure AD 身分識別
  - Microsoft 帳戶
  - 使用 Azure AD B2C 的社交和本機帳戶
- 您的使用者將獲得最佳的單一登入體驗。
- 您的應用程式可以啟用累加式同意。
- 支援條件式存取比較簡單。
- 您可以從創新中獲益。 因為所有的 Microsoft 開發工作現在都是專注于 MSAL，所以 ADAL 不會執行任何新功能。

**MSAL 現在是建議用於 Microsoft 身分識別平臺的驗證程式庫**。

## <a name="migration-guidance"></a>移轉指導

下列文章可協助您遷移至 MSAL：

- [遷移到 MSAL.Android](migrate-android-adal-msal.md)
- [遷移到 MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [遷移到 MSAL Java](migrate-adal-msal-java.md)
- [遷移至 MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [遷移至 MSAL.NET](msal-net-migration.md)
- [遷移到 MSAL Python](migrate-python-adal-msal.md)
- [使用訊息代理程式將 Xamarin 應用程式遷移到 MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)

__問： ADAL 是否已被取代？__  
A：是。 自2020年6月30日起，我們將不再將新功能新增至 ADAL。 我們會繼續在2022年6月30日前新增 ADAL 的重大安全性修正。

__問：如何? 知道哪些應用程式使用 ADAL？__  
答：如果您有應用程式的原始程式碼，您可以參考上述的「遷移指南」，以協助判斷應用程式所使用的程式庫，以及如何將它遷移至 MSAL。 如果您沒有應用程式原始程式碼的存取權，您可以[開啟支援要求](developer-support-help-options.md#open-a-support-request)來取得已註冊應用程式的清單，以及每個應用程式所使用的程式庫。

__問：我現有的 ADAL 應用程式是否會繼續工作？__  
答：您現有的應用程式不需要修改即可繼續工作。 如果您打算將其保留在2022年6月30日之後，您應該考慮將它們更新為 MSAL 以確保它們安全，但不需要遷移至 MSAL 來維護現有的功能。

__問：為什麼要投入 MSAL？__  
答： MSAL 包含不在 ADAL 中的新功能，包括累加式同意、單一登入和權杖快取管理。 此外，與 ADAL 不同的是，MSAL 會繼續接收2022年6月30日以外的安全性修補程式。 [深入了解](msal-overview.md)。

__問：您是否發行可協助我將應用程式從 ADAL 移至 MSAL 的工具？__  
答：否。 程式庫之間的差異需要專門用來開發和維護工具的資源，否則會花在改善 MSAL。 不過，我們提供了前述的一組遷移指南，協助您在應用程式中進行必要的變更。

__問： MSAL 如何與 AD FS 搭配運作？__  
答： MSAL.NET 支援針對 AD FS 2019 進行驗證的特定案例。 如果您的應用程式需要直接從舊版 AD FS 取得權杖，您應該保留在 ADAL 上。 [深入了解](msal-net-adfs-support.md)。

__問：如何? 取得遷移應用程式的協助嗎？__  
答：請參閱本文的[遷移指引](#migration-guidance)一節。 如果在閱讀您應用程式平臺的指南之後，有其他問題，您可以在 Stack Overflow 上張貼標記， `[adal-deprecation]` 或在程式庫的 GitHub 存放庫中提出問題。 請參閱 MSAL 總覽文章的[語言和](msal-overview.md#languages-and-frameworks)架構一節，以取得每個程式庫儲存機制的連結。

## <a name="next-steps"></a>後續步驟

- [將您的應用程式更新為使用 Microsoft 驗證程式庫和 Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Microsoft 身分識別平臺總覽](v2-overview.md)
- [回顧我們的 MSAL 程式碼範例](sample-v2-code.md)
