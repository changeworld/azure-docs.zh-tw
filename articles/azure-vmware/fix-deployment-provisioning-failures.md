---
title: 支援 Azure VMware 解決方案部署或布建失敗
description: 從您的 Azure VMware 解決方案私人雲端取得資訊，以針對 Azure VMware 解決方案部署或布建失敗提出服務要求。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 6d609774b0d3a2de7809d04e4fa0c4e3e6593590
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349115"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>開啟 Azure VMware 解決方案部署或布建失敗的支援要求

本文說明如何開啟 [支援要求](https://rc.portal.azure.com/#create/Microsoft.Support) ，並提供 Azure VMware 解決方案部署或布建失敗的重要資訊。 

當您的私人雲端發生失敗時，您需要在 Azure 入口網站中開啟支援要求。 若要開啟支援要求，請先取得 Azure 入口網站中的一些重要資訊：

- 相互關連識別碼
- Azure ExpressRoute 線路識別碼
- 錯誤訊息

## <a name="get-the-correlation-id"></a>取得相互關聯識別碼
 
當您在 Azure 中建立私人雲端或任何資源時，資源的相互關聯識別碼會自動產生。 在您的支援要求中包含私用雲端相互關聯識別碼，以更快速地開啟和解決要求。

在 Azure 入口網站中，您可以透過兩種方式取得資源的相互關聯識別碼：

* **總覽** 窗格
* 部署記錄檔
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>從資源總覽取得相互關聯識別碼

以下是已選取相互關聯識別碼之失敗私用雲端部署的作業詳細資料範例：

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="螢幕擷取畫面，顯示已選取相互關聯識別碼的失敗私用雲端部署。":::

若要在私 **用雲端中** 存取部署結果：

1. 在 [Azure 入口網站中，選取您的私人雲端。

1. 在左側功能表中，選取 **[總覽** ]。

啟動部署之後，部署的結果會顯示在 [私人雲端 **總覽** ] 窗格中。

複製並儲存私人雲端部署相互關聯識別碼，以包含在服務要求中。

### <a name="get-the-correlation-id-from-the-deployment-log"></a>從部署記錄檔取得相互關聯識別碼

您可以搜尋 Azure 入口網站中的部署活動記錄檔，以取得失敗部署的相互關聯識別碼。

若要存取部署記錄：

1. 在 [Azure 入口網站中，選取您的私人雲端，然後選取 [通知] 圖示。

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="顯示 Azure 入口網站中 [通知] 圖示的螢幕擷取畫面。":::

1. 在 [ **通知** ] 窗格中，選取 **活動記錄中的 [更多事件** ]：

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="螢幕擷取畫面，顯示 [通知] 窗格中所選活動記錄連結的更多事件。":::

1. 若要尋找失敗的部署和其相互關聯識別碼，請搜尋資源的名稱或您用來建立資源的其他資訊。 

    下列範例顯示名為 pc03 之私人雲端資源的搜尋結果。
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="顯示範例私用雲端資源和 [建立或更新 PrivateCloud] 窗格之搜尋結果的螢幕擷取畫面。":::
 
1. 在 [ **活動記錄** 檔] 窗格的搜尋結果中，選取失敗部署的作業名稱。

1. 在 [ **建立或更新 PrivateCloud** ] 窗格中，選取 [ **JSON** ] 索引標籤，然後 `correlationId` 在顯示的記錄檔中尋找。 複製 `correlationId` 值，以將其包含在您的支援要求中。 
 
## <a name="copy-error-messages"></a>複製錯誤訊息

若要協助解決您的部署問題，請包含 Azure 入口網站中所顯示的任何錯誤訊息。 選取警告訊息以查看錯誤摘要：
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="在 [錯誤] 窗格的 [摘要] 索引標籤上顯示錯誤詳細資料的螢幕擷取畫面，其中已選取 [複製] 圖示。":::

若要複製錯誤訊息，請選取 [複製] 圖示。 儲存複製的訊息，以包含在您的支援要求中。
 
## <a name="get-the-expressroute-id-uri"></a>取得 (URI 的 ExpressRoute 識別碼) 
 
可能是您嘗試使用私用雲端 ExpressRoute 線路來調整或對等現有的私人雲端進行調整，且失敗。 在該案例中，您需要包含在支援要求中的 ExpressRoute 識別碼。

若要複製 ExpressRoute 識別碼：

1. 在 [Azure 入口網站中，選取您的私人雲端。
1. 在左側功能表的 [ **管理** ] 底下，選取 [ **連接** ]。 
1. 在右窗格中，選取 [ **ExpressRoute** ] 索引標籤。
1. 選取 [ **EXPRESSROUTE 識別碼** ] 的複製圖示，並儲存您的支援要求所要使用的值。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="將 ExpressRoute 識別碼複製到剪貼簿。"::: 
 
## <a name="pre-validation-failures"></a>預先驗證失敗

如果在部署) 之前，您的私用雲端預先驗證檢查失敗 (，就不會產生相互關聯識別碼。 在此案例中，您可以在支援要求中提供下列資訊：

- 錯誤和失敗訊息。 這些訊息在許多失敗中可能很有説明，例如，針對與配額相關的問題。 請務必複製這些訊息，並將它們包含在支援要求中，如本文中所述。
- 您用來建立 Azure VMware 解決方案私用雲端的資訊，包括：
  - 位置
  - 資源群組
  - 資源名稱

## <a name="create-your-support-request"></a>建立您的支援要求

如需有關建立支援要求的一般資訊，請參閱 [如何建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 

若要建立 Azure VMware 解決方案部署或布建失敗的支援要求：

1. 在 [Azure 入口網站中，選取 [說明 **] 圖示，** 然後選取 [ **新增支援要求** ]。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Azure 入口網站中 [新增支援要求] 窗格的螢幕擷取畫面。":::

1. 輸入或選取所需的資訊：

   1. 在 [基本] 索引標籤上：

      1. 針對 **問題類型** ，請選取 **設定和安裝問題** 。

      1. 針對 **問題子類型** ，選取 [布建 **私人雲端** ]。

   1. 在 [ **詳細資料** ] 索引標籤上：

      1. 輸入或選取必要的資訊。

      1. 貼上要求這項資訊的相互關聯識別碼或 ExpressRoute 識別碼。 如果您沒有看到這些值的特定文字方塊，請將其貼到 [ **提供問題的詳細資料** ] 文字方塊中。

    1. 在 [ **提供問題的詳細資料** ] 文字方塊中，貼上任何錯誤詳細資料，包括您所複製的錯誤或失敗訊息。

1. 檢查您的專案，然後選取 [ **建立** ] 以建立您的支援要求。
