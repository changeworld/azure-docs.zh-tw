---
title: Azure 與 AppSource Marketplace 供應項目
description: 建立和管理 Azure 與 AppSource Marketplace 的供應項目
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278479"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure 與 AppSource Marketplace 供應項目

本節的第一部分將介紹用來建立及管理 Azure 和 AppSource Marketplace 供應項目的一般作業。  此部分會提供您管理特定供應項目類型所需了解的背景，以及所有供應項目類型的常見技術資訊。  本節大部分內容包含有關如何建立和管理特定供應項目類型的詳細指示。  

以下視頻介紹了 Azure 應用商店或 AppSource 中提供的各種功能和不同的產品/服務類型。  其中也涵蓋在這些市集中發佈應用程式或服務的重要技術面與商務面。

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**建置適用於 Azure Marketplace 和 AppSource 的應用程式與服務 - 組建 2018**

如需這些市集的詳細資訊，請參閱 [Azure Marketplace 和 AppSource 發佈指南](../marketplace-publishers-guide.md)。


## <a name="common-offer-operations"></a>一般供應項目作業

建立新供應項目的程序在供應項目類型之間存在顯著的差異，例如在 [Azure 應用程式供應項目](./azure-applications/cpp-azure-app-offer.md)和[諮詢服務供應項目](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md)之間。  相較之下，您在 [Cloud Partner 入口網站](https://cloudpartner.azure.com)中針對供應項目執行的許多其他作業，在供應項目類型之間就相當標準化。  這些常見作業 (包括發佈、檢視狀態、更新和刪除) 均會在[管理供應項目](./manage-offers/cpp-manage-offers.md)一節中加以說明。


## <a name="test-drive"></a>試用產品

*試用產品*是一個市集功能，可為客戶提供每個供應項目的「購買前先試用」示範選項，因此會加以啟用。  測試磁碟機功能僅限於以下產品類型子集[：Azure 應用程式](./azure-applications/cpp-azure-app-offer.md)[、Dynamics 365 業務中心](../cloud-partner-portal-orig/cpp-business-central-offer.md)、[面向客戶參與的動態 365、](./dyn365ce/cpp-customer-engagement-offer.md)[財務和運營動態 365、SaaS](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md)[應用程式](./saas-app/cpp-saas-offer.md)和[虛擬機器](./virtual-machine/cpp-virtual-machine-offer.md)。  此功能會要求發行者建立試用產品範本 (已針對其供應項目自訂)。  如需詳細資訊，請參閱[試用產品](./test-drive/what-is-test-drive.md)一節。

您可以藉由套用[試用產品篩選條件](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)，來瀏覽目前具有試用產品示範的市集供應項目。 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace 和 AppSource 供應項目類型

下表列出 [Cloud Partner 入口網站](https://cloudpartner.azure.com)目前支援的供應項目類型。  此資料表針對每個供應項目類型列出可陳列供應項目的市集，以及供應項目解決方案技術的一般描述。

|                供應項目類型                |  Marketplace  |   描述                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure 應用程式](./azure-applications/cpp-azure-app-offer.md) | Azure | 解決方案由透過 Azure Resource Manager 範本部署的一個或多個虛擬機器 (VM) 及選擇性自訂 Azure 程式碼組成。  部署可以由客戶透過解決方案範本進行，或者由發行者進行管理。 此類型主要用來提供更大的彈性 (與所提供的虛擬機器供應項目類型相比)。  |
| [諮詢服務](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | 兩者皆可 | Microsoft 的合格顧問可以在 Azure Marketplace 或 AppSource 列出他們領域專屬的服務。  他們的專業可協助客戶評估問題，並建立和部署符合客戶營運目標的適當解決方案。  |
| [容器](./containers/cpp-containers-offer.md)  | Azure | 解決方案是佈建成 Kubernetes 型服務或 Azure 容器執行個體的 Docker 容器映像。 |
| [動態 365 商務中心](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | 擴充此企業資源規劃 (ERP) 及企業管理系統的套件。 |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | 通過銷售、服務、專案服務和現場服務模組擴展此客戶資源管理 （CRM） 系統的包  |
| [財務和運營動態 365](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | 擴展此企業資源規劃 （ERP） 服務的套裝軟體，支援高級財務、運營、製造和供應鏈管理 |
| [IoT Edge 模組](./iot-edge-module/cpp-offer-process-parts.md) | Azure | 在 IoT Edge 裝置上執行且與 Docker 相容的容器。  它由使用自訂代碼、其他 Azure 服務和協力廠商服務組合的小型計算模組組成。 |
| [Power BI 應用程式](./power-bi/cpp-power-bi-offer.md) | AppSource | 打包可自訂的 Power BI 內容（包括資料集、報表和儀表板）的 Power BI 應用 |
| [SaaS 應用程式](./saas-app/cpp-saas-offer.md) | Azure | 解決方案是一種軟體即服務訂閱，由發行者管理，使用者通過使用 Azure 活動目錄的自訂介面登錄。 |
| [虛擬機器](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | 解決方案包含在部署至客戶訂用帳戶的單一虛擬機器內。  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

如需詳細資訊，請參閱[各個供應項目類型的發佈指南](../publisher-guide-by-offer-type.md)。


## <a name="next-steps"></a>後續步驟

您將在"[管理優惠](./manage-offers/cpp-manage-offers.md)"一文中瞭解您可以對市場優惠執行的一般操作及其共同的技術屬性和資產。
