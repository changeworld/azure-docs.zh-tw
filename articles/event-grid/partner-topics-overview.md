---
title: Azure 事件方格合作夥伴主題
description: 使用 Azure 事件方格，將來自協力廠商事件方格 SaaS 和 PaaS 合作夥伴的事件直接傳送至 Azure 服務。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690034"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>使用 Azure 事件方格的合作夥伴主題 (預覽)
合作夥伴主題可讓您將協力廠商的事件來源直接連線到事件方格。 這項整合可讓您使用從 Azure 服務訂閱事件的相同方式，訂閱來自合作夥伴的事件。 

## <a name="available-partners"></a>可用的合作夥伴
第一個透過事件方格合作夥伴主題提供的合作夥伴是 Auth0。 [Auth0 合作夥伴主題](auth0-overview.md)可讓您連線 Auth0 和 Azure 帳戶。 可即時回應、記錄及監視 Auth0 事件的整合。

[現在就試試看](auth0-how-to.md)，登入您的 Auth0 帳戶，並建立方格事件整合。 當您在 Auth0 中按一下 [建立] 之後，您就會在 Azure 帳戶中看到擱置中的 Auth0 主題。 按一下 [啟動]，您就可以建立事件訂閱、路由、篩選條件及傳遞事件，就像您處理任何其他事件來源一樣。

## <a name="pricing"></a>定價
合作夥伴主題的收費方式與系統主題的作業費率相同。

## <a name="limits"></a>限制
合作夥伴主題處於公開預覽狀態。 在公開預覽期間，合作夥伴主題須遵守與系統主題和自訂主題[相同的限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits)。

## <a name="how-do-i-become-an-event-grid-partner"></a>如何成為事件方格合作夥伴？
為了支援此發行而建立的基礎結構，讓新的合作夥伴可以輕鬆快速地將其事件功能與事件方格整合。 如需詳細資訊，請閱讀[合作夥伴上線文件](partner-onboarding-overview.md)。

## <a name="next-steps"></a>後續步驟

- [Auth0 合作夥伴主題](auth0-overview.md)
- [如何使用 Auth0 合作夥伴主題](auth0-how-to.md)
- [變成事件方格合作夥伴](partner-onboarding-overview.md)