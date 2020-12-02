---
title: 建立商業就緒的 Azure 遠端轉譯應用程式
description: 使用 Azure 遠端轉譯建立商業就緒應用程式的策略和考量
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 7258b37c9d92f25227eac678dde77963e644e64b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483312"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>教學課程：建立商業就緒的 Azure 遠端轉譯應用程式

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 商業應用程式的工作階段管理
> * 追蹤計費工作階段
> * 針對工作階段載入時間最佳化使用者體驗
> * 網路延遲方面的考量

## <a name="prerequisites"></a>必要條件

* 本教學課程的建置基礎為[教學課程：保護 Azure 遠端轉譯和模型儲存體](../security/security.md)。

## <a name="introduction-to-commercial-readiness"></a>商業就緒簡介

Azure 遠端轉譯可擴充混合實境的可能性。 將基本概念整合到您的解決方案之後，還有一些額外的考量可確保您的解決方案安全、可調整且準備好實現價值。

本課程模組將為您介紹商業應用程式可能需要考量的一些其他功能。

如需全系統架構最佳做法的廣泛概觀，請造訪：

* [Azure Architecture Center](/azure/architecture/)
* [Azure 開發人員快速入門指南](../../../../guides/developer/azure-developer-guide.md)

## <a name="analytics"></a>分析

整合分析工具可協助您管理、追蹤及改善解決方案。

如需您可以使用的分析資源完整清單，請造訪：

