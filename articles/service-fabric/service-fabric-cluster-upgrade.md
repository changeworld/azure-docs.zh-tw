---
title: 升級 Azure Service Fabric 叢集
description: 瞭解如何升級 Azure Service Fabric 叢集的版本或設定，包括設定叢集更新模式、升級憑證、新增應用程式埠、執行 OS 修補程式，以及在執行升級時所能預期的情況。
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: d92ac90e0e41d534231bafbe991a05764dbee07d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82789550"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>升級和更新 Azure Service Fabric 叢集

對於任何現代系統，可升級性的設計是產品達到長期成功的關鍵。 Azure Service Fabric 叢集是您所擁有，但部分由 Microsoft 管理的資源。 本文說明自動管理的部分以及您可以自行設定的部分。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>控制叢集上執行的網狀架構版本

確定您的叢集一律執行支援的網狀 [架構版本](service-fabric-versions.md)。 每次 Microsoft 宣佈發行新版本的 Service Fabric 時，先前的版本會在該日期的最短60天后標示為結束支援。 最新發行會在 [Service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)上宣佈。

叢集執行的版本在到期前 14 天會產生健全狀況事件，使您的叢集進入警告健全狀況狀態。 在您升級至支援的網狀架構版本之前，叢集會停留在警告狀態。

您可以將叢集設定為在 Microsoft 釋出網狀架構升級時自動接收該升級，或者，您也可以選取您想讓叢集執行的受支援網狀架構版本。  若要深入了解，請參閱[升級叢集的 Service Fabric 版本](service-fabric-cluster-upgrade-version-azure.md)。

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>自動升級期間的網狀架構升級行為

Microsoft 會維護 Azure 叢集中執行的網狀架構程式碼和組態。 視情況需要，我們會對軟體執行受監視的自動升級。 升級的項目可能是程式碼、組態或兩者。 為了確保您的應用程式不會因為這些升級而受到影響或影響最低，請在下列階段執行升級：

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>階段 1：使用所有叢集健康狀態原則執行升級

