---
title: Microsoft 安全性程式碼分析上架指南
description: 瞭解如何上架及安裝 Microsoft Security Code Analysis 擴充功能。 請參閱必要條件和查看其他資源。
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4fe13c418452a7a88dcd97939d6e853039f3fb64
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517065"
---
# <a name="onboarding-and-installing"></a>上線和安裝

開始使用 Microsoft 安全性程式碼分析的必要條件：

- 符合資格的 Microsoft 統一支援供應專案，如下一節所述。
- Azure DevOps 組織。
- 將延伸模組安裝到 Azure DevOps 組織的許可權。
- 可同步處理至雲端託管 Azure DevOps 管線的原始程式碼。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>上線 Microsoft Security Code 分析延伸模組

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>有興趣購買 Microsoft Security Code 分析延伸模組嗎？

如果您有下列其中一個支援供應專案，請洽詢您的技術客戶經理以購買或交換現有的時數，以取得延伸模組的存取權：

- 整合支援先進層
- 整合支援效能層級
- 適用于開發人員的頂級支援
- 適用于合作夥伴的頂級支援
- 適用于企業的頂級支援

如果您沒有上述支援合約的其中一項，您可以從我們的其中一個合作夥伴購買此延伸模組。

**後續步驟：**

如果您符合上述的資格，請洽詢下列清單中的合作夥伴，以購買 Microsoft Security Code Analysis 延伸模組。 否則，請聯絡 [Microsoft 安全性程式碼分析支援](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)。

>**合作 夥伴：**

- 區域-連絡人詳細資料： cloudsupport@zones.com
- Wortell –連絡人詳細資料： info@wortell.nl
- Logicalis –連絡人詳細資料： logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>成為合作夥伴

Microsoft 安全性程式碼分析小組想要將夥伴與合作夥伴協定的頂級支援上架。 合作夥伴可透過銷售延伸給想要購買的客戶，但沒有 Microsoft 的企業支援合約，來協助 Azure DevOps 客戶更安全地進行開發。 有興趣的合作夥伴可以 [在這裡](http://www.microsoftpartnersupport.com/msrd/opin)註冊。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>安裝 Microsoft 安全性程式碼分析擴充功能

1. 當擴充功能與您的 Azure DevOps 組織共用之後，請移至您的 Azure DevOps 組織頁面。 這類頁面的範例 URL 為 `https://dev.azure.com/contoso` 。
1. 在您的名稱旁邊，選取右上角的 [購物包] 圖示，然後選取 [ **管理延伸** 模組]。
1. 選取 [ **共用** ]。
1. 選取 [Microsoft 安全性程式碼分析] 延伸模組，然後選取 [ **安裝** ]。
1. 從下拉式清單中，選擇要安裝擴充功能的 Azure DevOps 組織。
1. 選取 [安裝]。 安裝完成之後，您就可以開始使用擴充功能。

>[!NOTE]
> 即使您沒有安裝擴充功能的存取權，仍請繼續進行安裝步驟。 您可以在安裝過程中要求 Azure DevOps 組織系統管理員的存取權。

安裝延伸模組之後，就可以看到安全的開發組建工作，並可將其新增至您的 Azure Pipelines。

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>將特定組建工作新增至您的 Azure DevOps 管線

1. 從您的 Azure DevOps 組織開啟您的 team 專案。
1. 選取 **管線**  >  **組建** 。
1. 選取您要在其中新增擴充功能組建工作的管線：
   - 新管線：選取 [ **新增** ]，然後遵循詳述的步驟來建立新的管線。
   - 編輯管線：選取現有的管線，然後選取 [ **編輯** ] 開始編輯管線。
1. 選取 **+** 並移至 [ **新增** 工作] 窗格。
1. 從清單或使用 [搜尋] 方塊，尋找您要新增的組建工作。 選取 [新增]  。
1. 指定工作所需的參數。
1. 將新組建排入佇列。
   >[!NOTE]
   >檔案和資料夾路徑相對於來源存放庫的根目錄。 如果您將輸出檔案和資料夾指定為參數，則會將它們取代為我們在組建代理程式上定義的一般位置。

> [!TIP]
>
> - 若要在組建之後執行分析，請將 Microsoft 安全性程式碼分析組建工作放在組建的 [發行組建成品] 步驟之後。 如此一來，您的組建就可以在執行靜態分析工具之前完成並張貼結果。
> - 當安全開發組建工作 **發生錯誤時** ，一律選取 [繼續]。 即使有一個工具失敗，其他工具也可以執行。 工具之間沒有相互相關性。
> - 只有當工具無法順利執行時，Microsoft 安全性程式碼分析組建工作才會失敗。 但即使工具識別程式碼中的問題，也會成功。 藉由使用後置分析組建工作，您可以將組建設定為在工具識別程式碼中的問題時失敗。
> - 透過發行管線執行時，不支援某些 Azure DevOps 組建工作。 更具體來說，Azure DevOps 不支援從發行管線內發佈成品的工作。
> - 如需 Azure DevOps Team Build 中可指定為參數的預先定義變數清單，請參閱 [Azure DevOps 組建變數](/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)。

## <a name="next-steps"></a>後續步驟

如需設定組建工作的詳細資訊，請參閱我們的設定 [指南](security-code-analysis-customize.md) 或 [YAML 設定指南](yaml-configuration.md)。

如果您有更多有關延伸模組和所提供工具的問題，請參閱我們的 [常見問題頁面](security-code-analysis-faq.md)。