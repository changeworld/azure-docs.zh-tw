---
title: Azure 庫伯奈斯服務 （AKS） 診斷概述
description: 瞭解 Azure 庫伯奈斯服務中的自診斷群集。
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126599"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure 庫伯奈斯服務診斷（預覽）概述

解決 Azure 庫伯奈斯服務 （AKS） 群集問題是維護群集的重要組成部分，尤其是在群集運行任務關鍵型工作負載時。 AKS 診斷是一種智慧的自診斷體驗，可説明您識別和解決群集中的問題。 AKS 診斷是雲原生診斷，您可以使用它，無需額外的配置或計費成本。

此功能現已處於公共預覽版中。

## <a name="open-aks-diagnostics"></a>打開 AKS 診斷

要訪問 AKS 診斷：

- 導航到[Azure 門戶](https://portal.azure.com)中的庫伯內斯群集。
- 按一下"**診斷並解決**左側導航中的問題"，該導航將打開 AKS 診斷。
- 使用主頁磁貼中的關鍵字選擇最能描述群集問題的類別，或在搜索欄中鍵入最能描述問題關鍵字，例如_叢集節點問題_。

![首頁](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>查看診斷報告

按一下類別後，您可以查看特定于群集的診斷報告。 如果群集中存在狀態圖示的問題，診斷報告會智慧調用。 您可以通過按一下 **"更多資訊**"來深入瞭解每個主題，以查看問題的詳細描述、建議的操作、指向有用文檔的連結、相關指標和日誌記錄資料。 在運行各種檢查後，根據群集的目前狀態智慧生成診斷報告。 診斷報告是確定群集問題和查找解決問題的後續步驟的有用工具。

![診斷報告](./media/concepts-diagnostics/diagnostic-report.png)

![擴展診斷報告](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>群集洞察

以下診斷檢查可在**群集見解**中提供。

### <a name="cluster-node-issues"></a>叢集節點問題

叢集節點問題檢查可能導致群集意外行為的節點相關問題。

- 節點就緒問題
- 節點故障
- 資源不足
- 節點缺少 IP 配置
- 節點 CNI 故障
- 未找到節點
- 節點電源關閉
- 節點身份驗證失敗
- 節點庫貝代理過時

### <a name="create-read-update--delete-operations"></a>創建、讀取、更新&刪除操作

CRUD 操作檢查可能導致群集中問題的任何 CRUD 操作。

- 使用中的子網刪除操作錯誤
- 網路安全性群組刪除操作錯誤
- 使用中路由表刪除操作錯誤
- 引用的資源預配錯誤
- 公共 IP 位址刪除操作錯誤
- 部署失敗，由於部署配額
- 由於組織策略而導致的操作錯誤
- 缺少訂閱註冊
- VM 擴展預配錯誤
- 子網容量
- 超出配額錯誤

### <a name="identity-and-security-management"></a>身分識別和安全性管理

身份和安全管理檢測可能阻止與群集通信的身份驗證和授權錯誤。

- 節點授權失敗
- 401 錯誤
- 403 錯誤

## <a name="next-steps"></a>後續步驟

收集日誌，以説明您使用[AKS Periscope](https://aka.ms/aksperiscope)進一步解決群集問題。

通過在標題中添加"[Diag]"，在[UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks)上發佈您的問題或回饋。
