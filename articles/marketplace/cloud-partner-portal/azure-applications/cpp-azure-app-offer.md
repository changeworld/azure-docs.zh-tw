---
title: Azure 應用程式產品 / |Azure 應用商店
description: 在 Azure Marketplace 上發佈 Azure 應用程式供應項目的程序概觀。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285311"
---
# <a name="azure-application-offer"></a>Azure 應用程式供應項目

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> 本節說明如何將新的 Azure 應用程式供應項目發佈到 [Azure Marketplace](https://azuremarketplace.microsoft.com)。  每個 Azure 應用程式都包含定義應用程式所使用之所有技術資產的 Azure Resource Manager 範本，這通常會包含一部或多部虛擬機器，以及其他支援的 Azure 或 Web 型服務。 所有 Azure 應用程式供應項目均須透過 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) 啟用存取安全性。  </div> | ![Azure 應用程式圖示](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>發佈概觀

以下的[建置 Azure Marketplace 解決方案範本和受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603)這支影片會介紹下列內容：可用的供應項目類型、需要的技術資產、撰寫 Azure Resource Manager 範本的方式、應用程式 UI 的部署及測試、應用程式供應項目發佈方式，以及應用程式檢閱流程。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Azure 應用程式類型

Azure 應用程式有兩種：受控應用程式和解決方案範本。 

- 解決方案範本是在 Marketplace 中發佈解決方案的其中一個主要方法。 當您的解決方案除了單一虛擬機器 (VM) 之外，還需要以自動化方式進行額外的部署和設定時，可以使用此供應項目類型。 您可以使用解決方案範本，自動提供多部 VM。 此自動化包括佈建網路和儲存體資源以提供複雜的 IaaS 解決方案。 如需解決方案範本需求和計費模型的概觀，請參閱 [Azure 應用程式：解決方案範本](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)。

- 受控應用程式與解決方案範本類似，只有一個主要差異。 在受控應用程式中，資源會部署到應用程式發行者所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但發行者租用戶中的身分識別可以存取資源群組。 身為發行者，您可以指定解決方案持續支援的成本。 使用 Azure 受控應用程式，輕鬆建置完全受控的周全應用程式並提供給您的客戶。

除了 Azure Marketplace，您也可以提供服務類別目錄中的受控應用程式。 服務類別目錄是組織中使用者適用的已核准解決方案的內部目錄。 您會使用目錄來符合組織標準，同時為組織中的群組提供解決方案。 員工會使用目錄輕鬆尋找其 IT 部門所建議和核准的應用程式。

>[!Note]
>雲解決方案供應商 （CSP） 合作夥伴管道加入宣告現已可用。  有關通過 Microsoft CSP 合作夥伴管道行銷產品/服務的更多資訊[，請參閱雲解決方案供應商](../../cloud-solution-providers.md)。

如需受控應用程式的優點與類型詳細資訊，請參閱 [Azure 受控應用程式概觀](https://docs.microsoft.com/azure/managed-applications/overview)。


## <a name="publishing-process-workflow"></a>發佈程序工作流程

下圖顯示發佈 Azure 應用程式供應項目的概略程序。

![發佈供應項目的工作流程](./media/new-offer-process.png)

發佈 Azure 應用程式供應項目的概略步驟如下：

1. 符合[必要條件](./cpp-prerequisites.md) - (未顯示) 確認您已符合將 Azure 應用程式發佈到 Azure Marketplace 的商業和技術需求。 

1. [創建產品/服務](./cpp-create-offer.md)- 提供有關產品/服務的詳細資訊。 這些資訊包括：供應項目說明、行銷資料、支援資訊，以及資產規格。

1. [建立或收集現有的商業及技術資產](./cpp-create-technical-assets.md) - 建立相關解決方案的商業資產 (法律聲明文件和行銷資料) 及技術資產。

1. [創建 SKU](./cpp-skus-tab.md) - 創建與產品/服務關聯的 SKU。 您需要針對預計發佈的每個映像提供個別的 SKU。

1. 認證並[公佈報價](./cpp-publish-offer.md)- 報價和技術資產完成後，您可以提交報價。 提交作業將啟動發佈程序。 在此程序中，解決方案會經過測試、驗證、認證，然後在 Azure Marketplace「上線」。

## <a name="next-steps"></a>後續步驟

考慮進行這些步驟之前，您必須先符合將受控應用程式發佈至 Microsoft Azure Marketplace 的[技術和商務需求](./cpp-prerequisites.md)。
