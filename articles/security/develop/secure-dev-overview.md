---
title: Microsoft Azure 的安全開發最佳作法
description: 最佳作法可協助您開發更安全的程式碼，並在雲端部署更安全的應用程式。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "68934857"
---
# <a name="secure-development-best-practices-on-azure"></a>Azure 上的安全開發最佳作法
這一系列的文章提供當您開發雲端應用程式時所要考慮的安全性活動和控制項。 涵蓋在生命週期的每個階段所要考慮的 Microsoft 安全性開發週期 (SDL) （SDL）和安全性問題和概念的階段。 其目標是要協助您定義活動和 Azure 服務，以便在生命週期的每個階段中用來設計、開發和部署更安全的應用程式。

文章中的建議來自我們的 Azure 安全性經驗，以及我們的客戶體驗。 您可以使用這些文章做為您在開發專案的特定階段應考慮之事項的參考，但我們建議您也一併閱讀所有文章，從一開始到結束至少一次。 閱讀所有文章會為您介紹您在專案的先前階段中可能錯過的概念。 在您發行產品之前執行這些概念，可協助您建立安全的軟體、解決安全性合規性需求，並降低開發成本。

這些文章的目標是要建立及部署安全 Azure 應用程式之所有層級的軟體設計師、開發人員和測試人員的資源。

## <a name="overview"></a>總覽

安全性是任何應用程式最重要的其中一個層面，而不是一個簡單的東西。 幸運的是，Azure 提供許多服務，可協助您保護雲端中的應用程式。 這些文章說明您可以在軟體發展生命週期的每個階段實行的活動和 Azure 服務，以協助您開發更安全的程式碼，並在雲端部署更安全的應用程式。

## <a name="security-development-lifecycle"></a>安全性開發生命週期

遵循安全軟體發展的最佳作法需要將安全性整合到軟體發展生命週期的每個階段，從需求分析到維護，不論專案的方法為何（[瀑布](https://en.wikipedia.org/wiki/Waterfall_model)、 [agile](https://en.wikipedia.org/wiki/Agile_software_development)或[DevOps](https://en.wikipedia.org/wiki/DevOps)）。 在喚醒高分析資料和操作安全性瑕疵的問題時，有更多開發人員瞭解在整個開發過程中都必須解決安全性問題。

稍後您可以在開發生命週期中修正問題，修正的越多成本就愈高。 安全性問題並不例外。 如果您在軟體發展的早期階段忽略安全性問題，下列每個階段可能會繼承上一個階段的弱點。 您的最終產品將會累積多個安全性問題，以及入侵的可能性。 在開發生命週期的每個階段中建立安全性，可協助您及早攔截問題，並協助您降低開發成本。

我們會遵循 Microsoft[安全性開發生命週期（SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的各個階段，引進活動和 Azure 服務，讓您在生命週期的每個階段中用來履行安全軟體發展實務。

SDL 階段包括：

  - 訓練
  - 規格需求
  - 設計
  - 實作
  - 驗證
  - 版本
  - 回應

![安全性開發生命週期](./media/secure-dev-overview/01-sdl-phase.png)

在這些文章中，我們將 SDL 階段分為設計、開發和部署。

## <a name="engage-your-organizations-security-team"></a>與您組織的安全性小組互動

您的組織可能會有正式的應用程式安全性計畫，可協助您在開發週期內從一開始就完成安全性活動。 如果您的組織有安全性與合規性小組，請務必在開始開發應用程式之前，先與他們互動。 無論您是否錯過任何工作，都請在 SDL 的每個階段詢問他們。

我們瞭解許多讀者可能沒有要參與的安全性或合規性小組。 這些文章可協助引導您在每個 SDL 階段都需要考慮的安全性問題和決策。

## <a name="resources"></a>資源

使用下列資源深入瞭解如何開發安全應用程式，並協助保護您在 Azure 上的應用程式：

[Microsoft 安全性開發週期 (SDL) （sdl）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – Sdl 是 Microsoft 的軟體發展流程，可協助開發人員建立更安全的軟體。 它可協助您解決安全性合規性需求，同時降低開發成本。

[開放式 Web 應用程式安全性專案（OWASP）](https://www.owasp.org/index.php/Main_Page) – OWASP 是線上的社區，可在 Web 應用程式安全性的領域中，產生免費的文章、方法、檔、工具和技術。

[向左推，例如老闆](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca)–一系列的線上文章，其中概述開發人員應該完成以建立更安全的程式碼的不同類型的應用程式安全性活動。

[Microsoft 身分識別平臺](../../active-directory/develop/index.yml)– microsoft 身分識別平臺是 Azure AD 身分識別服務和開發人員平臺的演進。 它是一個功能完整的平臺，由驗證服務、開放原始碼程式庫、應用程式註冊與設定、完整開發人員檔、程式碼範例和其他開發人員內容所組成。 Microsoft 身分識別平臺支援業界標準通訊協定，例如 OAuth 2.0 和 OpenID Connect。

[Azure 解決方案的安全性最佳作法](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)–當您使用 Azure 來設計、部署和管理雲端解決方案時，所要使用的安全性最佳作法集合。 本檔旨在做為 IT 專業人員的資源。 其中可包括建置和部署安全 Azure 解決方案的設計人員、架構設計師、開發人員和測試人員。

[Azure 上的安全性與合規性藍圖](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview)– azure 安全性與合規性藍圖是可協助您建立及啟動符合嚴格法規和標準之雲端動力應用程式的資源。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議可協助您設計、開發及部署安全應用程式的安全性控制和活動。

- [設計安全的應用程式](secure-design.md)
- [開發安全的應用程式](secure-develop.md)
- [部署安全的應用程式](secure-deploy.md)
