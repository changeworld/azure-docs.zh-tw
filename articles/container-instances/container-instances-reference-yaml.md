---
title: 容器組的 YAML 引用
description: Azure 容器實例支援的 YAML 檔的引用，以配置容器組
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896559"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML 引用：Azure 容器實例

本文介紹 Azure 容器實例支援的 YAML 檔的語法和屬性，以配置[容器組](container-instances-container-groups.md)。 使用 YAML 檔將組配置輸入到 Azure CLI 中的[az 容器創建][az-container-create]命令。 

YAML 檔是配置容器組以進行可重現部署的便捷方法。 它是使用[資源管理器範本](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups)或 Azure 容器實例 SDK 創建或更新容器組的簡明替代方法。

> [!NOTE]
> 此引用適用于 Azure 容器實例 REST API 版本的`2018-10-01`YAML 檔。

## <a name="schema"></a>結構描述 

YAML 檔的架構如下，包括突出顯示關鍵屬性的注釋。 有關此架構中屬性的說明，請參閱[屬性值](#property-values)部分。

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>屬性值

下表描述您在結構描述中必須設定的值。

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>微軟.容器實例/容器組物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 容器組的名稱。 |
|  apiVersion | 列舉 | 是 | 2018-10-01 |
|  location | 字串 | 否 | 資源位置。 |
|  tags | 物件 (object) | 否 | 資源標記。 |
|  身分識別 | 物件 (object) | 否 | 容器組的標識（如果已配置）。 - [容器組標識物件](#ContainerGroupIdentity) |
|  properties | 物件 (object) | 是 | [容器組屬性物件](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>容器組標識物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  type | 列舉 | 否 | 用於容器組的標識類型。 類型"系統分配，使用者分配"包括隱式創建的標識和一組使用者分配的身份。 類型"無"將從容器組中刪除任何標識。 - 系統分配、使用者分配、系統分配、使用者分配、無 |
|  userAssignedIdentities | 物件 (object) | 否 | 與容器組關聯的使用者標識的清單。 使用者標識字典金鑰引用將是 Azure 資源管理器資源識別碼，其形式為："/訂閱/訂閱 Id}/資源組/[資源組名稱]/提供程式/Microsoft.託管身份/使用者分配 ID/{標識名稱}"。 |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>容器組屬性物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  containers | array | 是 | 容器組中的容器。 - [容器物件](#Container) |
|  圖像註冊憑據 | array | 否 | 從中創建容器組的映射註冊表憑據。 - [圖像註冊憑據物件](#ImageRegistryCredential) |
|  重新開機策略 | 列舉 | 否 | 重新開機容器組內所有容器的策略。 - `Always`始終重新開機`OnFailure`- 在發生故障`Never`時重新開機 - 切勿重新開機。 - 總是，失敗，從不 |
|  ipAddress | 物件 (object) | 否 | 容器組的 IP 位址類型。 - [Ip位址物件](#IpAddress) |
|  osType | 列舉 | 是 | 容器組中容器所需的作業系統類型。 - 視窗或Linux |
|  磁碟區 | array | 否 | 可在此容器組中的容器裝載的卷的清單。 - [卷物件](#Volume) |
|  診斷 | 物件 (object) | 否 | 容器組的診斷資訊。 - [容器組診斷物件](#ContainerGroupDiagnostics) |
|  網路設定檔 | 物件 (object) | 否 | 容器組的網路設定檔資訊。 - [容器組網路設定檔物件](#ContainerGroupNetworkProfile) |
|  dnsConfig | 物件 (object) | 否 | 容器組的 DNS 配置資訊。 - [Dns 設定物件](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>容器物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 容器實例的使用者提供的名稱。 |
|  properties | 物件 (object) | 是 | 容器實例的屬性。 - [容器屬性物件](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>圖像註冊憑據物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  伺服器 | 字串 | 是 | 沒有"HTTP"和"HTTPs"等協定的 Docker 映射註冊表伺服器。 |
|  username | 字串 | 是 | 專用註冊表的使用者名。 |
|  密碼 | 字串 | 否 | 專用註冊表的密碼。 |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Ip位址物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  連接埠 | array | 是 | 容器組上公開的埠的清單。 - [埠物件](#Port) |
|  type | 列舉 | 是 | 指定 IP 是公開給公共互聯網還是專用 VNET。 - 公共或私人 |
|  ip | 字串 | 否 | IP 暴露在公共互聯網上。 |
|  dns名稱標籤 | 字串 | 否 | IP 的 Dns 名稱標籤。 |


<a id="Volume" />

### <a name="volume-object"></a>卷物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 磁碟區的名稱。 |
|  azure檔 | 物件 (object) | 否 | Azure 檔卷。 - [Azure 檔卷物件](#AzureFileVolume) |
|  emptyDir | 物件 (object) | 否 | 空目錄卷。 |
|  secret | 物件 (object) | 否 | 機密卷。 |
|  gitRepo | 物件 (object) | 否 | git 存儲庫卷。 - [GitRepoVolume 物件](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>容器組診斷物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  日誌分析 | 物件 (object) | 否 | 容器組日誌分析資訊。 - [日誌分析物件](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>容器組網路設定檔物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  id | 字串 | 是 | 網路設定檔的識別碼。 |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Dns 設定物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  名稱 伺服器 | array | 是 | 容器組的 DNS 伺服器。 - 字串 |
|  搜索域 | 字串 | 否 | 容器組中主機名稱查找的 DNS 搜索域。 |
|  選項 | 字串 | 否 | 容器組的 DNS 選項。 |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>容器屬性物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  image | 字串 | 是 | 用於創建容器實例的圖像的名稱。 |
|  命令 | array | 否 | 以 exec 形式在容器實例中執行的命令。 - 字串 |
|  連接埠 | array | 否 | 容器實例上的暴露埠。 - [容器埠物件](#ContainerPort) |
|  環境變數 | array | 否 | 要在容器實例中設置的環境變數。 - [環境變數物件](#EnvironmentVariable) |
|  resources | 物件 (object) | 是 | 容器實例的資源要求。 - [資源需求物件](#ResourceRequirements) |
|  卷安裝 | array | 否 | 容器實例可用的卷裝載。 - [卷安裝物件](#VolumeMount) |
|  生活調查 | 物件 (object) | 否 | 活度探頭。 - [容器探測物件](#ContainerProbe) |
|  就緒探針 | 物件 (object) | 否 | 就緒探測。 - [容器探測物件](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>埠物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  protocol | 列舉 | 否 | 與埠關聯的協定。 - TCP 或 UDP |
|  連接埠 | integer | 是 | 連接埠號碼。 |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Azure 檔卷物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  共用名稱稱 | 字串 | 是 | 要裝載為卷的 Azure 檔共用的名稱。 |
|  readOnly | boolean | 否 | 指示作為卷裝載的 Azure 檔共用是否為唯讀的標誌。 |
|  storageAccountName | 字串 | 是 | 包含 Azure 檔共用的存儲帳戶的名稱。 |
|  storageAccountKey | 字串 | 否 | 用於訪問 Azure 檔共用的存儲帳戶訪問金鑰。 |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  directory | 字串 | 否 | 目標目錄名稱。 不得包含或以"." 開頭。  如果提供 '.'，則卷目錄將是 git 存儲庫。  否則，如果指定，卷將包含具有給定名稱的子目錄中的 git 存儲庫。 |
|  repository | 字串 | 是 | 存儲庫 URL |
|  修訂 | 字串 | 否 | 提交指定修訂的雜湊值。 |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>日誌分析物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  workspaceId | 字串 | 是 | 用於日誌分析的工作區 ID |
|  工作區鍵 | 字串 | 是 | 日誌分析的工作區金鑰 |
|  日誌類型 | 列舉 | 否 | 要使用的日誌類型。 - 容器洞察或容器實例日誌 |
|  中繼資料 | 物件 (object) | 否 | 日誌分析中繼資料。 |


<a id="ContainerPort" />

### <a name="containerport-object"></a>容器埠物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  protocol | 列舉 | 否 | 與埠關聯的協定。 - TCP 或 UDP |
|  連接埠 | integer | 是 | 容器組中公開的埠號。 |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>環境變數物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 環境變數的名稱。 |
|  value | 字串 | 否 | 環境變數的值。 |
|  安全值 | 字串 | 否 | 安全環境變數的值。 |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>資源需求物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  requests | 物件 (object) | 是 | 此容器實例的資源請求。 - [資源請求物件](#ResourceRequests) |
|  限制 | 物件 (object) | 否 | 此容器實例的資源限制。 - [資源限制物件](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>卷安裝物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 卷裝載的名稱。 |
|  裝載路徑 | 字串 | 是 | 容器中應裝載卷的路徑。 不能包含冒號（:)。 |
|  readOnly | boolean | 否 | 指示卷裝載是否為唯讀的標誌。 |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>容器探測物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  exec | 物件 (object) | 否 | 要探測的執行命令 -[容器Exec物件](#ContainerExec) |
|  HTTPGet | 物件 (object) | 否 | 要探測的 Http 獲取設置 -[容器HttpGet物件](#ContainerHttpGet) |
|  初始延遲秒 | integer | 否 | 初始延遲秒數。 |
|  期間秒 | integer | 否 | 句點秒。 |
|  失敗閾值 | integer | 否 | 故障閾值。 |
|  成功閾值 | integer | 否 | 成功閾值。 |
|  超時秒 | integer | 否 | 超時秒數。 |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>資源請求物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  記憶體InGB | number | 是 | 此容器實例的 GB 中的記憶體請求。 |
|  cpu | number | 是 | 此容器實例的 CPU 請求。 |
|  Gpu | 物件 (object) | 否 | 此容器實例的 GPU 請求。 - [Gpu資源物件](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>資源限制物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  記憶體InGB | number | 否 | 此容器實例的 GB 記憶體限制。 |
|  cpu | number | 否 | 此容器實例的 CPU 限制。 |
|  Gpu | 物件 (object) | 否 | 此容器實例的 GPU 限制。 - [Gpu資源物件](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>容器Exec物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  命令 | array | 否 | 要在容器內執行的命令。 - 字串 |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>容器HttpGet物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  path | 字串 | 否 | 要探測的路徑。 |
|  連接埠 | integer | 是 | 要探測的埠號。 |
|  scheme | 列舉 | 否 | 配置。 - HTTP 或 HTTPs |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Gpu資源物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  count | integer | 是 | GPU 資源的計數。 |
|  sku | 列舉 | 是 | GPU 資源的 SKU。 - K80、P100、V100 |


## <a name="next-steps"></a>後續步驟

請參閱教程[使用 YAML 檔部署多容器組](container-instances-multi-container-yaml.md)。

請參閱使用 YAML 檔在[虛擬網路中](container-instances-vnet.md)部署容器組或[裝載外部卷](container-instances-volume-azure-files.md)的示例。

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

