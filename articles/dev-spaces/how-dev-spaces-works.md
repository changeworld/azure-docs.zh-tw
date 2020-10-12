---
title: Azure Dev Spaces 如何運作
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: 描述 power Azure Dev Spaces 的處理常式
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: 81408ed4bbe5322538d893b29c7397104c5b0844
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981263"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces 如何運作

開發 Kubernetes 應用程式可能是一項挑戰。 您需要 Docker 和 Kubernetes 設定檔。 您必須瞭解如何在本機測試您的應用程式，並與其他相依服務互動。 您可能需要一次處理多個服務的開發和測試，以及一小組開發人員。

Azure Dev Spaces 可讓您透過多種方式快速地逐一查看和 Kubernetes 應用程式，並與您的小組共同作業。 本文說明 Azure Dev Spaces 可以做什麼，以及它的運作方式。

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>快速逐一查看和 Kubernetes 應用程式

Azure Dev Spaces 可減少在 AKS 叢集的環境中開發、測試和反覆運算 Kubernetes 應用程式的工作。 這項縮減可讓開發人員專注于其應用程式的商務邏輯，而不會將其服務設定為在 Kubernetes 中執行。

### <a name="bridge-to-kubernetes"></a>橋接至 Kubernetes

使用 Bridge 到 Kubernetes，您可以將開發電腦連接到您的 Kubernetes 叢集，讓您在開發電腦上執行和偵錯工具代碼，就像是在叢集上執行一樣。 橋接器至 Kubernetes 會在您的叢集上執行 pod，以重新導向連線叢集之間的流量，以作為遠端代理程式來重新導向開發電腦與叢集之間的流量。 此流量重新導向可讓您開發電腦上的程式碼和在叢集中執行的服務進行通訊，就像是在相同的叢集中一樣。 如需將您的開發電腦連接到 Kubernetes 叢集的詳細資訊，請參閱 [Kubernetes 的橋樑如何運作][how-it-works-bridge-to-kubernetes]。

### <a name="run-your-code-in-aks"></a>在 AKS 中執行您的程式碼

除了在您的開發電腦與 AKS 叢集之間重新導向流量之外，您還可以使用 Azure Dev Spaces，直接在 AKS 中設定和快速執行您的程式碼。 使用 Visual Studio、Visual Studio Code 或 Azure Dev Spaces CLI 時，Azure Dev Spaces 會將您的程式碼上傳至叢集，然後建立並執行它。 Azure Dev spaces 也可以明智地同步程式碼變更並重新啟動您的服務，以反映所需的變更。 執行您的程式碼時，組建記錄檔和 HTTP 追蹤會串流回用戶端，讓您可以監視進度及診斷任何問題。 您也可以使用 Azure Dev Spaces，在 Visual Studio 和 Visual Studio Code 中附加偵錯工具至 JAVA、Node.js 和 .NET Core 服務。 如需詳細資訊，請參閱 [準備 Azure Dev Spaces 的專案如何運作][how-it-works-prep]、 [如何使用 Azure Dev Spaces 執行程式碼的運作][how-it-works-up]方式，以及如何 [使用 Azure Dev Spaces 來遠端偵錯程式碼的運作方式][how-it-works-remote-debugging]。

## <a name="team-development"></a>小組開發

Azure Dev Spaces 可協助小組在相同的 AKS 叢集上有效率地處理其應用程式，而不會造成干擾。

### <a name="intelligent-routing-between-dev-spaces"></a>開發人員空間之間的智慧型路由

藉由 Azure Dev Spaces，小組可以共用執行雲端原生應用程式的單一 AKS 叢集，並建立可供小組開發、測試和錯的獨立開發空間，而不會干擾其他開發人員空間。 應用程式的基準版本會在根開發人員空間中執行。 小組成員接著會根據根空間建立獨立的子開發空間，以進行應用程式的開發、測試和偵錯工具變更。 透過開發人員空間中的路由功能，子開發空間可在子開發空間中執行的服務與父開發空間之間路由傳送要求。 此路由可讓開發人員執行自己的服務版本，同時從父空間重複使用相依的服務。 每個子空間都有自己的唯一 URL，可供其他人共用及存取以進行共同作業。 如需有關路由如何在 Azure Dev Spaces 中運作的詳細資訊，請參閱 [路由如何搭配 Azure Dev Spaces 運作][how-it-works-routing]。

### <a name="live-testing-an-open-pull-request"></a>即時測試開啟的提取要求

您也可以使用 GitHub Actions 搭配 Azure Dev Spaces，直接在您的叢集中的提取要求中測試應用程式的變更，然後再進行合併。 Azure Dev Spaces 可以將應用程式的審核版本自動部署到您的叢集，讓作者和其他小組成員可以檢查整個應用程式內容中的變更。 使用 Azure Dev Spaces 的路由功能，此應用程式的審核版本也會部署到您的叢集，而不會影響到其他開發人員空間。 所有這些功能都可讓您安心地核准和合併提取要求。 若要查看 GitHub Actions 和 Azure Dev Spaces 的範例，請參閱 [GitHub Actions & Azure Kubernetes Service][pr-flow]。

## <a name="next-steps"></a>後續步驟

若要開始將您的本機開發電腦連線到 AKS 叢集，請參閱 [將您的開發電腦連線至 AKS][connect]叢集。


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development