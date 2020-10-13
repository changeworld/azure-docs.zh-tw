---
title: 了解 Azure Service Fabric 支援選項
description: 支援的 Azure Service Fabric 叢集版本和檔案支援票證連結
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: ae49a59c2629d9f9461d298ada555d314c0c9f22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256963"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 支援選項

為了在您執行應用程式工作負載所在的 Service Fabric 叢集傳遞適當的支援，我們已經為您設定各種選項。 依據所需的支援層級和問題的嚴重性而定，您必須挑選正確的選項。 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>報告實際執行問題，或要求 Azure 的付費支援

對於報告 Azure 上部署之 Service Fabric 叢集的問題，在 [Azure 入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)或 [Microsoft 支援入口網站](https://support.microsoft.com/oas/default.aspx?prid=16146)上建立支援票證。

深入了解：
 
- [Microsoft 對於 Azure 的支援](https://azure.microsoft.com/support/plans/?b=16.44)。
- [Microsoft 頂級支援](https://support.microsoft.com/en-us/premier)。

> [!Note]
> 在銅級可靠性層上執行的叢集或單一節點叢集僅供執行測試工作負載。 如果使用在銅級可靠性上執行的叢集或單一節點叢集時遇到問題，Microsoft 支援小組會協助減輕問題，但不會執行根本原因分析。 如需詳細資訊，請參閱[叢集的可靠性特性](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) (機器翻譯)。
>
> 如需生產環境就緒叢集必要條件的詳細資訊，請參閱[生產整備檢查清單](./service-fabric-production-readiness-checklist.md) (機器翻譯)。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>報告實際執行問題，或要求獨立 Service Fabric 叢集的付費支援

對於報告內部部署或其他雲端上部署之 Service Fabric 叢集的問題，在 [Microsoft 支援入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)上建立專業支援票證。

深入了解：

- [Microsoft 對於內部部署的專業支援](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft 頂級支援](https://support.microsoft.com/en-us/premier)。

## <a name="report-azure-service-fabric-issues"></a>報告 Azure Service Fabric 問題

我們已設定 GitHub 存放庫以報告 Service Fabric 問題。  我們也會主動監視下列論壇。

### <a name="github-repo"></a>GitHub 存放庫 

在 [Service-Fabric-issues git 存放庫](https://github.com/Azure/service-fabric-issues)上報告 Azure Service Fabric 問題。 此存放庫適用於報告和追蹤 Azure Service Fabric 的問題，並進行小規模的功能要求。 **請勿使用此存放庫來報告即時網站問題**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 和 MSDN 論壇

[StackOverflow 上的 Service Fabric 標籤][stackoverflow]和 [MSDN 上的 Service Fabric 論壇][msdn-forum]最適合用來詢問關於平台如何運作以及您如何使用它來完成特定工作的問題。

### <a name="azure-feedback-forum"></a>Azure 意見反應論壇

[Service Fabric 的 Azure 意見反應論壇][uservoice-forum]是用來提交對於產品的大功能構想的最佳位置，因為檢閱最受歡迎要求是中程至長程規劃的一部分。 我們希望您在社群中集結您的建議的支援。

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric 預覽版本 - 不支援用於生產環境

有時候，我們會以預覽形式發行包含我們想得到意見反應之重要功能的版本。 這些預覽版本只能用於測試目的。 您的生產叢集應一律執行支援的穩定 Service Fabric 版本。 預覽版本一律從 255 作為主要和次要版本號碼的開頭。 例如，如果您看到 Service Fabric 版本 255.255.5703.949，該發行版本只能使用於測試叢集並處於預覽狀態。 這些預覽版本也會在 [Service Fabric 小組部落格](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)上宣告，並且有內含功能的詳細資料。
這些預覽版本沒有付費的支援選項。 使用[報告 Azure Service Fabric 問題](#report-azure-service-fabric-issues)底下所列的其中一個選項來提出問題或提供意見反應。

## <a name="next-steps"></a>後續步驟

[支援的 Service Fabric 版本](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform
