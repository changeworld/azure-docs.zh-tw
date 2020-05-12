---
title: 使用 Helm 整合 Kubernetes 部署與 Azure 應用程式組態
description: 了解如何在使用 Helm 的 Kubernetes 部署中使用動態組態。
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 2aebccdf18aaba345beb344a8b6fc3b37754a4a1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793262"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>使用 Helm 與 Kubernetes 部署整合

Helm 提供一種方式來定義、安裝及升級在 Kubernetes 中執行的應用程式。 Helm 圖表包含建立 Kubernetes 應用程式執行個體所需的資訊。 組態會儲存在圖表本身的外部，在名為 values.yaml  的檔案中。 

在發行流程期間，Helm 會將圖表與適當的組態合併，以執行應用程式。 例如，values.yaml  中定義的變數，可以在執行中的容器內，以環境變數的形式來參考。 Helm 也支援建立 Kubernetes 秘密，其可裝載為資料磁碟區或公開為環境變數。

您可以在執行 Helm 時，在命令列上提供其他 YAML 型的設定檔，以覆寫儲存在 values.yaml  中的值。 Azure 應用程式組態支援將組態值匯出至 YAML 檔案。 將此匯出功能整合到您的部署，可讓 Kubernetes 應用程式利用儲存在應用程式組態中的設定值。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 將應用程式部署至使用 Helm 的 Kubernetes 時，請使用應用程式組態中的值。
> * 根據應用程式組態中的 Key Vault 參考，建立 Kubernetes 秘密。

本教學課程假設您已具備使用 Helm 管理 Kubernetes 的基本知識。 在 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/kubernetes-helm) 中使用 Helm 安裝應用程式。

## <a name="prerequisites"></a>Prerequisites

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (2.4.0 版或更新版本)
- 安裝 [Helm](https://helm.sh/docs/intro/install/) (2.14.0 版或更新版本)
- Kubernetes 叢集。

## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 選取 [組態總管]   > [建立]  以新增下列索引鍵/值組：

    | Key | 值 |
    |---|---|
    | settings.color | 白色 |
    | settings.message | Azure 應用程式設定的值 |

    目前先讓 [標籤]  和 [內容類型]  保持空白。

## <a name="add-a-key-vault-reference-to-app-configuration"></a>將 Key Vault 參考新增至應用程式組態
1. 登入 [Azure 入口網站](https://portal.azure.com) 並將秘密新增至 [Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)，包括名稱 **Password** 和值 **myPassword**。 
2. 選取您上一節中建立的應用程式組態存放區執行個體。

3. 選取 [組態總管]  。

4. 選取 [+ 建立]   > [金鑰保存庫參考]  ，然後指定下列值：
    - **金鑰**：選取 **secrets.password**。
    - **標籤**︰將此值保留空白。
    - **訂用帳戶**、**資源群組**與**金鑰保存庫**：輸入與您在上一步中建立之金鑰保存庫對應的值。
    - **祕密**：選取您在上一節中所建立，名為 **Password** 的祕密。

## <a name="create-helm-chart"></a>建立 Helm 圖表 ##
首先，使用下列命令建立範例 Helm 圖表
```console
helm create mychart
```

Helm 會使用如下所示的結構，建立名為 mychart 的新目錄。 

> [!TIP]
> 若要深入了解，請遵循本[圖表指南](https://helm.sh/docs/chart_template_guide/getting_started/)。

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

接下來，更新 deployment.yaml  檔案的 **spec:template:spec:containers** 區段。 下列程式碼片段會將兩個環境變數新增至容器。 您會在部署時動態設定其值。

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

更新後，完整的 deployment.yaml  檔案應該如下所示。

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

若要將機密資料儲存為 Kubernetes 秘密，請在範本資料夾下新增 secrets.yaml  檔案。

> [!TIP]
> 深入了解如何使用 [Kubernetes 祕密](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)。

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password }}
```

最後，請使用下列內容來更新 values.yaml  檔案，以選擇性地提供組態設定的預設值，以及 deployment.yaml  和 secrets.yaml  檔案中所參考的祕密。 從應用程式組態提取的組態將會覆寫其實際值。

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>在 Helm 安裝中從應用程式組態傳遞組態 ##
首先，將組態從應用程式組態下載至 myConfig.yaml  檔案。 使用金鑰篩選器，只下載開頭為**設定**的金鑰。 如果您摸金鑰篩選不足以排除 Key Vault 參考的金鑰，可以使用引數 **--skip-keyvault** 將其排除。 

> [!TIP]
> 深入了解 [export 命令](https://docs.microsoft.com/cli/azure/appconfig/kv?view=azure-cli-latest#az-appconfig-kv-export)。 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

接下來，將秘密下載到名為 mySecrets.yaml  的檔案。 命令列引數 **--resolve-keyvault** 會藉由擷取 Key Vault 中的實際值來解析 Key Vault 參考。 您必須使用具有對應 Key Vault 之存取權限的認證來執行此命令。

> [!WARNING]
> 當此檔案包含機密資訊時，請小心處理檔案，並在不再需要時加以清除。

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

使用 Helm 升級的 **-f** 引數來傳入您所建立的兩個組態檔。 這兩個組態檔會使用從應用程式組態中匯出的值，覆寫 values.yaml  中所定義的組態值。

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

您也可以使用 **--set** 引數升級 Helm，以傳遞常值金鑰值。 使用 **--set** 引數是避免將敏感性資料保存到磁碟的好方法。 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

存取 [Kubernetes 儀表板](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)，確認已成功設定組態和祕密。 您會看到來自應用程式組態的**色彩**和**訊息**值已填入容器的環境變數中。

![快速入門應用程式啟動本機](./media/kubernetes-dashboard-env-variables.png)

儲存為應用程式組態中 Key Vault 參考的祕密，**Password**，也已新增至 Kubernetes 祕密。 

![快速入門應用程式啟動本機](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已匯出要透過 Helm Kubernetes 部署中使用的 Azure 應用程式組態資料。 若要深入了解「應用程式組態」的使用方式，請繼續進行 Azure CLI 範例。

> [!div class="nextstepaction"]
> [Azure CLI](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
