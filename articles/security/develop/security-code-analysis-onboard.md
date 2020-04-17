---
title: 微軟安全程式分析載入指南
description: 本文介紹安裝 Microsoft 安全程式分析擴展
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3ef111817b6351277f975b9b7e454f9a89982451
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460200"
---
# <a name="onboarding-and-installing"></a>載入及安裝

開始使用 Microsoft 安全代碼分析的先決條件:

- 符合以下部分的 Microsoft 統一支援產品。
- Azure DevOps 組織。
- 安裝擴展到 Azure DevOps 組織的許可權。
- 可同步到雲端託管的 Azure DevOps 管道的原始程式碼。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>加入 Microsoft 安全程式分析擴充

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>有興趣購買 Microsoft 安全代碼分析擴展?

如果您有以下支援產品之一,請聯絡您的技術客戶經理購買或交換現有時間以存取延伸:

- 統一支援進階層
- 統一支援績效層
- 開發開發者的支援
- 對合作夥伴的卓越支援
- 企業卓越支援

如果您沒有上述支持協定之一,您可以從我們的合作夥伴之一購買延期。

**後續步驟:**

請從以下清單中聯繫合作夥伴,並要求購買 Microsoft 安全代碼分析擴展。

>**合作 夥伴:**

- 區域 - 連絡方式:cloudsupport@zones.com
- 沃特爾 – 聯繫方式:info@wortell.nl

### <a name="become-a-partner"></a>成為合作夥伴

Microsoft 安全代碼分析團隊希望透過合作夥伴支援進階協定加入合作夥伴。 合作夥伴將説明 Azure DevOps 客戶透過向希望購買擴展但未與 Microsoft 簽訂企業支援協定的客戶銷售擴展,從而功能更安全地開發。 有興趣的合作夥伴可以[在這裡](http://www.microsoftpartnersupport.com/msrd/opin)註冊。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>安裝 Microsoft 安全程式分析擴充

1. 與 Azure DevOps 組織共用擴展後,轉到 Azure DevOps 組織頁面。 此頁面的範例網址`https://dev.azure.com/contoso`是 。
1. 選擇您姓名旁邊的右上角的購物袋圖示,然後選擇 **「管理擴展**」 。
1. 選擇 **"共用**"。
1. 選擇 Microsoft 安全程式分析副檔名,選擇**安裝**。
1. 從下拉清單中,選擇要安裝擴展的 Azure DevOps 組織。
1. 選取 [安裝]  。 安裝完成後,您可以開始使用擴展。

>[!NOTE]
> 即使您沒有安裝擴展的訪問許可權,請繼續安裝步驟。 您可以在安裝過程中向 Azure DevOps 組織管理員請求訪問許可權。

安裝擴展後,安全開發生成任務可見,可用於添加到 Azure 管道中。

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>向 Azure DevOps 管道新增特定的產生任務

1. 從 Azure DevOps 組織中,打開團隊專案。
1. 選擇**導管** > **產生**。
1. 選擇要新增擴充產生工作的管道:
   - 新管道:選擇 **「新建」 然後**按照詳細步驟創建新管道。
   - 編輯導管:選擇現有管道,然後選擇 **「編輯」** 以開始編輯管道。
1. 選擇**+** 並轉到「**新增任務」** 窗格。
1. 從清單中或使用搜尋框尋找要添加的產生任務。 選取 [新增]  。
1. 指定任務所需的參數。
1. 將新組建排入佇列。
   >[!NOTE]
   >檔案和資料夾路徑與源存儲庫的根目錄相關。 如果將輸出檔和資料夾指定為參數,則它們將被替換到我們在生成代理上定義的公共位置。

> [!TIP]
>
> - 要在生成後運行分析,請將Microsoft安全代碼分析生成任務放在生成發佈的生成專案步驟之後。 這樣,您的生成就可以在運行靜態分析工具之前完成併發佈結果。
> - 始終為安全開發生成任務選擇 **「出錯時繼續**」。 即使一個工具出現故障,其他工具也可以運行。 工具之間沒有相互依賴性。
> - 僅當工具無法成功運行時,Microsoft 安全代碼分析生成任務才會失敗。 但是,即使工具識別代碼中的問題,它們也會成功。 通過使用"分析後"生成任務,可以將生成配置為在工具識別代碼中的問題時失敗。
> - 通過發佈管道運行時,不支援某些 Azure DevOps 生成任務。 更具體地說,Azure DevOps 不支援在發佈管道中發佈專案的任務。
> - 有關可以指定為參數的 Azure DevOps 團隊產生中預定義變數的清單,請參閱[Azure DevOps 產生變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)。

## <a name="next-steps"></a>後續步驟

有關設定產生工作的詳細資訊,請參閱我們的[設定指南](security-code-analysis-customize.md)或[YAML 設定指南](yaml-configuration.md)。

如果您對擴展和提供的工具有任何疑問,請查看我們的[常見問題頁面](security-code-analysis-faq.md)。
