---
title: Microsoft Azure 上的安全開發最佳作法
description: 協助您開發更安全的程式碼，並在雲端部署更安全的應用程式的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68934857"
---
# <a name="secure-development-best-practices-on-azure"></a>Azure 上的安全開發最佳做法
這一系列文章提供當您開發雲端應用程式時所要考慮的安全性活動和控制項。 Microsoft 安全性開發生命週期的階段 (SDL) ，以及在生命週期的每個階段所要考慮的安全性問題和概念。 其目標是協助您定義活動和 Azure 服務，您可以在生命週期的每個階段中使用這些活動，以設計、開發及部署更安全的應用程式。

這些文章中的建議來自于我們的 Azure 安全性體驗，以及我們的客戶體驗。 您可以使用這些文章做為您在開發專案的特定階段時應考慮的內容參考，但我們建議您也要一次閱讀所有文章，從一開始到最後一次。 閱讀所有文章會為您介紹可能在專案的先前階段中遺漏的概念。 在您發行產品之前執行這些概念，可協助您建立安全的軟體、解決安全性合規性需求，並降低開發成本。

這些文章旨在做為軟體設計人員、開發人員和測試人員的資源，這些都是在建立和部署安全的 Azure 應用程式的所有層級。

## <a name="overview"></a>概觀

安全性是任何應用程式最重要的層面之一，而且不是很簡單的事。 幸運的是，Azure 提供許多服務，可協助您保護雲端中的應用程式。 這些文章說明您可以在軟體發展生命週期的每個階段實行的活動和 Azure 服務，以協助您開發更安全的程式碼，並在雲端中部署更安全的應用程式。

## <a name="security-development-lifecycle"></a>安全性開發生命週期

遵循安全軟體發展的最佳作法，需要將安全性整合到軟體發展生命週期的每個階段，從需求分析到維護，無論專案方法 ([瀑布](https://en.wikipedia.org/wiki/Waterfall_model)式、 [agile](https://en.wikipedia.org/wiki/Agile_software_development)或 [DevOps](https://en.wikipedia.org/wiki/DevOps)) 。 在喚醒高設定檔的資料缺口和操作安全性瑕疵時，越來越多的開發人員都知道，在整個開發過程中必須解決安全性問題。

您稍後可以修正開發生命週期中的問題，修正的成本就愈高。 安全性問題並沒有例外。 如果您在軟體發展的早期階段忽略安全性問題，接下來的每個階段可能會繼承先前階段的弱點。 您的最終產品將會累積多個安全性問題和入侵的可能性。 在開發生命週期的每個階段中建立安全性，可協助您及早攔截問題，並協助您降低開發成本。

我們會遵循 Microsoft [安全性開發生命週期的各個階段 (SDL) ](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 引進活動和 Azure 服務，您可以在生命週期的每個階段中用來履行安全的軟體發展實務。

SDL 階段包括：

  - 訓練
  - 需求
  - 設計
  - 實作
  - 驗證
  - 版本
  - 回應

![安全性開發生命週期](./media/secure-dev-overview/01-sdl-phase.png)

在這些文章中，我們將 SDL 階段組成設計、開發和部署。

## <a name="engage-your-organizations-security-team"></a>與貴組織的安全性小組互動

您的組織可能會有正式的應用程式安全性方案，可協助您在開發生命週期中從開始到完成的安全性活動。 如果您的組織有安全性與合規性小組，請務必在開始開發您的應用程式之前，先與其互動。 請在 SDL 的每個階段詢問他們是否有任何您錯過的工作。

我們瞭解許多讀者可能沒有要參與的安全性或合規性小組。 這些文章可協助引導您在每個 SDL 階段需要考慮的安全性問題和決策。

## <a name="resources"></a>資源

使用下列資源來深入瞭解如何開發安全的應用程式，並協助保護您在 Azure 上的應用程式：

[Microsoft 安全性開發週期 (sdl) ](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – Sdl 是 microsoft 的軟體發展流程，可協助開發人員建立更安全的軟體。 它可協助您解決安全性合規性需求，同時降低開發成本。

[開放 Web 應用程式安全性專案 (OWASP) ](https://www.owasp.org/index.php/Main_Page) – OWASP 是線上的社區，可在 Web 應用程式安全性領域中產生免費的文章、方法、檔、工具和技術。

[像老闆一樣地推入，](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) 這一系列的線上文章將概述開發人員應完成以建立更安全程式碼的各種不同類型的應用程式安全性活動。

[Microsoft](../../active-directory/develop/index.yml) 身分識別平臺-microsoft 身分識別平臺是 Azure AD 身分識別服務與開發人員平臺的演進。 它是完整功能的平臺，由驗證服務、開放原始碼程式庫、應用程式註冊與設定、完整開發人員檔、程式碼範例和其他開發人員內容所組成。 Microsoft 身分識別平臺支援業界標準的通訊協定，例如 OAuth 2.0 和 OpenID Connect。

[Azure 解決方案的安全性最佳作法](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) –當您使用 azure 設計、部署及管理雲端解決方案時，所要使用的安全性最佳作法集合。 這份檔是專為 IT 專業人員提供的資源。 其中可包括建置和部署安全 Azure 解決方案的設計人員、架構設計師、開發人員和測試人員。

[Azure 上的安全性與合規性藍圖](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) -azure 安全性與合規性藍圖是可協助您建立及啟動符合嚴格法規和標準之雲端架構應用程式的資源。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議可協助您設計、開發及部署安全應用程式的安全性控制與活動。

- [設計安全的應用程式](secure-design.md)
- [開發安全的應用程式](secure-develop.md)
- [部署安全的應用程式](secure-deploy.md)
