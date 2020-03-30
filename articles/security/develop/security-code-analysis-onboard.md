---
title: 微軟安全代碼分析載入指南
description: 本文介紹安裝 Microsoft 安全代碼分析擴展
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6132aab98cc8145cb99cf153c64f20fbac00131c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197000"
---
# <a name="onboarding-and-installing"></a>載入和安裝

開始使用 Microsoft 安全代碼分析的先決條件：

- 符合以下部分的 Microsoft 統一支援產品。
- Azure DevOps 組織。
- 安裝擴展到 Azure DevOps 組織的許可權。
- 可同步到雲託管的 Azure DevOps 管道的原始程式碼。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>加入 Microsoft 安全代碼分析擴展

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>有興趣購買 Microsoft 安全代碼分析擴展？

如果您有以下支援產品之一，請聯繫您的技術客戶經理購買或交換現有時間以訪問擴展：

- 統一支援高級層
- 統一支援績效層
- 對開發人員的卓越支援
- 對合作夥伴的卓越支援
- 企業卓越支援

如果您沒有上述支援協定之一，您可以從我們的合作夥伴之一購買延期。

**後續步驟：**

請從以下清單中聯繫合作夥伴，並要求購買 Microsoft 安全代碼分析擴展。

>**合作 夥伴：**

- 區域 - 連絡方式：cloudsupport@zones.com

### <a name="become-a-partner"></a>成為合作夥伴

Microsoft 安全代碼分析團隊希望通過合作夥伴支援高級協定加入合作夥伴。 合作夥伴將説明 Azure DevOps 客戶通過向希望購買擴展但未與 Microsoft 簽訂企業支援協定的客戶銷售擴展，從而功能更安全地開發。 有興趣的合作夥伴可以[在這裡](http://www.microsoftpartnersupport.com/msrd/opin)註冊。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>安裝 Microsoft 安全代碼分析擴展

1. 與 Azure DevOps 組織共用擴展後，轉到 Azure DevOps 組織頁面。 此類頁面的示例 URL 是`https://dev.azure.com/contoso`。
1. 選擇您姓名旁邊的右上角的購物袋圖示，然後選擇 **"管理擴展**"。
1. 選擇 **"共用**"。
1. 選擇 Microsoft 安全代碼分析副檔名，選擇**安裝**。
1. 從下拉清單中，選擇要安裝擴展的 Azure DevOps 組織。
1. 選取 [安裝]****。 安裝完成後，您可以開始使用擴展。

>[!NOTE]
> 即使您沒有安裝擴展的存取權限，請繼續安裝步驟。 您可以在安裝過程中向 Azure DevOps 組織管理員請求存取權限。

安裝擴展後，安全開發生成任務可見，可用於添加到 Azure 管道中。

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>向 Azure DevOps 管道添加特定的生成任務

1. 從 Azure DevOps 組織中，打開團隊專案。
1. 選擇**管道** > **生成**。
1. 選擇要向其添加擴展生成任務的管道：
   - 新管道：選擇 **"新建"，** 然後按照詳細步驟創建新管道。
   - 編輯管道：選擇現有管道，然後選擇 **"編輯"** 以開始編輯管道。
1. 選擇**+** 並轉到"**添加任務"** 窗格。
1. 從清單中或使用搜索框查找要添加的生成任務。 選取 [加入]****。
1. 指定任務所需的參數。
1. 將新組建排入佇列。
   >[!NOTE]
   >檔和資料夾路徑與源存儲庫的根目錄相關。 如果將輸出檔案和資料夾指定為參數，則它們將被替換到我們在生成代理上定義的公共位置。

> [!TIP]
>
> - 要在生成後運行分析，請將 Microsoft 安全代碼分析生成任務放在生成發佈的生成專案步驟之後。 這樣，您的生成就可以在運行靜態分析工具之前完成併發布結果。
> - 始終為安全開發生成任務選擇 **"出錯時繼續**"。 即使一個工具出現故障，其他工具也可以運行。 工具之間沒有相互依賴性。
> - 僅當工具無法成功運行時，Microsoft 安全代碼分析生成任務才會失敗。 但是，即使工具識別代碼中的問題，它們也會成功。 通過使用"分析後"生成任務，可以將組建組態為在工具識別代碼中的問題時失敗。
> - 通過發佈管道運行時，不支援某些 Azure DevOps 生成任務。 更具體地說，Azure DevOps 不支援在發佈管道中發佈專案的任務。
> - 有關可以指定為參數的 Azure DevOps 團隊生成中預定義變數的清單，請參閱[Azure DevOps 生成變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)。

## <a name="next-steps"></a>後續步驟

有關配置生成任務的詳細資訊，請參閱我們的[配置指南](security-code-analysis-customize.md)或[YAML 配置指南](yaml-configuration.md)。

如果您對擴展和提供的工具有任何疑問，請查看我們的[常見問題頁面](security-code-analysis-faq.md)。
