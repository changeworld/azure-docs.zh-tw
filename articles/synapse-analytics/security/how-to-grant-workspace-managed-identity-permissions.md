---
title: 如何授予 Azure Synapse 工作區中的託管識別權限
description: 一篇介紹如何在 Azure Synapse 工作區中配置託管標識許可權的文章。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428013"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>授到工作區託管識別的權限(預覽)

本文介紹如何在 Azure 突觸工作區中授予託管標識的許可權。 許可權又允許透過 Azure 門戶存取工作區中的 SQL 池和 ADLS gen2 儲存帳戶。

>[!NOTE]
>此工作區託管標識將通過本文檔的其餘部分稱為託管標識。

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>向 SQL 池給託管識別權限

託管標識向工作區中的 SQL 池授予許可權。 授予許可權后,可以協調執行 SQL 池相關活動的管道。 使用 Azure 門戶創建 Azure 突觸工作區時,可以在 SQL 池上授予託管標識 CONTROL 許可權。

在創建 Azure 同步工作區時選擇 **「安全 + 網路**」。 選擇**在 SQL 池上對工作區的託管識別控制**。

![SQL 池上的 CONTROL 權限](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>將託管識別權限管理管理員(ADLS) 第 2 代儲存帳戶

創建 Azure 突觸工作區需要 ADLS gen2 儲存帳戶。 要在 Azure Synapse 工作區中成功啟動 Spark 池,Azure Synapse 託管標識需要此存儲帳戶上的*儲存 Blob 資料參與者*角色。 Azure Synapse 中的管道業務流程也受益於此角色。

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>在工作區建立期間授予託管識別的權限

使用 Azure 突觸工作區創建 Azure Synapse 工作區後,Azure Synapse 將嘗試將儲存 Blob 資料參與者角色授予託管標識。 您可以在 **「基礎知識」** 選項卡中提供 ADLS 第 2 代儲存帳戶詳細資訊。

![工作區建立串流中的基礎知識選項卡](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

在**帳號名稱**和**檔案系統名稱**中選擇 ADLS gen2 儲存帳戶和檔案系統。

![提供 ADLS 第 2 代儲存帳戶詳細資訊](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

如果工作區建立者也是 ADLS gen2 儲存帳戶**的擁有者**,則 Azure Synapse 將儲存*Blob 資料參與者*角色分配給託管標識。 您將在下面看到您輸入的儲存帳戶詳細資訊。

![成功配置儲存 Blob 資料參與者](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

如果工作區建立者不是 ADLS gen2 儲存帳戶的擁有者,則 Azure Synapse 不會將儲存*Blob 資料參與者*角色分配給託管標識。 存儲帳戶詳細資訊下方顯示的消息通知工作區建立者他們沒有足夠的許可權將*存儲 Blob 數據參與者*角色授予託管標識。

![失敗的儲存檢視器的總存取](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

如消息指出,除非*將存儲 Blob 數據參與者*分配給託管標識,否則無法創建 Spark 池。

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>在工作區建立後授予託管識別的權限

在工作區創建期間,如果不將存儲 Blob*數據參與者*分配給託管標識,則 ADLS gen2 存儲帳戶**的擁有者**將該角色手動分配給該標識。 以下步驟將説明您完成手動作業。

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>第一步:導覽到 Azure 門戶中的 ADLS 第 2 代儲存帳戶

在 Azure 門戶中,打開 ADLS gen2 儲存帳戶,然後從左側導航中選擇 **「概述**」。 您只需在容器或檔案系統級別分配*儲存 Blob 資料參與者*角色。 選取 [容器] ****。  
![ADLS 第 2 代儲存帳戶概述](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>第二步:選擇容器

託管標識應具有對創建工作區時提供的容器(檔案系統)的數據訪問許可權。 您可以在 Azure 門戶中找到此容器或檔案系統。 在 Azure 門戶中打開 Azure 同步工作區,然後從左側導航中選擇 **「概述」** 選項卡。
![ADLS 第 2 代儲存帳戶容器](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


選擇同一容器或檔案系統,將存儲*Blob 數據參與者*角色授予託管標識。
![ADLS 第 2 代儲存帳戶容器選擇](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>第三步:瀏覽到存取控制

選擇**訪問控制 (IAM)。**

![存取控制(IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>步驟 4:添加新的角色分配

選擇 **= 新增**。

![新增新角色配置](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>第 5 步:選擇 RBAC 角色

選擇**存儲 Blob 資料參與者**角色。

![選擇 RBAC 角色](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>第 6 步:選擇 Azure AD 安全主體

從 **「配置」下拉清單的存取權限**中選擇**Azure AD 使用者、群組或服務主體**。

![選擇 AAD 安全主體](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>第 7 步:搜尋託管識別

託管標識的名稱也是工作區名稱。 通過在 **「選擇**」中輸入 Azure Synapse 工作區名稱來搜尋託管標識。 您應該會看到列出的託管標識。

![尋找託管識別](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>第 8 步:選擇託管識別

選擇選**定成員**「的託管識別。 選擇 **「儲存」** 以新增角色分配。

![選擇託管識別](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>第 9 步:驗證儲存 Blob 資料參與者角色是否分配給託管識別

選擇**存取控制 (IAM),** 然後選擇**角色分配**。

![驗證角色分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

您應該會看到託管識別列在 **「存儲 Blob 資料參與者」** 部分下,並為其分配了 *「存儲 Blob 資料參與者」* 角色。 
![ADLS 第 2 代儲存帳戶容器選擇](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>後續步驟

瞭解有關[工作區託管標識的更多資訊](./synapse-workspace-managed-identity.md)
