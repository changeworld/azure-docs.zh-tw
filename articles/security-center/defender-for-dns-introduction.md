---
title: 適用於 DNS 的 Azure Defender - 優點和功能
description: 了解適用於 DNS 的 Azure Defender 的優點和功能
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 957e39f7629337182c3e19a1a514c42883666301
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796990"
---
# <a name="introduction-to-azure-defender-for-dns"></a>適用於 DNS 的 Azure Defender 簡介

[Azure DNS](../dns/dns-overview.md) 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。

適用於 DNS 的 Azure Defender 會藉由下列方式，為您的雲端資源提供額外的保護層級：

- 持續監視來自 Azure 資源的所有 DNS 查詢
- 執行進階安全性分析，在發生可疑活動時提供警示

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|預覽<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|定價：|**適用於 DNS 的 Azure Defender** 的計費方式如 [定價頁面](security-center-pricing.md)所示|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>適用於 DNS 的 Azure Defender 有哪些優點？

適用於 DNS 的 Azure Defender 可避免：

- 使用 DNS 通道從您的 Azure 資源將資料外流
- 與 C&C 伺服器通訊的惡意程式碼
- 與惡意網域通訊，以進行網路釣魚和密碼編譯採礦
- DNS 攻擊 - 與惡意 DNS 解析程式通訊 

適用於 DNS 的 Azure Defender 所提供的警示完整清單位於[警示參考頁面](alerts-reference.md#alerts-dns)。

## <a name="dependencies"></a>相依性

適用於 DNS 的 Azure Defender 不會使用任何代理程式。 

若要保護您的 DNS 層，請依照[啟用 Azure Defender](security-center-pricing.md#enable-azure-defender) 中所述，為每個訂用帳戶啟用適用於 DNS 的 Azure Defender。


## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於 DNS 的 Azure Defender。 如需相關內容，請參閱下列文章： 

- 安全性警示可能由資訊安全中心產生，或由不同的安全性產品資訊安全中心接收。 若要將所有警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。

- > [!div class="nextstepaction"]
    > [啟用 Azure Defender](security-center-pricing.md#enable-azure-defender)