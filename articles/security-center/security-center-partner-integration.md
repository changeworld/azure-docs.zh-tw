---
title: 在 Azure 資訊安全中心整合安全性解決方案 | Microsoft Docs
description: 了解 Azure 資訊安全中心如何與夥伴整合，以提高您 Azure 資源的整體安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758078"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 資訊安全中心整合安全性解決方案
這份文件可協助您管理已連線到 Azure 資訊安全中心的安全性解決方案，並且新增新的項目。

## <a name="integrated-azure-security-solutions"></a>整合式 Azure 安全性解決方案
資訊安全中心可以使得在 Azure 中啟用整合式安全性解決方案變得簡單。 好處包括：

- **簡化部署**：資訊安全中心提供整合式合作夥伴解決方案的精簡佈建。 針對反惡意程式碼和弱點評估之類的解決方案，資訊安全中心可以在您的虛擬機器上布建代理程式。 對於防火牆設備，資訊安全中心可以處理所需的許多網路設定。
- **整合**式偵測：來自合作夥伴解決方案的安全性事件會自動收集、匯總，並顯示為資訊安全中心警示和事件的一部分。 這些事件也會與來自其他來源的偵測整合，以提供進階的威脅偵測功能。
- **統一的健全狀況監視與管理**：客戶可以使用整合式的健康情況事件，一眼就能監視所有合作夥伴解決方案。 提供基本管理功能，而且可以讓您輕鬆使用夥伴解決方案存取進階設定。

目前，整合式安全性解決方案包含[Qualys](https://www.qualys.com/public-cloud/#azure)和[Rapid7](https://www.rapid7.com/products/insightvm/)和 Microsoft 應用程式閘道 Web 應用程式防火牆的弱點評量。

> [!NOTE]
> 資訊安全中心不會在合作夥伴虛擬裝置上安裝 Log Analytics 代理程式，因為大部分的安全性廠商會禁止在其應用裝置上執行的外部代理程式。

若要深入瞭解 Qualys 的弱點掃描工具整合，包括可供標準層客戶使用的內建掃描器，請參閱： 

- [虛擬機器的整合式弱點掃描器](built-in-vulnerability-assessment.md)。
- [部署合作夥伴弱點掃描解決方案](partner-vulnerability-assessment.md)。

資訊安全中心也會為您提供弱點分析：

* SQL 資料庫 - 請參閱[探索弱點評量儀表板中的弱點評量報告](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Azure Container Registry 映射-請參閱[與資訊安全中心的 Azure Container Registry 整合（預覽）](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>安全性解決方案如何整合
從資訊安全中心部署的 Azure 安全性解決方案會自動連線。 您也可以連接其他安全性資料來源，包括內部部署或其他雲端中執行的電腦。

[![夥伴解決方案整合](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理整合式 Azure 安全性解決方案和其他資料來源

1. 從[Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)開啟**資訊安全中心**。

1. 從資訊安全中心的功能表中，選取 [**安全性解決方案**]。

在 [**安全性解決方案**] 頁面中，您可以看到整合式 Azure 安全性解決方案的健康情況，並執行基本管理工作。

### <a name="connected-solutions"></a>連線的解決方案

[**已連接的解決方案**] 區段包含目前連接到資訊安全中心的安全性解決方案。 它也會顯示每個解決方案的健全狀況狀態。  

![連線的解決方案](./media/security-center-partner-integration/connected-solutions.png)

合作夥伴解決方案的狀態可以是︰

* 狀況**良好**（綠色）-沒有健康情況問題。
* 狀況**不良**（紅色）-有一個需要立即注意的健康情況問題。
* **已停止報告**（橙色）-解決方案已停止報告其健康狀態。
* **未報告**（灰色）-解決方案尚未報告任何專案，且沒有可用的健康情況資料。 如果解決方案的狀態是最近連線且仍在部署中，則可能未報告。

> [!NOTE]
> 如果健康情況狀態資料無法使用，資訊安全中心就會顯示最後收到之事件的日期和時間，以指出解決方案是否進行報告。 如果沒有可用的健康情況資料，而且在過去14天內未收到任何警示，資訊安全中心會指出解決方案狀況不良或未報告。
>
>

選取 [ **VIEW** ] 取得其他資訊和選項，例如：

   - **解決方案主控台**-開啟此解決方案的管理體驗。
   - **連結 VM** -開啟 [連結應用程式] 頁面。 您可以在這裡將資源連接到合作夥伴解決方案。
   - **刪除解決方案**
   - **設定**

   ![合作夥伴解決方案詳細資料](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>探索到的解決方案

資訊安全中心會自動探索在 Azure 中執行但未連線到資訊安全中心的安全性解決方案，並在 [探索到的**解決方案**] 區段中顯示解決方案。 這些解決方案包括 Azure 解決方案，例如[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)和合作夥伴解決方案。

> [!NOTE]
> 已探索解決方案功能的訂用帳戶層級需要資訊安全中心的標準層。 若要深入瞭解定價層，請參閱[定價](security-center-pricing.md)。
>

選取解決方案下的 **[連線]** 以與資訊安全中心整合，並收到安全性警示的通知。

### <a name="add-data-sources"></a>新增資料來源

[新增資料來源]**** 區段包含可以連線的其他可用資料來源。 如需從這些來源新增資料的指示，請按一下 [新增]****。

![資料來源](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在資訊安全中心中整合夥伴解決方案。 如需相關資訊，請參閱下列文章：

* [匯出安全性警示和建議](continuous-export.md)。 瞭解如何設定 Azure Sentinel 或任何其他 SIEM 的整合。
* [資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。