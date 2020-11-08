---
title: 依供應專案類型發佈指南-Microsoft 商業 marketplace
description: 本文說明 Microsoft 商用 marketplace 中提供的供應專案類型。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/06/2020
ms.openlocfilehash: a2b41ac32f8c887d7a8ffd33d51baebaa0e2482e
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366480"
---
# <a name="publishing-guide-by-offer-type"></a>依供應項目類型的發行指南

本文說明商用 marketplace 中可用的供應專案類型。 *供應專案類型* 會定義供應專案結構，其中包含商業市集中顯示的中繼資料、成品和其他內容。

在您 [決定發佈選項](determine-your-listing-type.md)之後，必須先選擇供應專案類型，才能開始在合作夥伴中心中建立供應專案。 供應項目類型會對應至您想要發佈的解決方案、應用程式服務供應項目類型，以及對等的 Microsoft 產品和服務。

您可以使用不同的方式來設定單一供應專案類型，以啟用不同的發佈選項、清單選項、布建或定價。 發佈選項和供應項目類型的組態，也會符合供應項目資格和技術需求。

建立您的供應專案之前，請務必先參閱線上商店和供應專案類型資格需求，以及技術發佈需求。

## <a name="list-of-offer-types"></a>供應項目類型的清單

下表顯示合作夥伴中心中的商業 marketplace 供應專案類型。

| **供應項目類型**    | **描述**  |
| :------------------- | :-------------------|
| [**Azure 應用程式**](plan-azure-application-offer.md) | 有兩種 Azure 應用程式方案： _解決方案範本_ 和 _受控應用程式_ 。 這兩種方案類型都支援將解決方案的部署和設定自動化，但 (VM) 以外的單一虛擬機器。 您可以自動化提供多個資源的程式，包括 Vm、網路和儲存體資源，以提供複雜的解決方案，例如 IaaS 解決方案。 這兩種方案類型都可以採用許多不同類型的 Azure 資源，包括但不限於 Vm。<ul><li>**解決方案範本** 方案是在商業 marketplace 中發佈解決方案的其中一種主要方式。 解決方案範本方案不會可交易在商業 marketplace 中，但可以用來部署透過商業市場計費的付費 VM 供應專案。 當客戶將管理解決方案，而且交易是透過另一個方案計費時，請使用方案範本方案類型。</li><br><li>**受控應用程式** 方案可讓您輕鬆地為客戶建立並提供完全受控的現成應用程式。 它們具有與解決方案範本方案相同的功能，但有一些主要差異：</li><ul><li> 資源會部署至資源群組，並由應用程式的發行者管理。 資源群組存在於客戶的訂用帳戶，但發行者租用戶中的身分識別可以存取資源群組。</li><li>您可以使用「發行者」來指定持續支援解決方案的成本，並透過商業市場支援交易。</li></ul>當您或您的客戶要求解決方案由夥伴管理，或您將部署以訂用帳戶為基礎的解決方案時，請使用受控應用程式方案類型。</ul> |
| [**Azure 容器**](marketplace-containers.md) | 當您的解決方案是布建為以 Kubernetes 為基礎的 Azure container service 的 Docker 容器映射時，請使用 Azure 容器供應專案類型。 |
| [**Azure 虛擬機器**](marketplace-virtual-machines.md) | 當您要將虛擬設備部署到與客戶相關的訂用帳戶時，請使用「虛擬機器」供應項目類型。 |
| [**諮詢服務**](consulting-services.md) | 諮詢服務可協助您將客戶與服務連線，以支援及擴充其 Azure、Dynamics 365 或 Power Suite 服務的使用。|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | 您可以發佈建置於或擴充 Dynamics 365 Business Central、Dynamics 365 Customer Engagement、Power Apps 和財務和營運應用程式的 AppSource 供應專案。|
| [**IoT Edge 模組**](iot-edge-module.md) | Azure IoT Edge 模組是 IoT Edge 所管理的最小計算單位，並可包含 Microsoft 服務 (例如「Azure 串流分析」)、第三方服務，或您自己的解決方案特定程式碼。 |
| [**受控服務**](partner-center-portal/create-new-managed-service-offer.md) | 您可以透過 [Azure Lighthouse](../lighthouse/overview.md)建立受控服務供應專案，以及管理客戶委派的訂用帳戶或資源群組。|
| [**Power BI 應用程式** <br/>**Microsoft 365**](appsource-offer-publishing-guide.md) | 您可以發佈建置於或擴充 Power BI 和 Microsoft 365 的 AppSource 供應專案。|
| [**軟體即服務**](plan-saas-offer.md) | 使用「軟體即服務」 (SaaS) 供應專案類型，讓客戶以訂用帳戶的形式購買您的 SaaS 型技術解決方案。 如需 SaaS 供應專案的單一登入需求的詳細資訊，請參閱在 [商業市場中 Azure AD 和可交易 saas](azure-ad-saas.md)供應專案。 |


## <a name="next-steps"></a>後續步驟

- 請參閱您的供應專案類型之對應文章中的資格需求，以完成供應專案的選取和設定。
- 請參閱每個線上商店的發佈模式，以取得解決方案如何對應至供應專案類型和設定的範例。