---
title: 適用于 Key Vault 的 Azure Defender-優點和功能
description: 瞭解適用于 Key Vault 的 Azure Defender 的優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 06818e443568918e2ee87bbfbec81836ea85648b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934603"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>適用于 Key Vault 的 Azure Defender 簡介

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 

針對適用于 Azure 的 **Key Vault 啟用 Azure Defender** -原生、先進的威脅防護，Azure Key Vault 以提供額外一層的安全情報。 

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式上市 (GA)|
|定價：|**適用于 Key Vault 的 Azure Defender**會依[定價頁面上的](security-center-pricing.md)顯示方式計費|
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端<br>![No](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Azure Defender 的 Key Vault 有哪些優點？

Azure Defender 會偵測到不尋常且可能有害的嘗試存取或惡意探索 Key Vault 帳戶。 這一層保護可讓您直接解決威脅，而無須成為安全性專家，且無須管理第三方安全性監視系統。  

發生異常活動時，Azure Defender 會顯示警示，並選擇性地透過電子郵件將警示傳送給組織中的相關成員。 這些警示包含可疑活動的詳細資料，以及如何調查和補救威脅的建議。 

## <a name="azure-defender-for-key-vault-alerts"></a>適用于 Key Vault 警示的 Azure Defender
當您從 Azure Defender 針對 Key Vault 收到警示時，建議您調查並回應警示，如 [回應 Azure defender for Key Vault](defender-for-key-vault-usage.md)中所述。 適用于 Key Vault 的 Azure Defender 可保護應用程式和認證，因此即使您熟悉觸發警示的應用程式或使用者，還是必須檢查每個警示周圍的狀況。

警示會出現在 Key Vault 的 **安全性** 頁面、Azure Defender 儀表板和安全性中心的 [警示] 頁面中。

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault 的安全性頁面":::

## <a name="next-steps"></a>下一步

在本文中，您已瞭解 Azure Defender 的 Key Vault。

如需相關內容，請參閱下列文章： 

- [Key Vault 安全性警示](alerts-reference.md#alerts-azurekv)--所有 Azure 資訊安全中心警示的參考資料表 Key Vault 區段
- [將警示匯出至 SIEM](continuous-export.md)
- [隱藏 Azure Defender 的警示](alerts-suppression-rules.md)