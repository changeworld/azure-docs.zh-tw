---
title: 適用於 Key Vault 的 Azure Defender - 優點和功能
description: 了解適用於 Key Vault 的 Azure Defender 有何優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 6649a8d470a75922aac423bf0b411163bdd79f71
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449097"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>適用於 Key Vault 的 Azure Defender 簡介

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 

啟用**適用於 Key Vault 的 Azure Defender**，可提供適用於 Azure Key Vault 的 Azure 原生進階威脅防護，而提供多一層的安全情報。 

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|**適用於 Key Vault 的 Azure Defender** 的計費方式如[定價頁面](security-center-pricing.md)所示|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>適用於 Key Vault 的 Azure Defender 有哪些優點？

Azure Defender 會偵測是否有人試圖以不尋常且可能有害的方式存取或惡意探索 Key Vault 帳戶。 這一層保護可讓您直接解決威脅，而無須成為安全性專家，且無須管理第三方安全性監視系統。  

發生異常活動時，Azure Defender 會顯示警示，並選擇性地透過電子郵件將其傳送給組織的相關成員。 這些警示包含可疑活動的詳細資料，以及如何調查和補救威脅的建議。 

## <a name="azure-defender-for-key-vault-alerts"></a>適用於 Key Vault 的 Azure Defender 警示
當您收到適用於 Key Vault 的 Azure Defender 傳來的警示時，建議您調查並回應這些警示，如[回應適用於 Key Vault 的 Azure Defender](defender-for-key-vault-usage.md) 所說明。 適用於 Key Vault 的 Azure Defender 會保護應用程式和認證，因此，即使您熟悉觸發警示的應用程式或使用者，仍務必檢查每個警示的相關情況。

警示會出現在 Key Vault 的 [安全性] 頁面、Azure Defender 儀表板和資訊安全中心的警示頁面中。

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault 的安全性頁面":::


> [!TIP]
> 您可以依照[在 Azure 資訊安全中心驗證 Azure Key Vault 威脅偵測](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)中的指示，模擬適用於 Key Vault 的 Azure Defender 警示。


## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於 Key Vault 的 Azure Defender。

如需相關內容，請參閱下列文章： 

- [Key Vault 安全性警示](alerts-reference.md#alerts-azurekv)--所有 Azure 資訊安全中心警示的參考資料表的 Key Vault 小節
- [將警示匯出至 SIEM](continuous-export.md)
- [隱藏來自 Azure Defender 的警示](alerts-suppression-rules.md)