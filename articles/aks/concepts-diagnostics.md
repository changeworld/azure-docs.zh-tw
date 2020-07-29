---
title: Azure Kubernetes Service （AKS）診斷總覽
description: 深入瞭解 Azure Kubernetes Service 中的自我診斷叢集。
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79126599"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure Kubernetes Service 診斷（預覽）總覽

疑難排解 Azure Kubernetes Service （AKS）叢集問題是維護叢集的重要部分，特別是當您的叢集正在執行任務關鍵性工作負載時。 AKS Diagnostics 是智慧型、自我診斷的體驗，可協助您找出並解決叢集中的問題。 AKS 診斷是雲端原生的，您可以使用它，而不需要額外的設定或計費成本。

這項功能現已開放公開預覽。

## <a name="open-aks-diagnostics"></a>開啟 AKS 診斷

若要存取 AKS 診斷：

- 在[Azure 入口網站](https://portal.azure.com)中，流覽至您的 Kubernetes 叢集。
- 按一下左側導覽中的 [**診斷並解決問題**]，這會開啟 [AKS 診斷]。
- 使用 [首頁] 磚中的關鍵字，或在搜尋列中輸入最能描述您問題的關鍵字（例如叢集_節點問題_），以選擇最能描述叢集問題的類別。

![首頁](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>檢視診斷報告

按一下類別之後，您就可以查看叢集專屬的診斷報告。 如果您的叢集中有任何狀態圖示的問題，診斷報表會以智慧方式呼叫。 您可以按一下 [**詳細資訊**]，以查看問題的詳細描述、建議的動作、實用檔的連結、相關的計量和記錄資料，以向下切入每個主題。 在執行各種檢查之後，會根據您叢集的目前狀態，以智慧方式產生診斷報告。 診斷報告可以用來查明叢集的問題，並尋找解決問題的後續步驟。

![診斷報告](./media/concepts-diagnostics/diagnostic-report.png)

![展開的診斷報告](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Cluster Insights

**Cluster Insights**中提供下列診斷檢查。

### <a name="cluster-node-issues"></a>叢集節點問題

叢集節點問題會檢查是否有可能造成您的叢集非預期行為的節點相關問題。

- 節點就緒問題
- 節點失敗
- 資源不足
- 節點遺失 IP 設定
- 節點 CNI 失敗
- 找不到節點
- 節點關閉電源
- 節點驗證失敗
- 節點 kube-proxy 過時

### <a name="create-read-update--delete-operations"></a>建立、讀取、更新 & 刪除作業

CRUD 作業會檢查是否有可能會在您的叢集中造成問題的 CRUD 作業。

- 使用中的子網刪除操作錯誤
- 網路安全性群組刪除操作錯誤
- 使用中的路由表刪除操作錯誤
- 參考的資源布建錯誤
- 公用 IP 位址刪除操作錯誤
- 部署因部署配額而失敗
- 因組織原則而發生作業錯誤
- 缺少訂用帳戶註冊
- VM 擴充功能布建錯誤
- 子網容量
- 超過配額錯誤

### <a name="identity-and-security-management"></a>身分識別和安全性管理

身分識別和安全性管理會偵測可能阻止與您叢集通訊的驗證和授權錯誤。

- 節點授權失敗
- 401錯誤
- 403 錯誤

## <a name="next-steps"></a>後續步驟

收集記錄檔，以協助您使用[AKS Periscope](https://aka.ms/aksperiscope)進一步疑難排解叢集問題。

在標題中新增 "[診斷]"，以在[UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks)張貼您的問題或意見反應。