在這個階段，升級會一次進行一個升級網域，而已在叢集中執行的應用程式會繼續執行，而不會產生任何停機時間。 升級期間會遵守節點健全狀況和應用程式健康情況) 的叢集健康情況原則 (。

如果不符合叢集健康情況原則，則會回復升級，並將電子郵件傳送給訂用帳戶的擁有者。 電子郵件中包含下列資訊：

* 我們必須回復叢集升級的通知。
* 建議的修復動作 (如果有的話)。
* 執行第2階段之前， (*n*) 的天數。

如果有任何升級因為基礎結構方面的原因而失敗，我們會試著多執行幾次相同的升級。 在電子郵件寄送日期的 *n* 天之後，我們會繼續進行第2階段。

如果符合叢集健康狀態原則，則升級會被視為成功並標示為完成。 在此階段執行初次升級或重新執行任何升級期間，可能會發生這種情形。 執行成功不會有任何電子郵件確認。 這是為了避免傳送太多電子郵件給您;接收電子郵件應視為一般的例外狀況。 我們預期大部分的叢集升級都會成功，並不會影響您的應用程式可用性。

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>階段 2：僅使用預設健康狀態原則執行升級

此階段的健康狀態原則的設定方式為，升級開始時健康狀態良好的應用程式數目在升級程序期間會維持不變。 和階段 1 一樣，階段 2 的升級會一次進行一個升級網域，而已在叢集中執行的應用程式會繼續執行，而不會產生任何停機時間。 在升級期間，會遵守叢集健康狀態原則 (節點健康狀態和所有在叢集中執行之應用程式的健康狀態的組合)。

如果不符合生效的叢集健康狀態原則，則會回復升級。 然後系統會傳送一封電子郵件給訂用帳戶的擁有者。 電子郵件中包含下列資訊：

* 我們必須回復叢集升級的通知。
* 建議的修復動作 (如果有的話)。
* 距離我們執行階段 3 之前的天數 (n)。

如果有任何升級因為基礎結構方面的原因而失敗，我們會試著多執行幾次相同的升級。 在 n 天到達的前幾天會傳送提醒電子郵件。 在電子郵件寄送日期的 n 天之後，我們會繼續進行階段 3。 您必須認真看待我們在階段 2 寄送的電子郵件並採取補救動作。

如果符合叢集健康狀態原則，則升級會被視為成功並標示為完成。 在此階段執行初次升級或重新執行任何升級期間，可能會發生這種情形。 執行成功不會有任何電子郵件確認。

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>階段 3：使用積極的叢集健康狀態原則執行升級

此階段的這些健康狀態原則的目的是升級完成，而不是應用程式的健康狀態。 有些叢集升級會在這個階段結束。 如果您的叢集進入這個階段，表示您的應用程式很可能會變成狀況不良和 (或) 失去可用性。

類似其他兩個階段，階段 3 升級會一次進行一個升級網域。

如果不符合叢集健康狀態原則，則會回復升級。 如果有任何升級因為基礎結構方面的原因而失敗，我們會試著多執行幾次相同的升級。 之後，便會鎖住叢集，使它不會再收到支援和 (或) 升級。

系統會傳送含有這項資訊的電子郵件給訂用帳戶擁有者，其中也會附上修復動作。 我們預期不會有任何叢集會進入階段 3 失敗的狀態。

如果符合叢集健康狀態原則，則升級會被視為成功並標示為完成。 在此階段執行初次升級或重新執行任何升級期間，可能會發生這種情形。 執行成功不會有任何電子郵件確認。

## <a name="manage-certificates"></a>管理憑證

Service Fabric 會使用您建立叢集時指定的 [X.509 伺服器憑證](service-fabric-cluster-security.md)，以保護叢集節點之間的通訊以及驗證用戶端。 您可以在 [Azure 入口網站](https://portal.azure.com)或使用 PowerShell/Azure CLI，新增、更新或刪除叢集和用戶端的憑證。  若要深入了解，請參閱[新增或移除憑證](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>開啟應用程式連接埠

您可以透過變更與節點類型相關聯的負載平衡器資源屬性來變更應用程式連接埠。 您可以使用 Azure 入口網站，也可以使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[開啟叢集的應用程式連接埠](create-load-balancer-rule.md)。

## <a name="define-node-properties"></a>定義節點屬性

有時候您可以確保工作負載只在叢集中的特定節點類型上執行。 例如，某些工作負載可能需要 GPU 或 SSD，而有些則不用。 針對叢集中的每種節點類型，您可以將自訂節點屬性新增至叢集節點。 條件約束是陳述式，會附加至針對一或多個節點屬性選取的個別服務。 放置條件約束會定義應該執行服務的位置。

如需使用放置條件約束、節點屬性及如何定義它們的詳細資訊，請參閱[節點屬性和放置條件約束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。

## <a name="add-capacity-metrics"></a>新增容量計量

對於每個節點類型，您可以加入您要在應用程式中用來報告負載的自訂容量計量。 如需使用容量度量報告負載的詳細資訊，請參閱《Service Fabric 叢集 Resource Manager 文件》的[描述您的叢集](service-fabric-cluster-resource-manager-cluster-description.md)和[度量和負載](service-fabric-cluster-resource-manager-metrics.md)。

## <a name="set-health-policies-for-automatic-upgrades"></a>設定自動升級的健康情況原則

您可以為網狀架構升級指定自訂的健全狀況原則。 如果已將您的叢集設定為自動網狀架構升級，這些原則會套用於自動網狀架構升級的階段 1。
如果已將您的叢集設定為手動網狀架構升級，則每當您選取新版本而觸發系統開始在叢集中展開網狀架構升級時，就會套用這些原則。 如果您不覆寫原則，則會使用預設值。

在 [網狀架構升級] 刀鋒視窗下，您可以選取進階升級設定來指定自訂的健康狀態原則，或檢閱目前的設定。 檢閱下列作法圖片。

![管理自訂的健康狀態原則][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>自訂叢集的 Fabric 設定

在叢集上可以設定許多不同的組態設定，例如叢集和節點屬性的可靠性層級。 如需詳細資訊，請參閱 [Service Fabric 叢集網狀架構設定](service-fabric-cluster-fabric-settings.md)。

## <a name="patch-the-os-in-the-cluster-nodes"></a>修補叢集節點中的 OS

修補程式協調流程應用程式 (POA) 是 Service Fabric 應用程式，可在 Service Fabric 叢集上將作業系統修補自動化，而不需要停機。 [適用於 Windows 的修補程式協調流程應用程式](service-fabric-patch-orchestration-application.md)可在叢集上部署，以協調的方式安裝修補程式，同時讓服務隨時可供使用。

## <a name="next-steps"></a>接下來的步驟

* 瞭解如何自訂某些 [service fabric 叢集網狀架構設定](service-fabric-cluster-fabric-settings.md)
* 了解如何 [相應放大和相應縮小叢集](service-fabric-cluster-scale-in-out.md)
* 深入瞭解 [應用程式升級](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
