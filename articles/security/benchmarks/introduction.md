---
title: Azure 安全性效能評定簡介
description: 安全性基準測試簡介
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369186"
---
# <a name="azure-security-benchmark-introduction"></a>Azure 安全性基準測試簡介

Azure 中每天都會發行新的服務和功能，開發人員會快速發行以這些服務為基礎的新雲端應用程式，而攻擊者一律會尋求新的方法來利用設定錯誤的資源。 雲端的移動速度很快，開發人員可以快速移動，而攻擊者一律在移動上。 您如何保持運作，並確保您的雲端部署安全？ 雲端系統的安全性作法與內部部署系統有何不同？ 您要如何監視許多獨立開發小組之間的一致性？

Microsoft 發現，使用 *安全性基準* 檢驗可協助您快速保護雲端部署的安全。 雲端服務提供者提供的基準測試建議可提供您在環境中選取特定安全性設定的起點，並可讓您快速降低組織的風險。

Azure 安全性基準測試包含一組高度影響的安全性建議，可用來協助保護您在 Azure 中使用的服務：

- **安全性控制**：這些建議一般適用于您的 azure 租使用者和 azure 服務。 每個建議都會識別共同作業者的清單，這些專案關係人通常牽涉到規劃、核准或實行基準測試。 
- **服務基準**：這些會將控制項套用至個別的 Azure 服務，以針對該服務的安全性設定提供建議。

## <a name="implement-the-azure-security-benchmark"></a>執行 Azure 安全性基準測試
- 藉由查看企業控制項和服務專屬基準的 [檔](overview.md)來 **規劃您的** Azure 安全性基準測試，以規劃您的控制架構，以及它如何對應至 CIS (控制 7.1) 和 NIST (SP 800-53) framework。
- 使用 Azure 資訊安全中心的 [法規合規性儀表板](../../security-center/security-center-compliance-dashboard.md)，透過 Azure 安全性效能評定狀態 (和其他控制集) 來 **監視** 您的合規性。
- **建立護欄** ，以將安全設定自動化，並強制遵循 Azure 安全性基準測試 (，以及您組織) 中的其他需求與 Azure 藍圖和 Azure 原則。
 
請注意，Azure 安全性基準測試 v2 符合 [microsoft 的安全性最佳作法](/security/compass/microsoft-security-compass-introduction) (先前的 Azure 安全性羅盤) ，因此 Azure 安全性基準測試可提供 microsoft azure 安全性建議的單一匯總視圖。

## <a name="common-use-cases"></a>常見使用案例

Azure 安全性基準通常用來解決下列客戶或服務合作夥伴的常見挑戰：
- 最新的 Azure，並且正在尋找安全性最佳作法，以確保安全的部署。
- 改善現有 Azure 部署的安全性狀態，以優先處理最高風險和緩和措施。
- 核准 Azure 服務以供技術和企業使用，以符合特定的安全性指導方針。
- 符合政府或高度規範的產業（例如財務和醫療保健）之客戶的法規需求， (或需要為這些客戶建立系統的服務廠商) 。 這些客戶必須確保其 Azure 設定符合產業架構中所指定的安全性功能，例如 CIS、NIST 或 PCI。 Azure 安全性基準測試可提供有效率的方法，讓控制項已經預先對應到這些產業基準測試。

## <a name="terminology"></a>詞彙

「控制」、「基準測試」和「基準」這類詞彙通常會在 Azure 安全性基準測試檔中使用，因此請務必瞭解 Azure 使用這些條款的方式。


| 詞彙 | 描述 | 範例 |
|--|--|--|
| 控制 | 控制項是功能或活動的高階描述，必須加以定址，且不是技術或實行的特定。 | 資料保護是其中一種安全性控制措施。 此控制項包含必須解決的特定動作，以協助確保資料受到保護。 |
| 基準測試 | 基準測試包含特定技術（例如 Azure）的安全性建議。 這些建議會依其所屬的控制項進行分類。 | Azure 安全性基準測試包含適用于 Azure 平臺的安全性建議 |
| 基準 | 基準是在個別 Azure 服務上執行基準測試。 每個組織會決定基準測試建議，而在 Azure 實施範圍中需要對應的設定。 | Contoso 公司應遵循 Azure SQL 安全性基準中建議的設定，來啟用 Azure SQL 安全性功能。

歡迎您提供關於 Azure 安全性基準測試的意見反應！ 我們建議您在下方的意見反應區域中提供意見。 如果您想要在 Azure 安全性基準小組中更私下地共用您的輸入，歡迎您填寫表單 https://aka.ms/AzSecBenchmark
