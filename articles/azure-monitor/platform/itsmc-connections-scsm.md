---
title: 將 SCSM 與 IT 服務管理連接器連線
description: 本文提供有關如何使用 IT 服務管理連接器 SCSM 的資訊， (ITSMC) 在 Azure 監視器中，以集中監視及管理 ITSM 工作專案。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 79706b66dba46253843b1f53a26481170d6ff723
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729627"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>使用 IT 服務管理連接器連接 System Center Service Manager

本文提供有關如何設定 System Center Service Manager 實例與 IT 服務管理連接器之間的連線 (ITSMC) 在 Log Analytics 中，以集中管理您的工作專案。

下列各節提供有關如何將 System Center Service Manager 產品連線到 Azure 中的 ITSMC 之詳細資料。

## <a name="prerequisites"></a>Prerequisites

請確保已符合下列必要條件︰

- 已安裝 ITSMC。 詳細資訊：[新增 IT 服務管理連接器解決方案](./itsmc-definition.md)。
- 已部署及設定 Service Manager Web 應用程式 (Web 應用程式)。 Web 應用程式的相關在[這裡](#create-and-deploy-service-manager-web-app-service)。
- 已建立及設定的混合式連線。 詳細資訊：[設定混合式連線](#configure-the-hybrid-connection)。
- Service Manager 的支援版本：2012 R2 或 2016。
- 使用者角色：[進階操作員](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10))。
- 現在，從 Azure 監視器傳送的警示可在 System Center Service Manager 事件中建立。

> [!NOTE]
> - ITSM Connector 只能連線到雲端式 ServiceNow 執行個體。 目前不支援內部部署 ServiceNow 執行個體。
> - 為了將自訂 [範本](./itsmc-definition.md#template-definitions) 作為動作的一部分，SCSM 範本中的參數 "ProjectionType" 應該對應至 "IncidentManagement！ProjectionType "（系統）

## <a name="connection-procedure"></a>連線程序

您可以使用下列程序，將 System Center Service Manager 執行個體連線到 ITSMC：

1. 在 Azure 入口網站中，移至 [所有資源]，然後尋找 **ServiceDesk(YourWorkspaceName)**

2. 在 [工作區資料來源] 下方，按一下 [ITSM 連線]。

    ![新增連線](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格頂端按一下 [新增]。

4. 提供下表中所述的資訊，然後按一下 [確定] 來建立連線。

> [!NOTE]
> 這些全部都是必要參數。

| **欄位** | **說明** |
| --- | --- |
| **連接名稱**   | 輸入您想要與 ITSMC 連線之 System Center Service Manager 執行個體的名稱。  稍後當您設定這個執行個體的工作項目/檢視詳細的記錄分析時，會使用這個名稱。 |
| **夥伴類型**   | 選取 **System Center Service Manager**。 |
| **伺服器 URL**   | 輸入 Service Manager Web 應用程式的 URL。 Service Manager Web 應用程式的相關詳細資訊在[這裡](#create-and-deploy-service-manager-web-app-service)。
| **用戶端識別碼**   | 將您所產生 (使用自動指令碼) 用來驗證 Web 應用程式的用戶端識別碼輸入。 自動化指令碼的相關詳細資訊在[這裡](./itsmc-service-manager-script.md)。|
| **用戶端祕密**   | 輸入針對此識別碼產生的用戶端祕密。   |
| **同步資料**   | 選取您想要透過 ITSMC 同步的 Service Manager 工作項目。  系統會將這些工作項目匯入 Log Analytics。 **選項︰** 事件、變更要求。|
| **資料同步範圍** | 輸入您想要起算資料的過去天數。 **上限**：120 天。 |
| **在 ITSM 解決方案中建立新的設定項目** | 如果您想要在 ITSM 產品中建立設定項目，請選取此選項。 選取時，Log Analytics 會在支援的 ITSM 系統中建立受影響的 CI 作為設定項目 (如果 CI 不存在)。 **預設**︰停用。 |

![Service Manager 連線](media/itsmc-connections/service-manager-connection.png)

**順利連線並同步處理時**︰

- 從 Service Manager 選取的工作項目會匯入到 Azure **Log Analytics**。 您可以在 [IT 服務管理連接器] 圖格上檢視這些工作項目的摘要。

- 您可以在這個 Service Manager 執行個體中建立來自 Log Analytics 警示、記錄檔記錄或 Azure 警示的事件。

深入了解：[從 Azure 警示建立 ITSM 工作項目](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

## <a name="create-and-deploy-service-manager-web-app-service"></a>建立及部署 Service Manager Web 應用程式服務

為了將內部部署 Service Manager 與 Azure 中的 ITSMC 連線，Microsoft 已在 GitHub 上建立 Service Manager Web 應用程式。

若要為您的 Service Manager 設定 ITSM Web 應用程式，請執行下列作業︰

- **部署 Web 應用程式** – 部署 Web 應用程式、設定屬性，以及驗證 Azure AD。 您可以使用 Microsoft 所提供給您的[自動化指令碼](./itsmc-service-manager-script.md)來部署 Web 應用程式。
- **設定混合式連線** - [以手動方式設定此連線](#configure-the-hybrid-connection)。

### <a name="deploy-the-web-app"></a>部署 Web 應用程式
使用自動化[指令碼](./itsmc-service-manager-script.md)來部署 Web 應用程式、設定屬性，以及驗證 Azure AD。

提供下列必要的詳細資料來執行指令碼︰

- Azure 訂用帳戶詳細資料
- 資源群組名稱
- Location
- Service Manager 伺服器詳細資料 (伺服器名稱、網域、使用者名稱和密碼)
- Web 應用程式的網站名稱前置詞
- 服務匯流排命名空間。

指令碼會使用您指定的名稱 (與其他可使它成為唯一的字串) 來建立 Web 應用程式。 它會產生 **Web 應用程式 URL**、**用戶端識別碼** 和 **用戶端祕密**。

將值儲存，當您使用 ITSMC 建立連線時會用到這些值。

**檢查 Web 應用程式安裝**

1. 移至 [Azure 入口網站] > [資源]。
2. 選取 Web 應用程式，按一下 [設定] > [應用程式設定]。
3. 確認您在透過指令碼部署應用程式時所提供的 Service Manager 執行個體之相關資訊。

## <a name="configure-the-hybrid-connection"></a>設定混合式連線

您可以使用下列程序，設定將 Service Manager 執行個體與 Azure 中的 ITSMC 連線之混合式連線。

1. 在 **Azure 資源** 下，尋找 Service Manager Web 應用程式。
2. 按一下 [設定] > [網路]。
3. 在 [混合式連線] 下，按一下 [設定混合式連線端點]。

    ![混合式連線網路](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. 在 [混合式連線] 刀鋒視窗上，按一下 [新增混合式連線]。

    ![混合式連線新增](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. 在 [新增混合式連線] 刀鋒視窗上，按一下 [建立新的混合式連線]。

    ![新增混合式連線](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. 輸入下列值：

   - **端點名稱**：指定新混合式連線的名稱。
   - **端點主機**︰Service Manager 管理伺服器的 FQDN。
   - **端點連接埠**：輸入 5724
   - **服務匯流排命名空間**：使用現有的或建立一個新的服務匯流排命名空間。
   - **位置**：選取位置。
   - **Name**：如果您要建立服務匯流排，請為它指定名稱。

     ![混合式連線值](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. 按一下 [確定] 將 [建立混合式連線] 刀鋒視窗關閉，然後開始建立混合式連線。

    建立混合式連線之後，它會顯示在刀鋒視窗下。

7. 建立混合式連線之後，請選取連線，然後按一下 [新增所選的混合式連線]。

    ![新增混合式連線](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>設定接聽程式設定

您可以使用下列程序來設定混合式連線的接聽程式設定。

1. 在 [混合式連線] 刀鋒視窗中，按 [下載連線管理員]，並將它安裝在執行 System Center Service Manager 執行個體的電腦上。

    一旦安裝完成後，可在 [啟動] 功能表下使用 [混合式連線管理員 UI]選項。

2. 按一下 [混合式連線管理員 UI]，系統會提示您輸入 Azure 認證。

3. 使用您的 Azure 認證登入，然後選取您在其中建立混合式連線的訂用帳戶。

4. 按一下 [檔案] 。

混合式連線已成功連線。

![混合式連線成功](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> 建立混合式連線之後，可瀏覽已部署的 Service Manager Web 應用程式來驗證及測試連線。 嘗試連線到 Azure 中的 ITSMC 之前，請確定連線成功。

以下範例影像顯示連線成功的詳細資料︰

![混合式連線測試](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>後續步驟

* [ITSM 連接器總覽](itsmc-overview.md)
* [建立 Azure 警示的 ITSM 工作項目](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [針對 ITSM 連接器中的問題進行疑難排解](./itsmc-resync-servicenow.md)