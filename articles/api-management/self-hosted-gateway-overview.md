---
title: 自託管的 Azure API 管理閘道概述 |微軟文檔
description: 瞭解自託管 Azure API 管理閘道如何説明組織在混合和多雲環境中管理 API。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513714"
---
# <a name="self-hosted-api-management-gateway-overview"></a>自託管 API 管理閘道概述

本文介紹了自託管閘道功能如何支援混合和多雲 API 管理，展示其高級體系結構，並重點介紹其基本功能。

> [!NOTE]
> 自託管閘道功能處於預覽狀態。 在預覽期間，自託管閘道僅在開發人員和高級層中可用，無需支付額外費用。 開發人員層僅限於單個自託管閘道部署。

## <a name="hybrid-and-multi-cloud-api-management"></a>混合和多雲 API 管理

自託管閘道功能擴展了對混合和多雲環境的 API 管理支援，使組織能夠從 Azure 中的單個 API 管理服務高效、安全地管理託管在本地和雲中的 API。

借助自託管閘道，客戶可以靈活地將 API 管理閘道元件的容器化版本部署到託管 API 的相同環境中。 所有自託管閘道都由它們聯合的 API 管理服務進行管理，從而為客戶提供所有內部和外部 API 的可見度和統一管理體驗。 將閘道放置在 API 附近，使客戶能夠優化 API 流量，並滿足安全性和合規性要求。

每個 API 管理服務由以下關鍵元件組成：

-   管理平面作為 API 公開，用於通過 Azure 門戶、PowerShell 和其他受支援的機制佈建服務。
-   閘道（或資料平面）負責代理 API 請求、應用策略和收集遙測資料
-   開發人員用來發現、學習和上載的開發人員門戶來使用 API

預設情況下，所有這些元件都部署在 Azure 中，從而導致所有 API 流量（在下圖中顯示為實心黑色箭頭）流經 Azure，而不管實現 API 的後端託管在何處。 此模型的操作簡單性是延遲增加、合規性問題以及在某些情況下額外的資料傳輸費用為代價的。

![沒有自承載閘道的 API 流量](media/self-hosted-gateway-overview/without-gateways.png)

將自託管閘道部署到與後端 API 實現相同的環境中，並將其添加到 API 管理服務中，允許 API 流量直接流向後端 API，從而提高延遲、優化資料傳輸成本並啟用合規性，同時保留在組織內擁有單一管理和發現所有 API 的優勢，而不管它們在實現的託管位置如何。

![具有自承載閘道的 API 流量流](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>包裝和功能

自託管閘道是作為每個 API 管理服務的一部分部署到 Azure 的託管閘道的容器化、功能等效版本。 自託管閘道可從 Microsoft 容器註冊表作為基於 Linux 的 Docker 容器提供。 它可以部署到 Docker、Kubernetes 或任何其他在桌面、伺服器叢集或雲基礎結構上運行的容器編排解決方案。

> [!IMPORTANT]
> 託管閘道中提供的某些功能在預覽版中尚不可用。 最值得注意的是：登錄到事件中心策略，服務交換矩陣集成，下游 HTTP/2。 沒有計劃使內置緩存在自託管閘道中可用。

## <a name="connectivity-to-azure"></a>連接到 Azure

自託管閘道需要從站 TCP/IP 連接到埠 443 上的 Azure。 每個自託管閘道都必須與單個 API 管理服務關聯，並通過其管理平面進行配置。 自託管閘道使用與 Azure 的連接進行：

-   通過每分鐘發送活動訊號消息來報告其狀態
-   定期檢查（每 10 秒一次），並在配置更新可用時應用配置更新
-   將請求日誌和指標發送到 Azure 監視器（如果配置為這樣做）
-   將事件發送到應用程式見解（如果設置為這樣做）

當與 Azure 的連接丟失時，自託管閘道將無法接收配置更新、報告其狀態或上載遙測。

自託管閘道旨在"故障靜態"，並且可以在暫時失去與 Azure 的連接時生存下來。 它可以在打開本地配置備份時部署，也可以不打開本地配置備份。 在前一種情況下，自託管閘道將定期將配置的備份副本保存在附加到容器或 Pod 的持久卷上。

當配置備份關閉且與 Azure 的連接中斷時：

-   正在運行的自託管閘道將繼續使用配置的記憶體中副本運行
-   已停止的自託管閘道將無法啟動

啟用配置備份並中斷與 Azure 的連接時：

-   正在運行的自託管閘道將繼續使用配置的記憶體中副本運行
-   停止的自託管閘道將開始使用配置的備份副本

恢復連接後，受中斷影響的每個自託管閘道將自動與其關聯的 API 管理服務重新連接，並下載閘道"離線"時發生的所有配置更新。

## <a name="next-steps"></a>後續步驟

-   [閱讀白皮書，瞭解有關此主題的其他背景](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [將自託管閘道部署到 Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [將自託管閘道部署到庫貝內特斯](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
