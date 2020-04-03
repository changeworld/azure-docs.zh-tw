---
title: Azure HDInsight 建立叢集 - 錯誤字典
description: 瞭解如何排除建立 Azure HDInsight 叢集時出現的錯誤
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 803783eddfbffd5c3dbab7353ee00dd7f11a09e5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618891"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight:叢集建立錯誤

本文介紹了創建群集時可能會遇到的錯誤的解決方法。

> [!NOTE]
> 本文中描述的前三個錯誤是驗證錯誤。 當 Azure HDInsight 產品使用**CsmDocument_2_0**類時,可能會發生此類操作。

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤代碼:部署文件「CsmDocument_2_0」驗證失敗

### <a name="error"></a>錯誤

"無法存取文本操作位置 URI:SCRIPT\<\>操作 URL"

#### <a name="error-message"></a>錯誤訊息

遠端伺服器返回了一個錯誤:(404) 未找到。

### <a name="cause"></a>原因

HDInsight 服務無法存取您在創建群集請求中提供的文稿操作 URL。 當服務嘗試訪問腳本操作時,它會收到前面的錯誤消息。

### <a name="resolution"></a>解決方案

- 對於 HTTP 或 HTTPS URL,請透過嘗試從隱身瀏覽器視窗轉到 URL 來驗證 URL。
- 對於 WASB URL,請確保腳本存在於您在請求中給出的存儲帳戶中。 還要確保此存儲帳戶的存儲金鑰正確。
- 對於 ADLS URL,請確保該腳本存在於存儲帳戶中。

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤代碼:部署文件「CsmDocument_2_0」驗證失敗

### <a name="error"></a>錯誤

無法訪問腳本操作位置 URI:SCRIPT_ACTION_URL" \< \>

#### <a name="error-message"></a>錯誤訊息

給定的文稿\<\>URI SCRIPT_URI位於 ADLS 中,但此群集沒有資料湖儲存主體

### <a name="cause"></a>原因

HDInsight 服務無法存取您在創建群集請求中提供的文稿操作 URL。 當服務嘗試訪問腳本操作時,它會收到前面的錯誤消息。

### <a name="resolution"></a>解決方案

將相應的 Azure 資料儲存庫存儲第 1 代帳戶添加到群集。 還將訪問數據存儲湖存儲第 1 代帳戶的服務主體添加到群集中。

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤代碼:部署文件「CsmDocument_2_0」驗證失敗

### <a name="error"></a>錯誤

請求中提供的\<「VM\>大小」CUSTOMER_SPECIFIED_VM_SIZE」 對於角色"ROLE"\<\>無效或不支援。 有效值為:VALID_VM_SIZE_FOR_ROLE。 \< \>

### <a name="cause"></a>原因

不允許角色指定虛擬機器大小。 可能發生此錯誤,因為 VM 大小值不按預期工作或不適合電腦角色。

### <a name="resolution"></a>解決方案

錯誤訊息列出 VM 大小的有效值。 選擇這些值之一,然後重試創建群集請求。

---

## <a name="error-codeinvalidvirtualnetworkid"></a>錯誤代碼:不合法網路 Id  

### <a name="error"></a>錯誤

「虛擬網路 Id 無效。 虛擬網络\<\>Id ' USER_VIRTUALNETWORKID "*"

### <a name="cause"></a>原因

在群集創建期間指定的**虛擬網路 Id**值格式不正確。

### <a name="resolution"></a>解決方案

確保**虛擬網路Id**和子網值格式正確。 要取得**虛擬網路代碼**值,可以:

1. 移至 Azure 入口網站。
1. 選擇虛擬網路。
1. 選擇「**屬性」** 選單項。 **資源 ID**屬性值是**虛擬網路 Id**值。

下面是虛擬網路 ID 的範例:

/訂閱/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/資源組/我的資源組/供應商/微軟.網络/虛擬網络/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>錯誤代碼:自訂失敗錯誤代碼

