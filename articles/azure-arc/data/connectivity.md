---
title: 連線模式和需求
description: 說明從環境到 Azure 的 Azure Arc 啟用的資料服務連接選項
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 9c1dd6f628e87792808d14db2c7bcc7f050923a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713201"
---
# <a name="connectivity-modes-and-requirements"></a>連接模式和需求

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>連接模式

從您的 Azure Arc 啟用的資料服務環境到 Azure 的連接程度有多個選項。 由於您的需求會因商務原則、政府規定或 Azure 的網路連線可用性而有所不同，因此您可以從下列連線模式中選擇。

Azure Arc 啟用的資料服務可讓您選擇在兩個不同的「連線模式」中連線到 Azure：直接連線和間接連線。  這可讓您彈性地選擇要傳送至 Azure 的資料量，以及使用者如何與 Arc 資料控制器互動。 視所選的連接模式而定，Azure Arc 啟用的資料服務的某些功能可能會或可能無法使用。

重要的是，如果 Azure Arc 啟用的資料服務直接連線到 Azure，則使用者可以使用 [Azure Resource Manager api](/rest/api/resources/)、Azure CLI 和 Azure 入口網站來操作 Azure Arc 資料服務。 直接連線模式中的體驗，就像您使用任何其他 Azure 服務搭配布建/取消布建、調整規模、設定等等，以及在 Azure 入口網站中的所有功能。  如果 Azure Arc 啟用的資料服務已間接連接至 Azure，則 Azure 入口網站是唯讀的視圖。 您可以查看已部署之 SQL 受控實例和 Postgres 超大規模實例的清查，以及這些實例的詳細資料，但您無法在 Azure 入口網站中對其採取動作。  在間接連線模式中，所有動作都必須使用 Azure Data Studio、Azure 資料 CLI 或 Kubernetes 原生工具（例如 kubectl）在本機執行。

此外，Azure Active Directory 和 Azure Role-Based 存取控制僅適用于直接連線模式，因為與 Azure 之間的持續連線和直接連線可提供這項功能。

最後，某些 Azure 連接的服務只有在可以直接達到時才能使用，例如 Azure Defender 安全性服務、容器深入解析和 ' Azure 備份/blob 儲存體 '。

目前，在預覽中，只支援間接連接模式。  未來規劃了直接連接模式。  下面有一個可想像的永不連線模式，但目前不支援。

||**間接連接**|**直接連線**|**從未連接**|
|---|---|---|---|
|**說明**|間接連接模式可在您環境中的本機提供大部分的管理服務，而不需要直接連線到 Azure。  您 _只能_將少量的資料傳送至 Azure，以供清查和計費之用。 它會匯出至檔案，並上傳至 Azure 至少每個月一次。  不需要直接或連續連接至 Azure。  某些需要連接至 Azure 的功能和服務將無法使用。|直接連線模式會在可與 Azure 建立直接連線時，提供所有可用的服務。 一律會從您 _的_ 環境起始連接至 Azure，並使用標準埠和通訊協定，例如 HTTPS/443。|任何資料都不能以任何方式傳送至 Azure 或從中傳送。|
|**目前的可用性**| 在預覽版中提供。|預計在未來進行預覽。|目前不支援。|
|**一般使用案例**|內部部署資料中心，因為商務或法規合規性原則或不考慮外部攻擊或資料遭到外泄，所以不允許在資料中心的資料區域內進行連線。  一般範例：金融機構、醫療保健、政府。 <br/><br/>邊緣網站通常不會連線到網際網路的邊緣網站位置。  一般範例：石油/天然氣或軍事現場應用程式。  <br/><br/>具有長時間中斷連接的邊緣網站位置。  一般範例：運動場、巡航。 | 使用公用雲端的組織。  一般範例： Azure、AWS 或 Google Cloud。<br/><br/>通常存在且允許網際網路連線的邊緣網站位置。  一般範例：零售商店、製造。<br/><br/>公司資料中心具有更寬鬆的原則，可在資料中心的資料區域與網際網路之間進行連接。  一般範例：非管制企業、小型/中型企業|真正的「空調」環境，在任何情況下都不會有任何資料可從資料環境中進入或移出。 一般範例：高秘密政府機構。|
|**將資料傳送至 Azure 的方式**|有三個選項可供您將帳單和清查資料傳送到 Azure：<br><br> 1) 資料會透過可連線至安全資料區域和 Azure 的自動化程式，從資料區域匯出。<br><br>2) 資料會由資料區域內的自動化程式從資料區域匯出，自動複製到較不安全的區域，而較不安全區域中的自動化程式會將資料上傳至 Azure。<br><br>3) 資料是由安全區域內的使用者手動匯出，以手動方式從安全區域中取出，並以手動方式上傳至 Azure。 <br><br>前兩個選項是可排程為經常執行的自動化連續程式，因此，將資料傳輸至 Azure 時，最少的延遲會受限於 Azure 的可用連線。|資料會自動並持續傳送至 Azure。|資料永遠不會傳送至 Azure。|

## <a name="feature-availability-by-connectivity-mode"></a>依連接模式的功能可用性

