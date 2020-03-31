---
title: Azure HDInsight 創建群集 - 錯誤字典
description: 瞭解如何排除創建 Azure HDInsight 群集時出現的錯誤
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: b0dc974185ad616d57327e9cc3743db9ecb20e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302724"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight：群集創建錯誤

本文介紹了創建群集時可能會遇到的錯誤的解決方法。

> [!NOTE]
> 本文中描述的前三個錯誤是驗證錯誤。 當 Azure HDInsight 產品使用**CsmDocument_2_0**類時，可能會發生此類操作。

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤代碼：部署文檔"CsmDocument_2_0"驗證失敗

### <a name="error"></a>錯誤

"無法訪問腳本操作位置 URI：SCRIPT\<操作 URL"\>

#### <a name="error-message"></a>錯誤訊息

遠端伺服器返回了一個錯誤：（404） 未找到。

### <a name="cause"></a>原因

HDInsight 服務無法訪問您在創建群集請求中提供的腳本操作 URL。 當服務嘗試訪問腳本操作時，它會收到前面的錯誤訊息。

### <a name="resolution"></a>解決方案

- 對於 HTTP 或 HTTPS URL，請通過嘗試從隱身瀏覽器視窗轉到 URL 來驗證 URL。
- 對於 WASB URL，請確保腳本存在於您在請求中給出的存儲帳戶中。 還要確保此存儲帳戶的存儲金鑰正確。
- 對於 ADLS URL，請確保該腳本存在於存儲帳戶中。

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤代碼：部署文檔"CsmDocument_2_0"驗證失敗

### <a name="error"></a>錯誤

無法訪問腳本操作位置 URI：SCRIPT_ACTION_URL" \< \>

#### <a name="error-message"></a>錯誤訊息

給定的腳本 URI \< \> SCRIPT_URI位於 ADLS 中，但此群集沒有資料湖存儲主體

### <a name="cause"></a>原因

HDInsight 服務無法訪問您在創建群集請求中提供的腳本操作 URL。 當服務嘗試訪問腳本操作時，它會收到前面的錯誤訊息。

### <a name="resolution"></a>解決方案

將相應的 Azure 資料存儲庫存儲第 1 代帳戶添加到群集。 還將訪問資料存儲湖存儲第 1 代帳戶的服務主體添加到群集中。

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤代碼：部署文檔"CsmDocument_2_0"驗證失敗

### <a name="error"></a>錯誤

請求中提供的\<"VM\>大小"CUSTOMER_SPECIFIED_VM_SIZE" 對於角色"ROLE"\<\>無效或不支援。 有效值為：VALID_VM_SIZE_FOR_ROLE。 \< \>

### <a name="cause"></a>原因

不允許角色指定虛擬機器大小。 可能發生此錯誤，因為 VM 大小值不按預期工作或不適合計算機角色。

### <a name="resolution"></a>解決方案

錯誤訊息列出 VM 大小的有效值。 選擇這些值之一，然後重試創建群集請求。

---

## <a name="error-codeinvalidvirtualnetworkid"></a>錯誤代碼：無效虛擬網路 Id  

### <a name="error"></a>錯誤

"虛擬網路 Id 無效。 虛擬網路 Id\<\>' USER_VIRTUALNETWORKID "*"

### <a name="cause"></a>原因

在群集創建期間指定的**虛擬網路 Id**值格式不正確。

### <a name="resolution"></a>解決方案

確保**虛擬網路Id**和子網值格式正確。 要獲取**虛擬網路 Id**值，可以：

1. 移至 Azure 入口網站。
1. 選擇虛擬網路。
1. 選擇"**屬性"** 功能表項目。 **資源識別碼**屬性值是**虛擬網路 Id**值。

下面是虛擬網路識別碼 的示例：

/訂閱/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/資源組/我的資源組/供應商/微軟.網路/虛擬網路/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>錯誤代碼：自訂失敗錯誤代碼

### <a name="error"></a>錯誤

"由於自訂腳本操作中的錯誤，群集部署失敗。 失敗操作：SCRIPT_NAME，\<\>請轉到 Ambari UI 以進一步調試故障。

### <a name="cause"></a>原因

成功部署群集後，將執行在創建群集請求期間提供的自訂腳本。 此錯誤代碼指示在執行名為\<SCRIPT_NAME\>的自訂腳本期間出現錯誤。

### <a name="resolution"></a>解決方案

