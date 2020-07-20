---
title: 如何自行診斷並解決 Azure 春季雲端中的問題
description: 瞭解如何在 Azure 春季雲端中自我診斷並解決問題。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.openlocfilehash: 581d77a5568b9533d85ff476b63001c8a237bc4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84457196"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>如何自行診斷並解決 Azure 春季雲端中的問題
Azure 春季雲端診斷是一種互動式體驗，可協助您針對應用程式進行疑難排解。 不需要組態。 當您發現問題時，Azure 春季雲端診斷會指出錯誤的原因，並引導您取得有助於疑難排解及解決問題的資訊。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 已部署的 Azure Spring Cloud 服務執行個體。 請遵循[關於如何透過 Azure CLI 來部署應用程式的快速入門](spring-cloud-quickstart-launch-app-cli.md)來開始使用。
* 服務執行個體中至少已建立一個應用程式。

## <a name="navigate-to-the-diagnostics-page"></a>流覽至 [診斷] 頁面
1. 登入 Azure 入口網站。
2. 移至 Azure Spring Cloud 的 [概觀]**** 頁面。
3. 在頁面左側的功能表中，開啟 [**診斷並解決問題**]。

 ![診斷，解決對話方塊](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>搜尋記錄的問題
若要找出問題，您可以輸入關鍵字來進行搜尋，或按一下 [方案群組] 以探索該類別中的所有內容。

 ![搜尋問題](media/spring-cloud-diagnose/search-detectors.png)

選取 [設定**伺服器健康情況檢查**]、[設定**伺服器健全狀況狀態**] 或 [設定**伺服器更新歷程記錄**] 將會顯示各種結果。

![問題選項](media/spring-cloud-diagnose/detectors-options.png)

尋找您的目標偵測器，然後按一下以執行。 執行偵測器之後，將會顯示診斷的摘要。 您可以選取 [**查看完整報表**] 來檢查診斷詳細資料，或按一下 [**顯示磚] 功能表**按鈕以返回 [偵測器清單]。

 ![摘要診斷](media/spring-cloud-diagnose/summary-diagnostics.png)

在 [診斷詳細資料] 頁面中，您可以在右上角將診斷時間範圍變更為控制器。 若要查看更多計量或記錄，請切換每個診斷。 計量和記錄檔可能會有15分鐘的延遲。

 ![診斷詳細資料](media/spring-cloud-diagnose/diagnostics-details.png)

某些結果包含相關的檔。

 ![相關詳細資料](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>後續步驟
* [使用警示和動作群組監視 Spring 雲端資源](spring-cloud-tutorial-alerts-action-groups.md)
* [Azure Spring Cloud 服務的安全性控制](spring-cloud-concept-security-controls.md)