---
title: 為自我裝載的 Azure API 管理閘道設定自訂功能變數名稱 |Microsoft Docs
description: 本主題說明為自我裝載的 Azure API 管理閘道設定自訂功能變數名稱的步驟。
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: dacf1329d35117c65bcc48a82ac27a767ebd2b3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82203160"
---
# <a name="configure-a-custom-domain-name"></a>設定自訂網域名稱

當您布[建自我裝載的 AZURE API 管理閘道](self-hosted-gateway-overview.md)時，它不會指派主機名稱，且必須由其 IP 位址參考。 本文說明如何將現有的自訂 DNS 名稱（也稱為主機名稱）對應到自我裝載的閘道。

## <a name="prerequisites"></a>必要條件

若要執行本文所述的步驟，您必須具有：

-   有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理實例。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
- 自我裝載的閘道。 如需詳細資訊，請參閱[如何布建自我裝載閘道](api-management-howto-provision-self-hosted-gateway.md)
-   您或您的組織所擁有的自訂功能變數名稱。 本主題不提供如何購買自訂功能變數名稱的指示。
-   Dns 伺服器上裝載的 DNS 記錄，可將自訂功能變數名稱對應至自我裝載閘道的 IP 位址。 本主題不提供如何裝載 DNS 記錄的指示。
-   您必須具備一個含有公開和私密金鑰的有效憑證 (.PFX)。 主體或主體別名（SAN）必須符合功能變數名稱（這可讓 API 管理實例透過 TLS 安全地公開 Url）。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>將自訂網域憑證新增至您的 API 管理服務

1. 從 [**安全性**] 底下選取 [**憑證**]。
2. 選取 [+ 新增] 。
3. 在 [**識別碼**] 欄位中輸入 [憑證的資源名稱]。
4. 選取包含憑證的檔案（。PFX），方法是選取 [**憑證**] 欄位或其旁邊的資料夾圖示。
5. 在 [**密碼**] 欄位中輸入憑證的密碼。
6. 選取 [**建立**]，將憑證新增至您的 API 管理服務。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>使用 Azure 入口網站設定自我裝載閘道的自訂功能變數名稱

1. 從 [**設定**] 底下選取**閘道**。
2. 選取您想要為其設定功能變數名稱的自我裝載閘道。
3. 選取 [**設定**] 底下的 [**主機名稱**]。
4. 選取 [+ 新增]
5. 在 [**名稱**] 欄位中輸入主機名稱的資源名稱。
6. 在 [**主機名稱**] 欄位中輸入功能變數名稱。
7. 從 [**憑證**] 下拉式清單中選取憑證。
8. 如果任何透過此閘道公開的 Api 使用用戶端憑證驗證，請選取 [**協商用戶端憑證**] 核取方塊。
    > [!WARNING]
    > 這項設定是由針對閘道設定的所有功能變數名稱所共用。
9. 選取 [**新增**]，將自訂功能變數名稱指派給選取的自我裝載閘道。

## <a name="next-steps"></a>後續步驟

[升級和調整服務的規模](upgrade-and-scale.md)
