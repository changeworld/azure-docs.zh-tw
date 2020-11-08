---
title: 如何為您的 Azure 應用程式供應專案新增技術詳細資料
description: 瞭解如何在合作夥伴中心中新增 Azure 應用程式供應專案的技術詳細資料。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369995"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>如何為您的 Azure 應用程式供應專案新增技術詳細資料

本文說明如何輸入技術詳細資料，以協助 Microsoft 商業市場連接到您的解決方案。 如果客戶選擇取得和管理您的供應專案，此連線可讓我們為客戶布建供應專案。

只有當您的供應專案包含受控應用程式，而該應用程式會使用 [Marketplace 計量付費 api](partner-center-portal/marketplace-metering-service-apis.md) 發出計量事件，並且具有將會使用 Azure AD 安全性權杖進行驗證的服務時，才會完成本節。 如需詳細資訊，請參閱不同驗證選項的 [Marketplace 計量服務驗證策略](partner-center-portal/marketplace-metering-service-authentication.md) 。

## <a name="technical-configuration-offer-level"></a>技術設定 (供應專案層級) 

只有當您要建立受控應用程式，以使用 [Marketplace 計量付費 api](partner-center-portal/marketplace-metering-service-apis.md)發出計量事件時，才會套用 [ **技術** 設定] 索引標籤。 如果是的話，請完成下列步驟。 否則，請移至 [下一個步驟](#next-steps)。 

如需這些欄位的詳細資訊，請參閱 [為商業市場規劃 Azure 應用程式供應](plan-azure-application-offer.md#technical-configuration)專案。

1. 在 [ **技術** 設定] 索引標籤上，提供 **Azure Active Directory 的租使用者識別碼** 和 **Azure Active Directory 應用程式識別碼** ，以用來驗證兩個服務之間的連線是否在經過驗證的通訊之後。

1. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤： [計畫總覽]。

## <a name="next-steps"></a>後續步驟

- [如何為您的 Azure 應用程式供應專案建立方案](create-new-azure-apps-offer-plans.md)
