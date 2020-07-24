---
title: 容器群組的 YAML 參考
description: Azure 容器實例所支援的 YAML 檔案參考，以設定容器群組
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084755"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML 參考： Azure 容器實例

本文涵蓋 Azure 容器實例所支援的 YAML 檔案語法和屬性，以設定[容器群組](container-instances-container-groups.md)。 使用 YAML 檔案，將群組設定輸入至 Azure CLI 中的[az container create][az-container-create]命令。 

YAML 檔案是將容器群組設定為可重現部署的便利方式。 這是使用[Resource Manager 範本](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups)或 Azure 容器實例 sdk 來建立或更新容器群組的簡單替代方案。

> [!NOTE]
> 此參考適用于 Azure 容器實例 REST API 版本的 YAML 檔案 `2019-12-01` 。

## <a name="schema"></a>結構描述 

YAML 檔案的架構如下所示，包括醒目提示索引鍵屬性的批註。 如需此架構中屬性的描述，請參閱[屬性值](#property-values)一節。

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
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
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>屬性值

下表描述您在結構描述中必須設定的值。



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.containerinstance/containerGroups 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 容器群組的名稱。 |
|  apiVersion | 列舉 | 是 | 2018-10-01 |
|  location | 字串 | No | 資源位置。 |
|  tags | object | 否 | 資源標記。 |
|  身分識別 | object | 否 | 容器群組的識別（若已設定）。 - [ContainerGroupIdentity 物件](#containergroupidentity-object) |
|  properties | 物件 (object) | 是 | [ContainerGroupProperties 物件](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  類型 | 列舉 | 否 | 用於容器群組的識別類型。 類型 ' SystemAssigned，UserAssigned ' 同時包含隱含建立的身分識別和一組使用者指派的身分識別。 「無」型別會移除容器群組中的任何身分識別。 -SystemAssigned、UserAssigned、SystemAssigned、UserAssigned、None |
|  userAssignedIdentities | object | 否 | 與容器群組相關聯的使用者身分識別清單。 使用者識別字典索引鍵參考將會以下列格式 Azure Resource Manager 資源識別碼： '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '。 |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  containers | array | 是 | 容器群組中的容器。 - [容器物件](#container-object) |
|  imageRegistryCredentials | array | 否 | 用來建立容器群組的映射登錄認證。 - [ImageRegistryCredential 物件](#imageregistrycredential-object) |
|  Restartpolicy： | 列舉 | 否 | 重新開機容器群組中所有容器的原則。 - `Always`一律重新開機- `OnFailure` 失敗時重新開機- `Never` 永不重新開機。 -Always，OnFailure，永不 |
|  ipAddress | object | 否 | 容器群組的 IP 位址類型。 - [IpAddress 物件](#ipaddress-object) |
|  osType | 列舉 | 是 | 容器群組中的容器所需的作業系統類型。 -Windows 或 Linux |
|  磁碟區 | array | 否 | 可由這個容器群組中的容器裝載的磁片區清單。 - [磁片區物件](#volume-object) |
|  診斷 | object | 否 | 容器群組的診斷資訊。 - [ContainerGroupDiagnostics 物件](#containergroupdiagnostics-object) |
|  networkProfile | object | 否 | 容器群組的網路設定檔資訊。 - [ContainerGroupNetworkProfile 物件](#containergroupnetworkprofile-object) |
|  dnsConfig | object | 否 | 容器群組的 DNS 設定資訊。 - [DnsConfiguration 物件](#dnsconfiguration-object) |
| sku | 列舉 | 否 | 容器群組的 SKU-標準或專用 |
| encryptionProperties | object | 否 | 容器群組的加密屬性。 - [EncryptionProperties 物件](#encryptionproperties-object) | 
| initContainers | array | 否 | 容器群組的 init 容器。 - [InitContainerDefinition 物件](#initcontainerdefinition-object) |




### <a name="container-object"></a>容器物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 使用者提供的容器實例名稱。 |
|  properties | 物件 (object) | 是 | 容器實例的屬性。 - [ContainerProperties 物件](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  伺服器 | 字串 | 是 | 沒有通訊協定的 Docker 映射登錄伺服器，例如 "HTTP" 和 "HTTPs"。 |
|  使用者名稱 | 字串 | 是 | 私人登錄的使用者名稱。 |
|  密碼 | 字串 | No | 私人登錄的密碼。 |




### <a name="ipaddress-object"></a>IpAddress 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  連接埠 | array | 是 | 在容器群組上公開的埠清單。 - [埠物件](#port-object) |
|  類型 | 列舉 | 是 | 指定 IP 是否公開至公用網際網路或私人 VNET。 -公用或私用 |
|  ip | 字串 | No | 公開至公用網際網路的 IP。 |
|  dnsNameLabel | 字串 | No | IP 的 Dns 名稱標籤。 |




### <a name="volume-object"></a>磁片區物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 磁碟區的名稱。 |
|  azureFile | object | 否 | Azure 檔案磁片區。 - [AzureFileVolume 物件](#azurefilevolume-object) |
|  emptyDir | object | 否 | 空的目錄磁片區。 |
|  secret | object | 否 | 秘密磁片區。 |
|  gitRepo | object | 否 | Git 存放庫磁片區。 - [GitRepoVolume 物件](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | 否 | 容器群組記錄分析資訊。 - [LogAnalytics 物件](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  id | 字串 | 是 | 網路設定檔的識別碼。 |




### <a name="dnsconfiguration-object"></a>DnsConfiguration 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  nameServers | array | 是 | 容器群組的 DNS 伺服器。 -字串 |
|  searchDomains | 字串 | No | 容器群組中主機名稱查閱的 DNS 搜尋網域。 |
|  選項 | 字串 | No | 容器群組的 DNS 選項。 |


### <a name="encryptionproperties-object"></a>EncryptionProperties 物件

| 名稱  | 類型  | 必要  | 值 |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | 字串    | 是   | Keyvault 基底 url。 |
| keyName   | 字串    | 是   | 加密金鑰名稱。 |
| keyVersion    | 字串    | 是   | 加密金鑰版本。 |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition 物件

| 名稱  | 類型  | 必要  | 值 |
|  ---- | ---- | ---- | ---- |
| NAME  | 字串 |  是 | Init 容器的名稱。 |
| properties    | 物件 (object)    | 是   | Init 容器的屬性。 - [InitContainerPropertiesDefinition 物件](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  image | 字串 | 是 | 用來建立容器實例的映射名稱。 |
|  命令 | array | 否 | 要在 exec 格式的容器實例中執行的命令。 -字串 |
|  連接埠 | array | 否 | 容器實例上已公開的埠。 - [ContainerPort 物件](#containerport-object) |
|  environmentVariables | array | 否 | 要在容器實例中設定的環境變數。 - [EnvironmentVariable 物件](#environmentvariable-object) |
|  resources | object | 是 | 容器實例的資源需求。 - [ResourceRequirements 物件](#resourcerequirements-object) |
|  volumeMounts | array | 否 | 可供容器實例使用的磁片區裝載。 - [VolumeMount 物件](#volumemount-object) |
|  livenessProbe | object | 否 | 活動探查。 - [ContainerProbe 物件](#containerprobe-object) |
|  readinessProbe | object | 否 | 準備就緒探查。 - [ContainerProbe 物件](#containerprobe-object) |




### <a name="port-object"></a>埠物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  protocol | 列舉 | 否 | 與埠相關聯的通訊協定。 -TCP 或 UDP |
|  連接埠 | integer | 是 | 連接埠號碼。 |




### <a name="azurefilevolume-object"></a>AzureFileVolume 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  名 | 字串 | 是 | 要掛接為磁片區的 Azure 檔案共用名稱。 |
|  readOnly | boolean | 否 | 此旗標指出裝載為磁片區的 Azure 檔案共用是否為唯讀。 |
|  storageAccountName | 字串 | 是 | 包含 Azure 檔案共用的儲存體帳戶名稱。 |
|  storageAccountKey | 字串 | No | 用來存取 Azure 檔案共用的儲存體帳戶存取金鑰。 |




### <a name="gitrepovolume-object"></a>GitRepoVolume 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  directory | 字串 | No | 目標目錄名稱。 不得包含或以 '.. ' 開頭。  如果提供 '. '，磁片區目錄將會是 git 存放庫。  否則，如果有指定，磁片區將會在具有指定名稱的子目錄中包含 git 存放庫。 |
|  repository | 字串 | 是 | 存放庫 URL |
|  revision | 字串 | No | 認可指定修訂的雜湊。 |



### <a name="loganalytics-object"></a>LogAnalytics 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  workspaceId | 字串 | 是 | Log analytics 的工作區識別碼 |
|  workspaceKey | 字串 | 是 | Log analytics 的工作區金鑰 |
|  logType | 列舉 | 否 | 要使用的記錄類型。 -ContainerInsights 或 ContainerInstanceLogs |
|  中繼資料 | object | 否 | Log analytics 的中繼資料。 |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition 物件

| 名稱  | 類型  | 必要  | 值 |
|  ---- | ---- | ---- | ---- |
| image | 字串    | No    | Init 容器的影像。 |
| 命令   | array | 否    | 要在 exec 容器內執行的命令。 -字串 |
| environmentVariables | array  | 否 |要在 init 容器中設定的環境變數。 - [EnvironmentVariable 物件](#environmentvariable-object)
| volumeMounts |array   | 否    | 可供 init 容器使用的磁片區裝載。 - [VolumeMount 物件](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  protocol | 列舉 | 否 | 與埠相關聯的通訊協定。 -TCP 或 UDP |
|  連接埠 | integer | 是 | 在容器群組內公開的埠號碼。 |




### <a name="environmentvariable-object"></a>EnvironmentVariable 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 環境變數的名稱。 |
|  value | 字串 | No | 環境變數的值。 |
|  secureValue | 字串 | No | 安全環境變數的值。 |




### <a name="resourcerequirements-object"></a>ResourceRequirements 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  requests | object | 是 | 此容器實例的資源要求。 - [ResourceRequests 物件](#resourcerequests-object) |
|  限制 | object | 否 | 此容器實例的資源限制。 - [ResourceLimits 物件](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  NAME | 字串 | 是 | 磁片區掛接的名稱。 |
|  mountPath | 字串 | 是 | 容器內應裝載磁片區的路徑。 不能包含冒號（:)。 |
|  readOnly | boolean | 否 | 指出磁片區掛接是否為唯讀的旗標。 |




### <a name="containerprobe-object"></a>ContainerProbe 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  exec | object | 否 | [ContainerExec 物件](#containerexec-object)的執行命令 |
|  HTTPGet | object | 否 | 探查[ContainerHttpGet 物件](#containerhttpget-object)的 Http Get 設定 |
|  initialDelaySeconds | integer | 否 | 初始延遲秒數。 |
|  periodSeconds | integer | 否 | 句點秒數。 |
|  failureThreshold | integer | 否 | 失敗閾值。 |
|  successThreshold | integer | 否 | 成功閾值。 |
|  timeoutSeconds | integer | 否 | 超時秒數。 |




### <a name="resourcerequests-object"></a>ResourceRequests 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 是 | 此容器實例的記憶體要求（以 GB 為單位）。 |
|  cpu | number | 是 | 此容器實例的 CPU 要求。 |
|  gpu | object | 否 | 此容器實例的 GPU 要求。 - [GpuResource 物件](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 否 | 此容器實例的記憶體限制（以 GB 為單位）。 |
|  cpu | number | 否 | 此容器實例的 CPU 限制。 |
|  gpu | object | 否 | 此容器實例的 GPU 限制。 - [GpuResource 物件](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  命令 | array | 否 | 要在容器內執行的命令。 -字串 |




### <a name="containerhttpget-object"></a>ContainerHttpGet 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  path | 字串 | No | 要探查的路徑。 |
|  連接埠 | integer | 是 | 要探查的埠號碼。 |
|  scheme | 列舉 | 否 | 配置。 -HTTP 或 HTTPs |




### <a name="gpuresource-object"></a>GpuResource 物件

|  名稱 | 類型 | 必要 | 值 |
|  ---- | ---- | ---- | ---- |
|  count | integer | 是 | GPU 資源的計數。 |
|  sku | 列舉 | 是 | GPU 資源的 SKU。 -K80、P100、V100 |


## <a name="next-steps"></a>後續步驟

請參閱[使用 YAML 檔案部署多容器群組](container-instances-multi-container-yaml.md)教學課程。

請參閱使用 YAML 檔案在[虛擬網路](container-instances-vnet.md)中部署容器群組或[裝載外部磁片](container-instances-volume-azure-files.md)區的範例。

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

