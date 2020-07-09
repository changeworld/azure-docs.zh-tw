---
title: Azure 事件方格合作夥伴主題
description: 使用 Azure 事件方格，將來自協力廠商事件方格 SaaS 和 PaaS 合作夥伴的事件直接傳送至 Azure 服務。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 97829f262a00e2353624b2a3fa9df210518ad3a0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115990"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Azure 事件方格中的合作夥伴主題（預覽）
藉由使用合作夥伴主題，您可以將協力廠商事件來源直接連線到 Azure 事件方格。 這項整合可讓您使用從 Azure 服務訂閱事件的相同方式，訂閱來自合作夥伴的事件。 

## <a name="available-partners"></a>可用的合作夥伴
第一個透過事件方格合作夥伴主題提供的合作夥伴是 Auth0。 您可以使用[Auth0 合作夥伴主題](auth0-overview.md)來連接您的 Auth0 和 Azure 帳戶。 此整合可讓您即時回應、記錄及監視 Auth0 事件。

[若要試用](auth0-how-to.md)，請登入您的 Auth0 帳戶，並建立事件方格整合。 在 Auth0 中選取 [**建立**] 之後，您會在 Azure 帳戶中看到擱置中的 Auth0 主題。 選取 [**啟用**]，您就可以建立事件方格訂用帳戶來路由傳送、篩選及傳遞事件，就像處理任何其他事件來源一樣。

## <a name="pricing"></a>定價
合作夥伴主題的收費方式與系統主題的作業費率相同。

## <a name="limits"></a>限制
合作夥伴主題處於公開預覽狀態。 在公開預覽期間，合作夥伴主題受限於與系統主題和自訂主題[相同的限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits)。

## <a name="how-do-i-become-an-event-grid-partner"></a>如何成為事件方格合作夥伴？
為了支援此發行而建立的基礎結構，讓新的合作夥伴可以輕鬆快速地將其事件功能與事件方格整合。 如需詳細資訊，請參閱[合作夥伴上架檔](partner-onboarding-overview.md)。

## <a name="next-steps"></a>後續步驟

- [Auth0 合作夥伴主題](auth0-overview.md)
- [如何使用 Auth0 合作夥伴主題](auth0-how-to.md)
- [變成事件方格合作夥伴](partner-onboarding-overview.md)