|**功能**|**間接連接**|**直接連線**|
|---|---|---|
|**自動高可用性**|支援|支援|
|**自助服務佈建**|支援<br/>您可以透過 Azure Data Studio、Azure 資料 CLI 或 Kubernetes 原生工具 (helm、kubectl、oc 等 ) ，或使用 Azure Arc 啟用的 Kubernetes Gitops) 將布建來建立。|支援<br/>除了間接連線模式建立選項以外，您也可以透過 Azure 入口網站、Azure Resource Manager Api、Azure CLI 或 ARM 範本建立。 **正在等待直接連線模式的可用性**
|**彈性延展性**|支援|支援<br/>**正在等待直接連線模式的可用性**|
|**Billing**|支援<br/>計費資料會定期匯出並傳送至 Azure。|支援<br/>計費資料會自動並持續傳送至 Azure，並以近乎即時的方式進行反映。 **正在等待直接連線模式的可用性**|
|**庫存管理**|支援<br/>清查資料會定期匯出並傳送至 Azure。|支援<br/>清查資料會自動並持續傳送至 Azure，並以近乎即時的方式進行反映。 **正在等待直接連線模式的可用性**|
|**自動升級和修補**|支援<br/>資料控制器必須能夠直接存取 Microsoft Container Registry (MCR) 或容器映射需要從 MCR 提取，並推送至資料控制器可存取的本機私人容器登錄。|支援<br/>**正在等待直接連線模式的可用性**|
|**自動備份和還原**|支援<br/>自動本機備份和還原。|支援<br/>除了自動本機備份和還原之外，您還可以 _選擇性地_ 將備份傳送至長期、異地保留的 Azure 備份。 **正在等待直接連線模式的可用性**|
|**監視**|支援<br/>使用 Grafana 和 Kibana 儀表板的本機監視。|支援<br/>除了本機監視儀表板，您可以 _選擇性地_ 將監視資料和記錄檔傳送至 Azure 監視器，以便在同一處大規模監視多個網站。 **正在等待直接連線模式的可用性**|
|**驗證**|使用本機使用者名稱/密碼進行資料控制器和儀表板驗證。 使用 SQL 和 Postgres 登入或 Active Directory 連接至資料庫實例。  使用 K8s authentication 提供者來驗證 Kubernetes API。|除了（而非間接連接模式的驗證方法）之外，您也可以 _選擇_ 使用 Azure Active Directory。 **正在等待直接連線模式的可用性**|
|**角色型存取控制 (RBAC)**|在 Kubernetes API 上使用 Kubernetes RBAC。 針對資料庫實例，請使用 SQL 和 Postgres RBAC。|您可以選擇性地整合 Azure Active Directory 和 Azure RBAC。 **正在等待直接連線模式的可用性**|
|**Azure Defender**|不支援|規劃未來|

## <a name="connectivity-requirements"></a>連線能力需求

**某些功能需要連接至 Azure。**

**所有與 Azure 的通訊一律會從您的環境起始。** 即使是由 Azure 入口網站中的使用者所起始的作業也是如此。  在此情況下，實際上是在 Azure 中排入佇列的工作。  您環境中的代理程式會起始與 Azure 的通訊，以查看佇列中有哪些工作、執行工作，以及回報狀態/完成/失敗至 Azure。

