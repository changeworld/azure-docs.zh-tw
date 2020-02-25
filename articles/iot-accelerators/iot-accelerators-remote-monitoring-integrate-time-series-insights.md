---
title: 整合時間序列深入解析與遠端監視 - Azure | Microsoft Docs
description: 在此操作說明中，您將學習如何為尚未包含時間序列見解的現有遠端監視解決方案設定時間序列見解。
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2020
ms.locfileid: "77564639"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>整合 Azure 時間序列深入解析與遠端監視

Azure 時間序列深入解析是完全受管理的分析、儲存及視覺化服務，可讓您在雲端上管理 IoT 規模的時間序列資料。 您可以使用時間序列見解來儲存及管理時間序列資料、同時探索多個事件並將其視覺化、執行根本原因分析以及比對多個網站與資產。

遠端監視解決方案加速器現在提供時間序列見解的自動部署和整合。 在此操作說明中，您將學習如何為尚未包含時間序列見解的現有遠端監視解決方案設定時間序列見解。

> [!NOTE]
> Azure China 雲端目前不提供時間序列見解。 Azure China 雲端中新的遠端監視解決方案加速器部署，將 Cosmos DB 用於所有儲存體。

## <a name="prerequisites"></a>Prerequisites

若要完成此操作說明，您必須已部署遠端監視解決方案：

