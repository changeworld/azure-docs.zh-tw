---
title: 針對 Synapse Studio 與儲存體之間的連線進行疑難排解
description: 針對 Synapse Studio 與儲存體之間的連線進行疑難排解
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 8cf440a517c1a3496b3df438fdd0d2534609908f
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733093"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>針對 Azure Synapse Analytics Synapse Studio 與儲存體之間的連線進行疑難排解

在 Synapse Studio 中，您可以流覽位於連結儲存體中的資料資源。 當您嘗試存取資料資源時，本指南可協助您解決連線問題。 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>案例 #1：儲存體帳戶缺少適當的許可權

如果您的儲存體帳戶缺少適當的許可權，您將無法透過 Synapse Studio 中的「資料」來展開儲存體結構，> 「連結」。 請參閱下方的問題徵兆螢幕擷取畫面。 

詳細的錯誤訊息可能會有所不同，但錯誤訊息的一般意義是：「此要求未獲授權執行此作業」。

在連結的儲存體節點中：  
![儲存體連接問題1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

在儲存體容器節點中：  
![儲存體連接問題1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**解決方案**：若要將您的帳戶指派給適當的角色，請參閱 [使用 Azure 入口網站指派 Azure 角色以存取 blob 和佇列資料](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>案例 #2：無法將要求傳送至存放裝置伺服器

在 Synapse Studio 中選取箭號以展開「> 資料」中的儲存體結構時，您可能會在左側面板中看到「REQUEST_SEND_ERROR」問題。 請參閱下列問題徵兆螢幕擷取畫面：

在連結的儲存體節點中：  
![儲存體連接問題2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

在儲存體容器節點中：  
![儲存體連接問題2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

此問題背後可能有幾個可能的原因：

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>儲存體資源位於 vNet 後方，而儲存體私人端點必須設定

**解決方案**：在此情況下，您必須設定儲存體帳戶的儲存體私人端點。 如需如何設定 vNet 的儲存體私人端點，請參閱 [使用 Azure 入口網站指派 Azure 角色以存取 blob 和佇列資料](../security/how-to-connect-to-workspace-from-restricted-network.md)。

設定儲存體私人端點之後，您可以使用 "nslookup \<storage-account-name\> . dfs.core.windows.net" 命令來檢查連線能力。 它應該會傳回類似以下的字串： " \<storage-account-name\> privatelink.dfs.core.windows.net"。

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>儲存體資源不在 vNet 後方，但因為已設定防火牆，所以無法存取 Blob 服務 (Azure AD) 端點

**解決方案**：在此情況下，您必須在 Azure 入口網站中開啟您的儲存體帳戶。 在左側導覽中，向下滾動以 **支援 + 疑難排解** ，然後選取 [連線 **檢查** ]，以檢查 **Blob 服務 (Azure AD)** 連接狀態。 如果無法存取，請遵循升級指南來檢查 [儲存體帳戶] 頁面下的 [ **防火牆和虛擬網路** ] 設定。 如需有關儲存體防火牆的詳細資訊，請參閱 [設定 Azure 儲存體防火牆和虛擬網路](../../storage/common/storage-network-security.md)。

### <a name="other-issues-to-check"></a>其他要檢查的問題 

* 您正在存取的儲存體資源是 Azure Data Lake Storage Gen2，且位於防火牆和 vNet (，同時設定了) 的儲存體私人端點。
* 您要存取的容器資源已刪除或不存在。
* 跨租使用者：使用者用來登入的工作區租使用者與儲存體帳戶的租使用者不相同。 


## <a name="next-steps"></a>後續步驟
如果先前的步驟無法協助您解決問題，請 [建立支援票證](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)。