* [Azure 分析服務](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>追蹤計費的使用量

追蹤多個內部小組或外部用戶端的 Azure 遠端轉譯耗用量會成為重要的考慮，尤其是在多租用戶的情況下。

為了達成此目的，Azure 提供稱為資源標記的服務，這會將 Azure 遠端轉譯服務的耗用量與每個用戶端建立關聯。

如需資源命名與標記的詳細資訊，最好從下列位置開始：

* [資源命名與標記決策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

### <a name="diagnostics"></a>診斷

Windows 事件追蹤 (ETW) 和事件追蹤記錄 (ETL) 等功能強大的工具，可讓您輕鬆地在應用程式內產生追蹤事件，並有助於診斷商業解決方案部署中可能發生的網路、內容內嵌、工作階段、應用程式和其他問題。

如需詳細資訊，請瀏覽：

* [建立用戶端效能追蹤](../../../how-tos/performance-tracing.md)
* [如何收集 Windows 事件追蹤 (ETW) 資料](/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [使用 Windows 裝置入口網站：記錄](/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>使用量分析

Azure Application Insights 可協助您了解使用者如何使用您的 Azure 遠端轉譯應用程式。 每次您更新應用程式時，都可以評估它適用於使用者的程度，並據以加強您的解決方案。 您可以透過了解這些來制定下一個開發週期的相關資料導向決策。

如需詳細資訊，請瀏覽：

* [使用 Application Insights 進行使用量分析](../../../../azure-monitor/app/usage-overview.md)

## <a name="fast-startup-time-strategies"></a>快速啟動時間策略

您的使用案例可能需要從應用程式啟動快速啟動至 3D 模型檢視。 例如，在重要的會議期間，必須讓所有項目預先啟動並執行。 另一個範例則是在 CAD 3D 模型檢閱期間，在 CAD 應用程式與混合實境之間進行快速設計反覆運算對於效率至關重要。

Azure 遠端轉譯需要前置處理過的 3D 模型，而 Azure 目前需要幾分鐘的時間來建立工作階段並載入模型以進行轉譯。 盡可能順暢且快速地進行此程序，需要預先準備 3D 模型資料和 ARR 工作階段。

此處所分享的建議目前並非標準 Azure 遠端轉譯的一部分，但您可以自行實作，以加快啟動時間。

### <a name="initiate-early"></a>提早起始

為了縮短啟動時間，最簡單的解決方案是在使用者工作流程中盡快移動工作階段的建立和初始化。 其中一個策略，是在得知需要 ARR 工作階段時，盡快將工作階段初始化。 這通常是使用者開始將 3D 模型上傳至 Azure Blob 儲存體來與 Azure 遠端轉譯搭配使用時。 在此情況下，可在 3D 模型上傳時起始工作階段建立工作階段初始化，讓這兩個工作流程平行執行。

藉由確保所選的 Azure Blob 儲存體輸入和輸出容器都與 Azure 遠端轉譯工作階段位於相同的區域資料中心，可進一步簡化此程序。

### <a name="scheduling"></a>排程

如果您知道未來需要進行 Azure 遠端轉譯，則可以排程特定的日期和時間來啟動 Azure 遠端轉譯工作階段。

此選項可透過入口網站提供，讓使用者可以上傳 3D 模型，並排程時間在未來進行檢視。 這也是要求其他喜好設定 (例如[「標準」](../../../reference/vm-sizes.md)或[「進階」](../../../reference/vm-sizes.md)轉譯) 的絕佳位置。 如果需要顯示各種資產的組合 (其中的理想大小難以自動判斷)，或需要確保 Azure 區域在指定時間內有可用的 VM，則可能適合「進階」轉譯。

### <a name="session-pooling"></a>工作階段集區

在最嚴苛的情況下，另一個選項是「工作階段集區」，其中一或多個工作階段會隨時建立並初始化。 這會建立一個工作階段集區，以供要求的使用者立即使用。 這種方法的缺點是，一旦 VM 初始化之後，就會開始服務的計費。 讓工作階段集區隨時保持執行中可能不符合成本效益，但根據分析，可能可以預測尖峰負載，或可與上面的排程策略結合，從而預測何時需要工作階段，並據以相應增加和減少工作階段集區。

這項策略也有助於在「標準」和「進階」工作階段之間以更動態的方式進行最佳化選擇，因為在單一使用者工作階段內切換這兩種類型的速度會大幅提升，例如先檢視「進階」複雜度模型，然後進行可在「標準」複雜度中工作的案例。 如果這些使用者工作階段非常冗長，則可以節省大量成本。

如需 Azure 遠端轉譯工作階段的詳細資訊，請參閱：

* [遠端轉譯工作階段](../../../concepts/sessions.md)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>標準與進階伺服器大小路由策略

需要選取是否要建立「標準」或「進階」伺服器大小，會在設計您的使用者體驗和端對端系統時帶來挑戰。 雖然可以選擇只使用「進階」工作階段，但「標準」工作階段會大幅減少使用 Azure 計算資源，且成本會低於「進階」工作階段。 這讓您盡可能在必要的情況下使用「標準」工作階段，且只在需要時才使用「進階」工作階段。

在這裡，我們會分享數個選項 (從最低到最高程度的全面性)，以解決管理工作階段選擇的需求。

### <a name="use-only-standard-or-premium"></a>僅使用標準或進階

如果您確定您的需求「一律」低於「標準」和「進階」之間的臨界值，則這會大幅簡化您的決策。 只使用「標準」。 但請記住，如果已載入資產的複雜度總和總計因過於複雜無法用於「標準」工作階段而遭到拒絕，那麼對使用者體驗的影響就至關重要。

同樣地，如果您預期大部分的使用情況超出「標準」與「進階」之間的臨界值，或成本並非您使用案例中的關鍵因素，則一律選擇「進階」也是使其保持簡單的選項。

### <a name="ask-the-user"></a>詢問使用者

如果您想要同時支援「標準」與「進階」，則判斷要具現化的工作階段類型最簡單的方式，就是在使用者選取要檢視的 3D 資產時加以詢問。 這種方法的挑戰在於，使用者必須了解 3D 資產的複雜性，或甚至是多個將要檢視的資產。 一般來說，基於這個原因，不建議這樣做。 如果使用者選取錯誤而選擇「標準」，則產生的使用者體驗可能會在不適當的時刻遭到入侵。

### <a name="analyze-the-3d-model"></a>分析 3D 模型

另一個相對簡單的方法，就是分析所選 3D 資產的複雜性。 如果「標準」的模型複雜度低於臨界值，則起始「標準」工作階段，否則起始「進階」工作階段。 這裡的挑戰是，單一工作階段最後可能會用來檢視多個模型，其中有些可能會超過「標準」的複雜性臨界值，而導致無法針對一系列不同的 3D 資產順暢地使用相同的工作階段。

### <a name="automatic-switching"></a>自動切換

在「標準」和「進階」工作階段之間自動切換，對於可能同時包含工作階段集區的系統設計而言可能會有很大的意義。 這項策略可讓您進一步最佳化資源使用率。 當使用者載入模型進行檢視時，會判斷複雜度，並從工作階段集區服務要求正確的工作階段大小。

## <a name="working-with-networks"></a>使用網路

### <a name="diagnostics"></a>診斷

Azure 遠端轉譯需要具有低延遲的快速網際網路連線。 使用者網路的品質可能會對於體驗的品質產生顯著的影響。 假設您的用戶端可能有不同的網路設定，而網路延遲偶爾不佳，則診斷工具就是關鍵。  

為確保您可以提供持續高品質的體驗，建議您將伺服器端與用戶端分析工具整合到您的 Azure 遠端轉譯應用程式內。 這麼做會將您所需的資訊帶給您，以診斷及減輕用戶端可能會遇到的任何網路問題。

### <a name="client-network-configurations"></a>用戶端網路設定

開發部署到各種企業環境內的強固共同作業解決方案，其中一項最大的挑戰，就是為您用戶端可能使用的不同網路拓撲和企業防火牆設定做好準備。

許多企業會封鎖 LAN 內的所有點對點流量。 這會讓您難以利用自動 LAN 探索簡單且簡化的 UX，在所有已探索到的混合實境應用程式執行個體之間建立本機共用工作階段。

其他潛在的失敗點是設定為故意節流頻寬的路由器，以及封鎖大部分 TCP/IP 連接埠的防火牆。

當您打算在不熟悉的網路上使用 Azure 遠端轉譯時，我們建議下列項目：

* 提供會議前的檢查清單，以評估網路的就緒程度。
* 確定適當的區域資料中心可以服務要求。
* 讓您有足夠的時間可診斷任何問題。
* 帶入具有高頻寬資料方案的行動作用點做為備份。

### <a name="end-to-end-bandwidth"></a>端對端頻寬

請務必評估 Azure 遠端轉譯 VM 與終端用戶端之間可能存在的每個網路階段頻寬功能。 請記住，從 Azure 資料中心到用戶端 ISP 的網路區段，其限制因素可能會較從 ISP 到用戶端更多。 您可以使用 Blob 下載速度測試來協助診斷這類問題。

### <a name="bandwidth-competition"></a>頻寬競賽

在設計您的混合實境應用程式時，請記住，應用程式的不同功能可能會與 Azure 遠端轉譯競爭頻寬。 最可能的非預期範例，就是當單一房間中的許多參與者都預期同時使用 ARR 來檢視 3D 資產時。 網路資料流程的每個階段都必須具有容量，才能傳輸合併所有 ARR 資料流的總和總計。

其他範例則包括串流處理的影片、同步背景上傳其他相關的內容以及語音聊天，特別是其中有許多參與者且系統是使用分散式點對點方法 (相對於中間方法的音訊混合伺服器)。

如需網路分析的詳細資訊，請檢視：

* [Azure 儲存體 Blob 下載速度測試](https://www.azurespeed.com/Azure/Download)
* [Azure 網路來回行程延遲統計資料](../../../../networking/azure-network-latency.md)
* [伺服器端效能追蹤](../../../overview/features/performance-queries.md)
* [用戶端效能追蹤](../../../how-tos/performance-tracing.md)

## <a name="collaboration-considerations"></a>共同作業考量

Azure 遠端轉譯最有價值的用途，包括多個參與者在同一時間檢視相同 3D 體驗的共同作業。 在這些共用的工作階段中，請務必了解每個參與者都需要唯一的 Azure 遠端轉譯工作階段，不論其是否位於相同網路上的相同位置。

這是因為每個參與者實際上都會看到來自不同有利點的相同體驗，這需要同時從每個檢視方塊轉譯相同的 3D 資產。

### <a name="multiple-azure-remote-rendering-sessions"></a>多個 Azure 遠端轉譯工作階段

如果您想要支援 Azure 遠端轉譯的共用體驗，您所放置來建立及管理 ARR 工作階段的系統將需要準備好起始多個工作階段。 如果參與者的地理位置分散，則這些工作階段可能需要在不同的 Azure 資料中心進行初始化。

您的系統也必須管理一或多個參與者可能位於目前不受 Azure 遠端轉譯支援的地理區域中，或目前沒有可用的 Azure 遠端轉譯 VM 執行個體。

與工作階段集區和本文件中所討論的其他策略合併時，可進一步簡化管理多個同步工作階段。

### <a name="azure-blob-storage-considerations"></a>Azure Blob 儲存體考量

所有同步的 ARR 工作階段都可以參考相同的 SAS URI，以便檢視已轉換的模型。 如此一來，就可以上傳及轉換所需的 3D 資產一次，然後在所有工作階段之間加以共用。 特別是當參與者已共置並使用相同的資料中心時，與 Azure 遠端轉譯伺服器和使用者位於不同資料中心的 Azure Blob 儲存體不會有任何效能方面的考量。

如果 3D 資產通常是針對單一的檢視工作階段上傳然後加以捨棄 (例如在設計檢閱工作階段中)，則相對於 Azure 遠端轉譯伺服器的 Azure Blob 儲存體地理區域也較不重要。

不過，對於將重複使用的 3D 資產 (例如在定型使用案例中)，建議在您打算要使用 Azure 遠端轉譯的每個區域資料中心 Blob 儲存體中，保留已準備好要使用的 3D 資產。 這可以使用 Azure 儲存體備援來進行自動化。 CDN 通常也會用於此目的，但 Azure 遠端轉譯尚未提供這個選項。

其他資訊：

* [混合實境中的共用體驗](/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure 儲存體備援](../../../../storage/common/storage-redundancy.md)

## <a name="managing-model-access"></a>管理模型存取

若要充分運用 Azure 遠端轉譯，必須仔細考慮用於管理 3D 模型的端對端基礎結構。

使用 Azure 遠端轉譯的優點是，永遠不需要將大型 3D 資產直接傳輸到混合實境裝置，就能進行檢視。  此外，一旦將 3D 資產上傳並轉換成與 Azure 遠端轉譯搭配使用，則任何使用者數目都可以共用該 3D 模型的單一執行個體。

### <a name="considerations-for-3d-model-access"></a>3D 模型存取的考量

以下是決定模型存取策略時的一些重要考量。

根據預期的使用案例，決定允許使用者選取 3D 資產以進行檢視的最佳位置或位置組合。 一些常見的選項包括：

* 直接在混合實境體驗內
* 透過隨附的入口網站
* 在隨附的桌面或行動應用程式中

如果您的使用案例包含可多次上傳相同 3D 資產的使用模式，則後端會追蹤哪些模型已轉換可與 ARR 搭配使用，讓模型只預先處理一次即適用於多個未來選取項目。 設計檢閱範例可供小組存取通用原始 3D 資產。 每個小組成員都應該在其工作流程中的某個時間點，使用 ARR 來檢閱模型。 只有第一個視圖會觸發前置處理步驟。 後續的視圖會在 SAS 輸出容器中查詢相關聯的後續處理檔案。

視使用案例而定，您可能需要針對每個 3D 資產或將在同一個工作階段中一起檢視的資產群組，決定並可能保存正確的 Azure 遠端轉譯伺服器大小 (「標準」或「進階」)。  

### <a name="on-device-model-selection-list"></a>裝置上的模型挑選清單

在許多使用案例中，例如定型、工作指引或行銷應用程式，在 Azure 遠端轉譯中經常檢視的一組 3D 資產可能相當靜態。 在這些情況下，可以預先轉換一組策劃的 3D 資產，並透過包含必要資訊的資料庫提供，以填入策劃資產的選取項目清單。 然後，您可以從混合實境應用程式中取出這項資料，以填入選取項目功能表。

這也可以藉由提供一種方式來上傳私人 3D 資產 (對於每個個別或群組都是唯一的) 以進一步進行。 接著，該私人資產清單會與使用者體驗中的通用策劃資產清單結合，以供挑選3D 資產進行檢視。

### <a name="on-device-onedrive-access"></a>裝置上的 OneDrive 存取

假設 OneDrive 檔案選擇器是以原生方式建置於 Microsoft 的混合實境裝置內，那麼從 OneDrive 選取裝置上的 3D 資產很具有吸引力，特別是針對載入不同或經修改 3D 模型的常見使用案例。 在此案例中，使用者會透過混合實境應用程式內的 OneDrive 檔案選擇器，來選取一或多個 3D 資產。 然後，3D 資產會遷移至 SAS 輸入容器、轉換成 SAS 輸出容器，並附加至 ARR 工作階段。 在理想的情況下，混合實境應用程式會叫用雲端型程序來執行這些步驟，而非將所有的位元從 OneDrive 移至裝置，然後再移回 Azure Blob 儲存體。

您可以保存先前已檢視 3D 資產之間的關聯，讓您再次從 OneDrive 選擇相同的模型時，應用程式可以略過轉換程序，並透過其 SAS URI 直接載入相關聯的已轉換 3D 資產，藉此採取更進一步的步驟。

其他資訊：

* [適用於 OneDrive 進行 Azure 儲存體複寫的 Microsoft Power Automate 範本](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [OneDrive 檔案儲存體 API 概觀](/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>直接 CAD 存取

混合實境有一個引人注目的使用案例，就是 CAD 工作進行中的設計檢閱。 在此案例中，關鍵在於從桌面到混合實境的最快載入時間。 理想的解決方案可能包含開發特定 CAD 應用程式的外掛程式。 這些外掛程式會直接管理負載、轉換及檢視程序的每個層面：

* 提供 UX 以：
  * 將 CAD 應用程式與特定混合實境裝置配對 (一次)。
  * 要求在該混合實境裝置上檢視選取的幾何。
* 如果尚未執行，請啟動 Azure 遠端轉譯工作階段，使其可以在上傳及轉換 CAD 檔案時平行處理
* 將 CAD 幾何資料標準化為 Azure 遠端轉譯所支援的其中一種格式
* 將正規化資料直接傳輸到 Azure Blob 儲存體輸入容器
* 起始模型轉換程序
* 將模型的輸出容器 SAS URI 連結至 Azure 遠端轉譯工作階段
* 通知配對的混合實境應用程式，該模型已可供使用，並準備好可供您檢視並提供輸出容器 SAS URI，讓應用程式可將其附加至工作階段。

更簡單但較不簡化的方法可以將 3D 模型儲存到本機硬碟的程序自動化，然後起始程序將該儲存的檔案傳輸至 SAS 輸入容器。

### <a name="azure-marketplace"></a>Azure Marketplace

許多企業用戶端會基於安全性理由，強制將您的 Azure Stack 部署到自己的 Azure 帳戶和認證之下。 若要這麼做，您可以考慮封裝 Azure 受控應用程式，使其可以在 Azure Marketplace 上發佈為 Azure 應用程式供應項目。

其他資訊：

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [教學課程：在 Marketplace 中發佈 Azure 受控應用程式](../../../../marketplace/create-new-azure-apps-offer.md)

### <a name="security"></a>安全性

請務必從頭開始建置您的端對端 Azure 遠端轉譯解決方案，以獲得安全性。 在您的端對端解決方案設計中，有許多要考量的安全性層面，包括：

* 驗證策略
* 存取管理 – 群組、原則和權限
* 多租用戶
* 資料儲存體和傳輸加密
* 暫存使用權杖
* 分散式阻斷服務 (DDoS) 攻擊
* 威脅偵測
* VPN 和安全網路
* 防火牆
* 憑證和秘密金鑰管理
* 應用程式弱點和入侵

為了進行驗證，請盡可能將大部分的 ARR 驗證和工作階段管理移到 Azure Web 服務。 這會導致更佳的受控和更安全的解決方案。

其他資訊：

* [Azure AD 服務驗證](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-service-authentication)
* [使用 Azure 強化您的安全性狀態](https://azure.microsoft.com/overview/security/)
* [雲端安全性](https://azure.microsoft.com/product-categories/security/)