* [部署遠端監視解決方案加速器](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>建立取用者群組

在要用來將資料串流處理到時間序列見解的 IoT 中樞，建立專屬的取用者群組。

> [!NOTE]
> 應用程式會使用取用者群組從 Azure IoT 中樞提取資料。 每個取用者群組最多允許五個輸出取用者。 您應該針對每五個輸出接收建立一個新的取用者群組，而您最多可以建立 32 個取用者群組。

1. 在 Azure 入口網站中，按一下 [Cloud Shell] 按鈕。

1. 執行下列命令來建立新的取用者群組。 在遠端監視部署中使用 IoT 中樞的名稱，並將遠端監視部署的名稱用作資源群組名稱：

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>部署時間序列見解

接下來，以額外資源的形式將時間序列見解部署到遠端監視解決方案，並將其連線到 IoT 中樞。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 [建立資源] > [物聯網] > [時間序列深入解析]。

    ![新增時間序列深入解析](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. 若要建立時間序列深入解析環境，請使用下表中的值：

    | 設定 | 值 |
    | ------- | ----- |
    | 環境名稱 | 下列螢幕擷取畫面會使用名稱 **contorosrmtsi**。 當您完成此步驟時，請選擇您自己的唯一名稱。 |
    | 訂用帳戶 | 在下拉式清單中選取您的 Azure 訂用帳戶。 |
    | 資源群組 | **使用現有項目**。 選取現有遠端監視資源群組的名稱。 |
    | Location | 我們使用**美國東部**。 如果可能的話，請在與遠端監視解決方案相同的區域中建立環境。 |
    | SKU |**S1** |
    | Capacity | **1** |

    ![建立時間序列深入解析](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. 按一下 **[建立]** 。 可能需要一點時間來建立環境。

## <a name="create-event-source"></a>建立事件來源

建立新的事件來源以連線到 IoT 中樞。 確定您會使用先前步驟中所建立的取用者群組。 時間序列深入解析要求每個服務都要具備其他服務未使用的專屬取用者群組。

1. 瀏覽至新的時間序列見解環境。

1. 選取左側的 [事件來源]。

    ![檢視事件來源](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. 按一下 [新增]。

    ![新增事件來源](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. 若要設定您的 IoT 中樞作為新的事件來源，請使用下表中的值：

    | 設定 | 值 |
    | ------- | ----- |
    | 事件來源名稱 | 下列螢幕擷取畫面會使用名稱 **contosorm-iot-hub**。 當您完成此步驟時，請使用您自己的唯一名稱。 |
    | 來源 | **IoT 中心** |
    | 匯入選項 | **從可用的訂用帳戶使用 IoT 中樞** |
    | 訂用帳戶識別碼 | 在下拉式清單中選取您的 Azure 訂用帳戶。 |
    | IoT 中樞名稱 | **contosorma57a6**。 從您的遠端監視解決方案使用 IoT 中樞的名稱。 |
    | IoT 中樞原則名稱 | **iothubowner** 確定所使用的原則是擁有者原則。 |
    | IoT 中樞原則金鑰 | 系統會自動填入此欄位。 |
    | IoT 中樞取用者群組 | **timeseriesinsights** |
    | 事件序列化格式 | **JSON**     | 
    | 時間戳記屬性名稱 | 保留空白 |

    ![建立事件來源](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. 按一下 **[建立]** 。

## <a name="configure-the-data-access-policy"></a>設定資料存取原則

為確保有權存取遠端監視解決方案的所有使用者都能夠在時間序列見解總管中瀏覽資料，請在 Azure 入口網站中的資料存取原則下新增應用程式和使用者。 

1. 在導覽清單中，選擇 [資源群組]。

1. 選擇 [ContosoRM] 資源群組。

1. 在 Azure 資源清單中，選擇 [contosormtsi]。

1. 選擇 [資料存取原則]，以查看目前的角色指派清單。

1. 選擇 [新增] 以開啟 [選取使用者規則] 窗格。

   如果您沒有指派角色的權限，就看不到 [新增] 選項。

1. 在 [角色] 下拉式清單中，選取 [讀者] 及 [參與者] 等角色。

1. 在 [選取] 清單中，選取使用者、群組或應用程式。 如果在清單中未看到安全性主體，您可以在 [選取] 方塊中輸入，以在目錄中搜尋顯示名稱、電子郵件地址和物件識別碼。

1. 選擇 [儲存] 以建立角色指派。 在幾分鐘之後，即會在資料存取原則中指派安全性主體的角色。

> [!NOTE]
> 如果您需要為其他使用者授與存取時間序列深入解析總管的權限，則可以使用下列步驟來[授與資料存取](../time-series-insights/time-series-insights-data-access.md#grant-data-access)。

## <a name="configure-azure-stream-analytics"></a>設定 Azure 串流分析 

下一個步驟是設定 Azure 串流分析管理員微服務，以停止傳送訊息至 Cosmos DB，並僅將其儲存在時間序列見解中。 如果您想在 Cosmos DB 中複製訊息，請跳過此步驟。

1. 在導覽清單中，選擇 [資源群組]。

1. 選擇 [ContosoRM] 資源群組。

1. 在資源清單中尋找 Azure 串流分析 (ASA) 的資料流處理工作。 資源名稱的開頭為 **streamingjobs-** 。

1. 在頂端按一下按鈕以停止 ASA 資料流處理工作。

1. 編輯 ASA 查詢，並刪除指向 Cosmos DB 中訊息流的 **SELECT**、**INTO** 及 **FROM** 子句。 這些子句應位於查詢的底部，如下列範例所示：

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. 重新啟動 Azure 串流分析的串流處理作業。

7. 請在命令提示字元中鍵入下列命令，以將最新變更提取至 Azure 串流分析管理員微服務：

.NET： 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java：

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>設定遙測微服務

請在命令提示字元中鍵入下列命令，以提取最新的遙測微服務：

.NET：

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java：

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>[選擇性] 設定 Web UI 以連結至時間序列見解總管

若要在時間序列見解總管中輕鬆地檢視資料，建議您自訂 UI 以輕鬆地連結至環境。 為此，請使用下列命令將最新變更提取至 Web UI ：

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>設定環境變數

若要完成時間序列見解整合，您必須為已更新的微服務設定部署環境。

### <a name="basic-deployments"></a>基本部署

為已更新的微服務設定 `basic` 部署環境。

1. 在 Azure 入口網站中，按一下左方窗格的 [Azure Active Directory] 索引標籤。

1. 按一下 [應用程式註冊]。

1. 搜尋並按一下您的 **ContosoRM** 應用程式。

1. 巡覽至 [設定] > [金鑰]，然後為您的應用程式建立新金鑰。 請確保將金鑰值複製到安全的位置。

1. 使用最新標記從 GitHub 存放庫中提取[最新的 docker compose yaml 檔案](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm)。 

1. 遵循[如何建立及使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)中所述的步驟來透過 SSH 連線至 VM。

1. 連線後，請鍵入 `cd /app`。

1. 將下列環境變數新增至 docker compose yaml 檔案中每個微服務和 VM 中的 `env-setup` 指令碼：

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. 瀏覽至 [遙測服務]，並透過新增與上述相同的環境變數來編輯 docker compose 檔案。

1. 瀏覽至 [ASA 管理員服務]，並透過新增 `PCS_TELEMETRY_STORAGE_TYPE` 來編輯 docker compose 檔案。

1. 從 VM 中使用 `sudo ./start.sh` 來重新啟動 Docker 容器。

> [!NOTE]
> 上述環境變數設定在1.0.2 之前會對遠端監視版本有效

### <a name="standard-deployments"></a>標準部署

為以上已更新的微服務設定 `standard` 部署環境

1. 在命令列上執行 `kubectl proxy`。 如需詳細資訊，請參閱[存取 Kubernetes API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server)。

1. 開啟 Kubernetes 管理主控台。

1. 尋找設定對應來為 TSI 新增下列新環境變數：

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. 編輯遙測服務 Pod 的 yaml 檔案範本：

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. 編輯 ASA 管理員服務 Pod 的 yaml 檔案範本：

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何在時間序列見解總管中瀏覽資料和診斷警示，請參閱[進行根本原因分析](iot-accelerators-remote-monitoring-root-cause-analysis.md)的教學課程。

* 若要深入了解如何在時間序列見解總管中探索和查詢資料，請參閱關於 [Azure 時間序列見解總管](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)的文件。