由於腳本是自訂腳本，因此我們建議您疑難排解，並在必要時重新運行該腳本。 要排除腳本故障，請檢查 /var/lib/ambari 代理/* 資料夾中的日誌。 或者打開 Ambari UI 中的 **"操作"** 頁，然後選擇**run_customscriptaction**操作以查看錯誤詳細資訊。

---

## <a name="error-codeinvaliddocumenterrorcode"></a>錯誤代碼：無效文檔錯誤代碼

### <a name="error"></a>錯誤

\<資料庫\>\<\>\<\>DATABASE_NAME中的META_STORE_TYPE元存儲架構版本METASTORE_MAJOR_VERSION與群集版本CLUSTER_VERSION不相容。\> \<

### <a name="cause"></a>原因

自訂元存儲與所選的 HDInsight 群集版本不相容。 目前，HDInsight 4.0 群集僅支援 Metastore 版本 3.0 及更高版本，而 HDInsight 3.6 群集不支援 Metastore 版本 3.0 及更高版本。

### <a name="resolution"></a>解決方案

僅使用 HDInsight 群集版本支援的 Metastore 版本。 如果不指定自訂元存儲，HDInsight 會在內部創建元存儲，然後在群集刪除後將其刪除。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>錯誤代碼：未與群集錯誤代碼連接 

### <a name="error"></a>錯誤

無法連接到群集管理終結點以執行縮放操作。 驗證網路安全規則是否阻止對群集的外部訪問，以及群集管理器 （Ambari） UI 是否能夠成功訪問。

### <a name="cause"></a>原因

網路安全性群組 （NSG） 上的防火牆規則阻止與關鍵 Azure 運行狀況和管理服務的群集通信。

### <a name="resolution"></a>解決方案

如果您計畫使用網路安全性群組來控制網路流量，請在安裝 HDInsight 之前執行以下操作：

- 識別您要用於 HDInsight 的 Azure 區域。
- 識別 HDInsight 所需的 IP 位址。 如需詳細資訊，請參閱 [HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
  - 創建或修改計畫安裝 HDInsight 的子網的網路安全性群組。
  - 對於網路安全性群組，允許來自 IP 位址的埠 443 上的入站流量。 此配置可確保 HDInsight 管理服務可以從虛擬網路外部到達群集。

---

## <a name="error-code-storagepermissionsblockedformsi"></a>錯誤代碼：存儲許可權阻止  

### <a name="error"></a>錯誤

託管標識對存儲帳戶沒有許可權。 請驗證"存儲 Blob 資料擁有者"角色是否分配給存儲帳戶的託管標識。 存儲：/訂閱/\<訂閱\>ID /資源\<組/資源\>組名稱/提供程式/Microsoft.存儲/存儲帳戶\</存儲帳戶\>名稱、託管標識：/訂閱\</訂閱\>ID /資源\<組//\>資源組名稱/資源組名稱/提供程式/Microsoft.託管身份\</使用者分配\>Id 身份/使用者託管標識名稱"

### <a name="cause"></a>原因

您沒有提供管理標識所需的許可權。 使用者分配的託管標識在 Azure 資料湖存儲 Gen2 存儲帳戶上沒有 Blob 存儲參與者角色。

### <a name="resolution"></a>解決方案

1. 開啟 Azure 入口網站。
1. 移至您的儲存體帳戶。
1. 查看**存取控制 （IAM）** 下。
1. 確保使用者具有存儲 Blob 資料參與者角色或分配給他們的存儲 Blob 資料擁有者角色。

有關詳細資訊，請參閱在[Data Lake 存儲 Gen2 帳戶上設置託管標識的許可權](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>錯誤代碼：無效網路安全性群組安全規則  

### <a name="error"></a>錯誤

網路安全性群組的安全規則/訂閱/\<訂閱 ID\>/資源組/<資源組名稱\>預設/提供程式/Microsoft.網路/網路安全性群組/\<網路安全性群組名稱\>配置與子網/訂閱/訂閱 ID/\<\>資源組/\<資源組名稱\>RG-westeurope-vnet-tom-default/供應商/Microsoft.Network/虛擬網路/\<虛擬網路網路名稱\>/子網/\<子網名稱\>不允許所需的入站和/或出站連接。 有關詳細資訊，請訪問[為 Azure HDInsight 規劃虛擬網路](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)，或聯繫支援人員。

### <a name="cause"></a>原因

如果網路安全性群組或使用者定義的路由 （UDR） 控制到 HDInsight 群集的入站流量，請確保群集可以與關鍵的 Azure 運行狀況和管理服務進行通信。

### <a name="resolution"></a>解決方案

如果您計畫使用網路安全性群組來控制網路流量，請在安裝 HDInsight 之前執行以下操作：

- 確定計畫用於 HDInsight 的 Azure 區域，並為區域創建 IP 位址的安全清單。 有關詳細資訊，請參閱[運行狀況和管理服務：特定區域](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)。
- 識別 HDInsight 所需的 IP 位址。 有關詳細資訊，請參閱 [HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
- 創建或修改計畫安裝 HDInsight 的子網的網路安全性群組。 對於網路安全性群組，允許來自 IP 位址的埠 443 上的入站流量。 此配置可確保 HDInsight 管理服務可以從虛擬網路外部到達群集。
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>錯誤代碼：群集設置未能在一個或多個主機上安裝元件

###  <a name="error"></a>錯誤

群集設置無法在一個或多個主機上安裝元件。 請重試您的請求。

### <a name="cause"></a>原因 

通常，當存在暫時性問題或 Azure 中斷時，將生成此錯誤。

### <a name="resolution"></a>解決方案

檢查[Azure 狀態](https://status.azure.com)頁中是否有任何可能影響群集部署的 Azure 中斷。 如果沒有中斷，請重試群集部署。

## <a name="next-steps"></a>後續步驟

有關在群集創建中排除錯誤的詳細資訊，請參閱使用[Azure HDInsight 排除群集創建故障](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)。