### <a name="error"></a>錯誤

「由於自定義腳本操作中的錯誤,群集部署失敗。 失敗操作:SCRIPT_NAME,\<\>請轉到 Ambari UI 以進一步調試故障。

### <a name="cause"></a>原因

成功部署群集後,將執行在創建群集請求期間提供的自定義腳本。 此錯誤代碼指示在執行名為\<\>SCRIPT_NAME 的自訂文本期間出現錯誤。

### <a name="resolution"></a>解決方案

由於腳本是自定義腳本,因此我們建議您排除故障,並在必要時重新運行該腳本。 要排除腳稿故障,請檢查 /var/lib/ambari 代理/* 資料夾中的日誌。 或者打開 Ambari UI 中的 **「操作」** 頁,然後選擇**run_customscriptaction**操作以查看錯誤詳細資訊。

---

## <a name="error-codeinvaliddocumenterrorcode"></a>錯誤代碼:不合法的文件錯誤代碼

### <a name="error"></a>錯誤

\<\>資料庫\<\<DATABASE_NAME中的META_STORE_TYPE元儲存架構版本METASTORE_MAJOR_VERSION與群集版本CLUSTER_VERSION不相容\> \>\<\>

### <a name="cause"></a>原因

自訂中儲存與所選的 HDInsight 群集版本不相容。 目前,HDInsight 4.0 群集僅支援 Metastore 版本 3.0 及更高版本,而 HDInsight 3.6 群集不支援 Metastore 版本 3.0 及更高版本。

### <a name="resolution"></a>解決方案

僅使用 HDInsight 群集版本支援的 Metastore 版本。 如果不指定自定義元存儲,HDInsight 會在內部創建元存儲,然後在群集刪除後將其刪除。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>錯誤代碼:未與群組錯誤代碼連線 

### <a name="error"></a>錯誤

無法連接到群集管理終結點以執行縮放操作。 驗證網路安全規則是否阻止對群集的外部訪問,以及群集管理員 (Ambari) UI 是否能夠成功存取。

### <a name="cause"></a>原因

網路安全組 (NSG) 上的防火牆規則阻止與關鍵 Azure 運行狀況和管理服務的群集通信。

### <a name="resolution"></a>解決方案

如果您計劃使用網路安全組來控制網路流量,請在安裝 HDInsight 之前執行以下操作:

- 識別您要用於 HDInsight 的 Azure 區域。
- 識別 HDInsight 所需的 IP 位址。 如需詳細資訊，請參閱 [HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
  - 創建或修改計畫安裝 HDInsight 的子網的網路安全組。
  - 對於網路安全組,允許來自IP位址的埠443上的入站流量。 此配置可確保 HDInsight 管理服務可以從虛擬網路外部到達群集。

---

## <a name="error-code-storagepermissionsblockedformsi"></a>錯誤代碼:儲存權限封鎖

### <a name="error"></a>錯誤

託管標識對存儲帳戶沒有許可權。 請驗證「存儲 Blob 資料擁有者」角色是否分配給存儲帳戶的託管標識。 存儲:/訂閱/\<\>訂閱 ID\</資源\>組/資源 組名稱/提供程式/Microsoft.\<存儲/存儲帳戶\>/存儲帳戶 名稱、託管\<標識:/訂閱\>\</訂閱\>ID /資源 組// 資源組名稱/資源組名稱/\<提供程式/Microsoft.託管身份 /使用者分配\>Id 身份/使用者託管標識名稱"

### <a name="cause"></a>原因

您沒有提供管理標識所需的許可權。 使用者分配的託管標識在 Azure 數據湖儲存 Gen2 儲存帳戶上沒有 Blob 儲存參與者角色。

### <a name="resolution"></a>解決方案

1. 開啟 Azure 入口網站。
1. 移至您的儲存體帳戶。
1. 查看**控制 (IAM)** 下。
1. 確保使用者具有存儲 Blob 資料參與者角色或分配給他們的存儲 Blob 數據擁有者角色。

有關詳細資訊,請參閱在[Data Lake 儲存 Gen2 帳號上設定託管識別權限](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>錯誤代碼:無效網路安全群組安全規則

### <a name="error"></a>錯誤

\<網路\>安全組/訂閱/訂閱 ID/ 資源組/<\>資源組名稱 預設/供應商/Microsoft.網路/網路\<安全組/\>網路安全組名稱 中的安全規則配置了子網/\<\>訂閱/訂閱\<ID/\>資源組/ 資源組名稱 RG-westeurope-vnet-tom-預設/提供商\</Microsoft.Network/虛擬網路/ 虛擬網路名稱\>/子網/\<子網名稱\>不允許所需的入站和/或出站連接。 有關詳細資訊,請造[訪 為 Azure HDInsight 規劃虛擬網路](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment),或聯絡支援人員。

### <a name="cause"></a>原因

如果網路安全組或使用者定義的路由 (UDR) 控制到 HDInsight 群集的入站流量,請確保群集可以與關鍵的 Azure 運行狀況和管理服務進行通信。

### <a name="resolution"></a>解決方案

如果您計劃使用網路安全組來控制網路流量,請在安裝 HDInsight 之前執行以下操作:

- 確定計劃用於 HDInsight 的 Azure 區域,並為區域創建 IP 位址的安全清單。 有關詳細資訊,請參閱[執行狀況和管理服務:特定區域](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)。
- 識別 HDInsight 所需的 IP 位址。 關於詳細資訊,請參閱 [HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
- 創建或修改計畫安裝 HDInsight 的子網的網路安全組。 對於網路安全組,允許來自IP位址的埠443上的入站流量。 此配置可確保 HDInsight 管理服務可以從虛擬網路外部到達群集。

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>錯誤代碼:叢集設定未能在一個或多個主機上安裝元件

### <a name="error"></a>錯誤

叢集設置無法在一個或多個主機上安裝元件。 請重試您的請求。

### <a name="cause"></a>原因 

通常,當存在暫時性問題或 Azure 中斷時,將生成此錯誤。

### <a name="resolution"></a>解決方案

檢查[Azure 狀態](https://status.azure.com)頁中是否有任何可能影響群集部署的 Azure 中斷。 如果沒有中斷,請重試群集部署。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>錯誤代碼:與叢集錯誤代碼連線失敗

### <a name="error"></a>錯誤

無法連接到群集管理終結點。 請稍後重試。

### <a name="cause"></a>原因

在嘗試建立叢集時,HDInsight 服務無法連接到叢集

### <a name="resolution"></a>解決方案

如果您使用的是自定義 VNet 網路安全組 (NSG) 和使用者定義的路由 (UDR),請確保群集可以與 HDInsight 管理服務進行通訊。 關於詳細資訊,請參閱[HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>錯誤代碼:由於策略衝突導致部署失敗:「資源」<Resource URI>被策略禁止。 策略識別符:'{"策略分配":{"名稱":"","id":"/<Policy Name>提供者/Microsoft.管理/管理組/提供程式/Microsoft.<Management Group Name>授權/策略分配/"\"""<Policy Name>策略定義":<Policy Definition>

### <a name="cause"></a>原因

基於訂閱的 Azure 策略可能會拒絕創建公共 IP 位址。 建立 HDInsight 叢集需要兩個公用 IP。

以下策略通常影響叢集建立:

* 禁止在訂閱中創建 IP 位址或負載平衡器的策略。
* 阻止創建存儲帳戶的策略。
* 阻止刪除網路資源(如IP位址或負載均衡器)的策略。

### <a name="resolution"></a>解決方案

在創建 HDInsight 群集時刪除或禁用基於訂閱的 Azure 策略。

---

## <a name="next-steps"></a>後續步驟

有關在群組建立中排除錯誤的詳細資訊,請參閱使用[Azure HDInsight 排除叢集建立故障](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)。