|**資料類型**|[方向]|**必要/選用**|**額外成本**|**需要模式**|**備註**|
|---|---|---|---|---|---|
|**容器映像**|Microsoft Container Registry-> 客戶|必要|否|間接或直接|容器映射是散發軟體的方法。  在可透過網際網路連線至 Microsoft Container Registry (MCR) 的環境中，可以直接從 MCR 提取容器映射。  當部署環境沒有直接連線時，您可以從 MCR 提取映射，並將其推送至部署環境中的私人容器登錄。  在建立時，您可以將建立程式設定為從私用容器登錄（而非 MCR）提取。 這也適用于自動更新。|
|**資源清查**|客戶環境-> Azure|必要|否|間接或直接|資料控制站、資料庫實例 (于 postgresql 和 SQL) 的清查會保留在 Azure 中供計費之用，也適用于在一個位置建立所有資料控制器和資料庫實例的清查，這在您有多個 Azure Arc 資料服務的環境時特別有用。  當實例布建、取消布建、相應放大/縮小時，會在 Azure 中更新清查的相應增加/減少。|
|**帳單遙測資料**|客戶環境-> Azure|必要|否|間接或直接|資料庫實例的使用量必須傳送至 Azure，以供計費之用。  在預覽期間，Azure Arc 啟用的資料服務不會產生任何費用。|
|**監視資料和記錄**|客戶環境-> Azure|選用|可能視資料量而定 (請參閱 [Azure 監視器定價](https://azure.microsoft.com/en-us/pricing/details/monitor/)) |間接或直接|您可能會想要將本機收集的監視資料和記錄檔傳送至 Azure 監視器，以將多個環境中的資料匯總至一個位置，也可以使用 Azure 監視器的服務，例如警示、使用 Azure Machine Learning 中的資料等。|
|**Azure 角色型存取控制 (Azure RBAC) **|客戶環境-> Azure > 客戶環境|選用|否|僅限直接|如果您想要使用 Azure RBAC，則必須隨時與 Azure 建立連線能力。  如果您不想要使用 Azure RBAC，則可以使用本機 Kubernetes RBAC。  **正在等待直接連線模式的可用性**|
|**Azure Active Directory (AD)**|客戶環境-> Azure > 客戶環境|選用|或許您可能已支付 Azure AD|僅限直接|如果您想要使用 Azure AD 進行驗證，則必須隨時與 Azure 建立連接。 如果您不想要使用 Azure AD 來進行驗證，您可以 Active Directory 同盟服務 (ADFS) 超過 Active Directory。 **正在等待直接連線模式的可用性**|
|**備份/還原**|客戶環境-> Azure > 客戶環境|選用|是儲存體成本|僅限直接|您可能會想要將在本機執行的備份傳送到 Azure 備份，以長期備份保留備份，並將它們帶回本機環境進行還原。 **正在等待直接連線模式的可用性**|
|**Azure Defender 安全性服務**|客戶環境-> Azure > 客戶環境|選用|是|僅限直接|**正在等待直接連線模式的可用性**|
|**Azure 入口網站的布建和設定變更**|客戶環境-> Azure > 客戶環境|選用|否|僅限直接|您可以使用 Azure Data Studio 或 azdata CLI 在本機進行布建和設定變更。  在直接連線模式中，您也可以從 Azure 入口網站布建及變更設定。 **正在等待直接連線模式的可用性**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>網際網路位址、埠、加密和 proxy 伺服器支援的詳細資料

目前，在預覽階段中，只支援間接連接的模式。  在此模式中，網際網路上提供的服務只需要三個連接。  所有對 Azure 和 Microsoft Container Registry 的 HTTPS 連線都會使用已正式簽署和可驗證的憑證，以 SSL/TLS 加密。

|**名稱**|**連線來源**|**連線目標**|**通訊協定**|**通訊埠**|**可以使用 proxy**|**驗證**|**備註**|
|---|---|---|---|---|---|---|---|
|**Microsoft Container Registry (MCR) **|每個 Kubernetes 節點上的 Kubernetes kubelet 會提取容器映射。|`mcr.microsoft.com`|HTTPS|443|是|無|Microsoft Container Registry 會裝載已啟用 Azure Arc 的資料服務容器映射。  您可以從 MCR 提取這些映射，並將其推送至私人容器登錄，並設定資料控制器部署程式從該私人容器登錄中提取容器映射。|
|**Azure Resource Manager Api**|執行 Azure Data Studio、Azure 資料 CLI 或 Azure CLI 的電腦，其會連線到 Azure。|`login.microsoftonline.com`<br/>`management.azure.com`<br/>`san-af-eastus-prod.azurewebsites.net`<br/>`san-af-eastus2-prod.azurewebsites.net`<br/>`san-af-australiaeast-prod.azurewebsites.net`<br/>`san-af-centralus-prod.azurewebsites.net`<br/>`san-af-westus2-prod.azurewebsites.net`<br/>`san-af-westeurope-prod.azurewebsites.net`<br/>`san-af-southeastasia-prod.azurewebsites.net`<br/>`san-af-koreacentral-prod.azurewebsites.net`<br/>`san-af-northeurope-prod.azurewebsites.net`<br/>`san-af-westeurope-prod.azurewebsites.net`<br/>`san-af-uksouth-prod.azurewebsites.net`<br/>`san-af-francecentral-prod.azurewebsites.net`|HTTPS|443|是|Azure Active Directory|Azure Data Studio，Azure 資料 CLI 和 Azure CLI 會連線至 Azure Resource Manager Api，以針對某些功能在 Azure 中傳送和取得資料。|
|**Azure 監視器 Api**|執行 Azure 資料 CLI 或 Azure CLI 的電腦，其正在將監視計量或記錄上傳至 Azure 監視器。|`login.microsoftonline.com`<br/>`management.azure.com`<br/>`*.ods.opinsights.azure.com`<br/>`*.oms.opinsights.azure.com`<br/>`*.monitoring.azure.com`|HTTPS|443|是|Azure Active Directory|Azure Data Studio，Azure 資料 CLI 和 Azure CLI 會連線至 Azure Resource Manager Api，以針對某些功能在 Azure 中傳送和取得資料。|

> [!NOTE]
> 目前，Grafana 和 Kibana 儀表板的所有瀏覽器 HTTPS/443 連線，以及從 Azure Data CLI 至資料控制器 API 的所有瀏覽器，都是使用自我簽署憑證進行 SSL 加密。  未來將提供一項功能，可讓您提供自己的憑證來加密這些 SSL 連線。

從 Azure Data Studio 和 Azure Data CLI 至 Kubernetes API 伺服器的連線，會使用您所建立的 Kubernetes authentication 和加密。  使用 Azure Data Studio 和 Azure 資料 CLI 的每個使用者都必須有已驗證的 Kubernetes API 連線，才能執行與 Azure Arc 啟用的資料服務相關的許多動作。
