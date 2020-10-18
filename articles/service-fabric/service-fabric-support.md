---
title: 了解 Azure Service Fabric 支援選項
description: 支援的 Azure Service Fabric 叢集版本和檔案支援票證連結
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: afadd68b4c74ede522aa16809fab341e5802c627
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165922"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 支援選項

我們已建立一些支援要求選項，以提供管理 Service Fabric 叢集和應用程式工作負載的需求。 視所需的支援和問題的嚴重性而定，您可以選擇適合您的選項。

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>報告實際執行問題，或要求 Azure 的付費支援

若要報告在 Azure 上執行之 Service Fabric 叢集的相關問題，請 [在 Azure 入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 或 [Microsoft 支援入口網站](https://support.microsoft.com/oas/default.aspx?prid=16146)上開啟支援票證。

深入了解：
 
- [Microsoft 對於 Azure 的支援](https://azure.microsoft.com/support/plans/?b=16.44)。
- [Microsoft 頂級支援](https://support.microsoft.com/en-us/premier)。

> [!Note]
> 在銅級可靠性層或單一節點叢集上執行的叢集，將可讓您只執行測試工作負載。 如果使用在銅級可靠性上執行的叢集或單一節點叢集時遇到問題，Microsoft 支援小組會協助減輕問題，但不會執行根本原因分析。 如需詳細資訊，請參閱叢集 [的可靠性特性](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)。
>
> 如需生產環境就緒叢集必要條件的詳細資訊，請參閱[生產整備檢查清單](./service-fabric-production-readiness-checklist.md) (機器翻譯)。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>報告實際執行問題，或要求獨立 Service Fabric 叢集的付費支援

若要報告在內部部署或其他雲端上執行 Service Fabric 叢集的相關問題，您可以在 [Microsoft 支援入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)上開啟專業支援的票證。

深入了解：

- [Microsoft 對於內部部署的專業支援](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft 頂級支援](https://support.microsoft.com/en-us/premier)。

## <a name="report-azure-service-fabric-issues"></a>報告 Azure Service Fabric 問題

我們已設定 GitHub 存放庫以報告 Service Fabric 問題。  我們也會主動監視下列論壇。

### <a name="github-repo"></a>GitHub 存放庫 

回報 [Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues)上的 Azure Service Fabric 問題。 此存放庫適用于報告和追蹤問題，以及提出與 Azure Service Fabric 相關的小型功能要求。 **請勿使用此媒體來報告即時網站問題**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 和 MSDN 論壇

[StackOverflow 上的 Service Fabric 標記][stackoverflow]和[MSDN 上的 Service Fabric 論壇][msdn-forum]最適合用來詢問有關平臺運作方式的一般問題，以及您可以如何使用它來完成特定工作。

### <a name="azure-feedback-forum"></a>Azure 意見反應論壇

[適用于 Service Fabric 的 Azure 意見反應論壇][uservoice-forum]是提交重要產品功能構想的最佳位置。 我們會複習最受歡迎的要求，並將其納入我們的中型到長期計畫。 我們希望您在社群中集結您的建議的支援。

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric 預覽版本 - 不支援用於生產環境

我們偶爾會提供特殊的預覽版本，其中包含我們想要針對其進行早期意見反應的重大功能變更。 您應該只在不提供生產工作負載的隔離測試環境中使用預覽版本。 您的生產叢集應一律執行支援的穩定 Service Fabric 版本。 我們不提供這些預覽版本的付費支援選項。

預覽版本一律從 255 作為主要和次要版本號碼的開頭。 例如，如果您看到 Service Fabric 版本255.255.5703.949，此版本會處於預覽狀態，且僅供用於測試叢集。 這些預覽版本也會在 [Service Fabric 小組部落格](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)上宣告，並且有內含功能的詳細資料。 使用[報告 Azure Service Fabric 問題](#report-azure-service-fabric-issues)底下所列的其中一個選項來提出問題或提供意見反應。

## <a name="next-steps"></a>後續步驟

[支援的 Service Fabric 版本](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform
