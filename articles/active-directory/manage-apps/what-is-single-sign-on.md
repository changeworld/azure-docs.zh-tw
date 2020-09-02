---
title: 什麼是Azure 單一登入 (SSO)？
description: 了解單一登入 (SSO) 如何與 Azure Active Directory 搭配運作。 請使用 SSO 讓使用者不需要記住每個應用程式的密碼。 也請使用 SSO 來簡化帳戶管理作業。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperfq1
ms.openlocfilehash: 6c7ac840dfba665c4691fc4d389d66f44b077bf7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069463"
---
# <a name="what-is-single-sign-on-sso"></a>什麼是單一登入 (SSO)？

單一登入表示使用者不需要登入其使用的每個應用程式。 使用者登入一次後，該認證也會用於其他應用程式。

如果您是終端使用者，很可能並不在意 SSO 的詳情。 您只想要使用應用程式來提高生產力，而不需要這麼頻繁地輸入密碼。 您可以在下列位置找到您的應用程式： https://myapps.microsoft.com 。
 
如果您是管理員或 IT 專業人員，請繼續閱讀以深入了解 SSO，以及如何在 Azure 中加以實作。

## <a name="single-sign-on-basics"></a>單一登入基本概念
單一登入在使用者登入和使用應用程式的方式上提供了長足進步。 單一登入型驗證系統通常稱為「新式驗證」。 新式驗證和單一登入屬於稱為「身分識別與存取管理 (IAM)」的一種運算類別。 若要了解如何實現單一登入，請參閱這段影片。

驗證基本概念：基本概念 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>單一登入與 Web 應用程式
Web 應用程式極為熱門。 Web 應用程式由各種公司託管，並以服務形式提供。 一些熱門的 Web 應用程式範例包括 Microsoft 365、GitHub 和 Salesforce，還有數千種 Web 應用程式。 用戶會在電腦上使用網頁瀏覽器來存取 Web 應用程式。 單一登入可讓使用者在各種 Web 應用程式之間瀏覽，而不需要多次登入。

若要了解單一登入如何與 Web 應用程式搭配運作，請參閱這兩段影片。

驗證基本概念：Web 應用程式 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

驗證基本概念：Web 單一登入 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>雲端與內部部署託管的應用程式
實作單一登入的方式取決於託管應用程式的位置。 為了存取應用程式而路由傳送網路流量的方式，讓裝載動作變得很重要。 如果應用程式透過區域網路 (稱為內部部署應用程式) 來裝載和存取，則使用者不需要存取網際網路就能使用應用程式。 如果應用程式裝載於其他位置 (稱為雲端託管應用程式)，使用者就必須存取網際網路才能使用應用程式。

> [!TIP]
> 雲端託管應用程式也稱為軟體即服務 (SaaS) 應用程式。 

雲端託管應用程式的單一登入相當簡單。 您可讓識別提供者知道應用程式正在使用雲端託管應用程式。 然後，您可將應用程式設定為信任識別提供者。 若要了解如何使用 Azure AD 作為應用程式的識別提供者，請參閱[應用程式管理上的快速入門系列](view-applications-portal.md)。

> [!TIP]
> 雲端和網際網路這兩個詞通常可交換使用。 之所以如此與網路圖有關。 圖上常會用雲的圖形來代表大型電腦網路，因為在實務上，我們不可能畫出每個元件。 網際網路是最知名的網路，因此很容易就會交換使用這些詞。 不過，任何電腦網路都可以創造出雲端。

您也可以針對內部部署應用程式使用單一登入。 進行內部部署 SSO 的技術稱為應用程式 Proxy。 若要深入了解，請參閱[單一登入選項](sso-options.md)。

## <a name="multiple-identity-providers"></a>多重識別提供者
當您設定單一登入以在多個識別提供者之間運作時，這種方式稱為同盟。 若要了解同盟如何運作，請參閱這段影片。

驗證基本概念：同盟 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>後續步驟
* [應用程式管理快速入門系列](view-applications-portal.md)
* [單一登入選項](sso-options.md)
