---
title: Azure Dev Spaces 如何運作
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述為 Azure 開發空間供電的過程
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234974"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces 如何運作

開發 Kubernetes 應用程式可能具有挑戰性。 您需要 Docker 和庫伯奈斯設定檔。 您需要瞭解如何在本地測試應用程式並與其他從屬服務進行交互。 您可能需要與開發人員團隊一起同時處理開發和測試多個服務。

Azure 開發人員空間為您提供了多種方法來快速反覆運算和調試 Kubernetes 應用程式並與您的團隊協作。 本文介紹了 Azure 開發空間可以做什麼以及其工作原理。

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>快速反覆運算和調試庫伯奈斯應用程式

Azure 開發人員空間減少了在 AKS 群集上下文中開發、測試和反覆運算 Kubernetes 應用程式的工作。 這種工作量的減少使開發人員能夠專注于其應用程式的業務邏輯，而不是將其服務配置為在 Kubernetes 中運行。

### <a name="connect-your-development-machine-to-aks"></a>將開發電腦連接到 AKS

使用 Azure 開發人員空間，可以將開發電腦連接到 AKS 群集，從而可以在開發電腦上運行和調試代碼，就像它在群集上運行一樣。 Azure 開發人員空間通過在群集上運行充當遠端代理的 Pod 來重定向連接的 AKS 群集之間的流量，從而在開發電腦和群集之間重定向流量。 此流量重定向允許開發電腦上的代碼和在 AKS 群集中運行的服務進行通信，就像它們位於同一 AKS 群集中一樣。 有關將開發電腦連接到 AKS 的詳細資訊，請參閱[將開發電腦連接到 AKS 群集的工作原理][how-it-works-connect]。

### <a name="run-your-code-in-aks"></a>在 AKS 中運行代碼

除了在開發電腦和 AKS 群集之間重定向流量外，借助 Azure 開發人員空間，您還可以直接在 AKS 中配置和快速運行代碼。 借助視覺化工作室、視覺化工作室代碼或 Azure 開發空間 CLI，Azure 開發人員空間將上載代碼到群集，然後生成並運行它。 Azure 開發人員空間還可以智慧地同步代碼更改並重新啟動服務，以便根據需要反映更改。 運行代碼時，生成日誌和 HTTP 跟蹤將資料流回用戶端，以便您可以監視進度並診斷任何問題。 您還可以使用 Azure 開發人員空間，將視覺化工作室和視覺化工作室代碼中的調試器附加到 JAVA、Node.js 和 .NET 核心服務。 有關詳細資訊，請參閱為[Azure 開發人員空間準備專案的工作原理][how-it-works-prep]、使用[Azure 開發人員空間運行代碼的工作原理][how-it-works-up]，以及使用[Azure 開發人員空間遠端偵錯代碼的工作原理][how-it-works-remote-debugging]。

## <a name="team-development"></a>小組開發

Azure 開發人員空間可説明團隊在同一 AKS 群集上高效地處理其應用程式，而不會造成中斷。

### <a name="intelligent-routing-between-dev-spaces"></a>開發空間之間的智慧路由

使用 Azure 開發人員空間，團隊可以共用運行雲原生應用程式的單個 AKS 群集，並創建獨立的開發空間，團隊可以在其中開發、測試和調試，而不會干擾其他開發空間。 應用程式的基準版本在根開發空間中運行。 然後，團隊成員根據應用程式的根空間創建獨立的子開發空間，以便進行開發、測試和調試更改。 通過 Dev Spaces 中的路由功能，子開發空間可以在子開發空間和父開發空間中運行的服務之間路由請求。 此路由允許開發人員在重用父空間中的從屬服務時運行自己的服務版本。 每個子空間都有其自己獨特的 URL，其他人可以共用和訪問該 URL 進行協作。 有關路由在 Azure 開發空間中的工作方式的詳細資訊，請參閱[路由如何與 Azure 開發空間一起工作][how-it-works-routing]。

### <a name="live-testing-an-open-pull-request"></a>即時測試打開拉取請求

還可以將 GitHub 操作與 Azure 開發空間一起使用，在合併之前直接在群集中的拉取請求中測試對應用程式的更改。 Azure 開發人員空間可以自動將應用程式的審閱版本部署到群集，從而允許作者和其他團隊成員查看整個應用程式上下文中的更改。 使用 Azure 開發人員空間的路由功能，應用程式的此審閱版本也會部署到群集，而不會影響其他開發空間。 所有這些功能都允許您自信地批准和合併拉取請求。 要查看 GitHub 操作和 Azure 開發空間的示例，請參閱[GitHub 操作& Azure 庫伯奈斯服務][pr-flow]。

## <a name="next-steps"></a>後續步驟

要開始將本地開發電腦連接到 AKS 群集，請參閱[將開發電腦連接到 AKS 群集][connect]。

要開始使用 Azure 開發人員空間進行團隊開發，請參閱[Azure 開發人員空間中的團隊開發][quickstart-team]快速入門。

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development