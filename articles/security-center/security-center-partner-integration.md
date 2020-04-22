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
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758078"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 資訊安全中心整合安全性解決方案
這份文件可協助您管理已連線到 Azure 資訊安全中心的安全性解決方案，並且新增新的項目。

## <a name="integrated-azure-security-solutions"></a>整合式 Azure 安全性解決方案
資訊安全中心可以使得在 Azure 中啟用整合式安全性解決方案變得簡單。 優點包括：

- **簡化部署**：資訊安全中心提供整合式合作夥伴解決方案的精簡佈建。 對於反惡意軟體和漏洞評估等解決方案,安全中心可以在虛擬機上預配代理。 對於防火牆設備,安全中心可以處理所需的大部分網路配置。
- **集成檢測**:合作夥伴解決方案中的安全事件將自動收集、聚合,並作為安全中心警報和事件的一部分顯示。 這些事件也會與來自其他來源的偵測整合，以提供進階的威脅偵測功能。
- **統一的健全狀況監視與管理**：客戶可以使用整合式的健康情況事件，一眼就能監視所有合作夥伴解決方案。 提供基本管理功能，而且可以讓您輕鬆使用夥伴解決方案存取進階設定。

目前,整合安全解決方案包括[Qualys](https://www.qualys.com/public-cloud/#azure)和[Rapid7](https://www.rapid7.com/products/insightvm/)和 Microsoft 應用程式閘道 Web 應用程式防火牆的漏洞評估。

> [!NOTE]
> 安全中心不會在合作夥伴虛擬設備上安裝日誌分析代理,因為大多數安全供應商禁止在其設備上運行外部代理。

要瞭解有關從 Qualys 整合漏洞掃描工具(包括可供標準層客戶使用的內建掃描器)的更多詳細資訊,請參閱: 

- [虛擬機器的整合漏洞掃描器](built-in-vulnerability-assessment.md)。
- [部署合作夥伴漏洞掃描解決方案](partner-vulnerability-assessment.md)。

安全中心還為您的:

* SQL 資料庫 ─請參閱[漏洞評估儀表板中流覽漏洞評估報告](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Azure 容器註冊表映像 ─ 請參考[Azure 容器註冊表與安全中心整合(預覽)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>安全性解決方案如何整合
從資訊安全中心部署的 Azure 安全性解決方案會自動連線。 您還可以連接其他安全數據來源,包括本地或其他雲端中執行的電腦。

[![夥伴解決方案整合](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理整合式 Azure 安全性解決方案和其他資料來源

1. 從[Azure 門戶](https://azure.microsoft.com/features/azure-portal/)開啟**安全中心**。

1. 從安全中心的選單中,選擇**安全解決方案**。

在 **「安全解決方案**」頁中,您可以看到整合 Azure 安全解決方案的運行狀況並執行基本管理任務。

### <a name="connected-solutions"></a>連線的解決方案

**「已連接的解決方案**」部分包括當前連接到安全中心的安全解決方案。 它還顯示每個解決方案的運行狀況。  

![連線的解決方案](./media/security-center-partner-integration/connected-solutions.png)

合作夥伴解決方案的狀態可以是︰

* **健康**(綠色) - 沒有健康問題。
* **不健康**(紅色) - 有一個健康問題,需要立即關注。
* **停止報告**(橙色) - 解決方案已停止報告其運行狀況。
* **未報告**(灰色) - 解決方案尚未報告任何內容,並且沒有可用的健康資料。 如果解決方案最近連接且仍在部署,則其狀態可能未報告。

> [!NOTE]
> 如果健康情況狀態資料無法使用，資訊安全中心就會顯示最後收到之事件的日期和時間，以指出解決方案是否進行報告。 如果沒有可用的運行狀況數據,並且在過去 14 天內未收到警報,則安全中心指示解決方案不正常或不報告。
>
>

選擇 **「檢視」** 以取得其他資訊和選項,例如:

   - **解決方案主控台**- 打開此解決方案的管理經驗。
   - **連結 VM** - 打開連結應用程式頁面。 您可以在這裡將資源連接到合作夥伴解決方案。
   - **移除解決方案**
   - **設定**

   ![合作夥伴解決方案詳細資料](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>探索到的解決方案

安全中心會自動發現在 Azure 中運行但未連接到安全中心的安全解決方案,並在 **「發現的解決方案」** 部分中顯示這些解決方案。 這些解決方案包括 Azure 解決方案,如[Azure AD 標識保護](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)以及合作夥伴解決方案。

> [!NOTE]
> 已探索解決方案功能的訂用帳戶層級需要資訊安全中心的標準層。 請參閱[定價](security-center-pricing.md)以瞭解有關定價層的更多情況。
>

選擇解決方案下的**CONNECT**以與安全中心集成,並通知安全警報。

### <a name="add-data-sources"></a>新增資料來源

[新增資料來源]**** 區段包含可以連線的其他可用資料來源。 如需從這些來源新增資料的指示，請按一下 [新增]****。

![資料來源](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在資訊安全中心中整合夥伴解決方案。 有關相關信息,請參閱以下文章:

* [匯出安全警報與建議](continuous-export.md)。 瞭解如何設置與 Azure Sentinel 或任何其他 SIEM 的整合。
* [資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。