---
title: 如何在 Azure Synapse 工作區中將許可權授與受控識別
description: 說明如何在 Azure Synapse 工作區中設定受控識別許可權的文章。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 1f0644c25d0047f774fe8f99efa34a33e10d7b2b
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983290"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>將許可權授與工作區受控識別（預覽）

本文會教您如何在 Azure synapse 工作區中將許可權授與受控識別。 接著，許可權會允許存取工作區中的 SQL 集區，並透過 Azure 入口網站 ADLS Gen2 儲存體帳戶。

>[!NOTE]
>本文件的其餘部分會將此工作區受控識別稱為受控識別。

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>將受控識別許可權授與 SQL 集區

受控識別會授與許可權給工作區中的 SQL 集區。 授與許可權之後，您可以協調執行 SQL 集區相關活動的管線。 當您使用 Azure 入口網站建立 Azure Synapse 工作區時，您可以授與受控識別的控制許可權給 SQL 集區。

當您要建立 Azure Synapse 工作區時，請選取 [**安全性 + 網路**]。 然後選取 [將**控制項授與 SQL 集區上工作區的受控識別**]。

![SQL 集區的 CONTROL 許可權](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>將受控識別許可權授與 ADLS Gen2 儲存體帳戶

需要 ADLS Gen2 儲存體帳戶，才能建立 Azure Synapse 工作區。 若要在 Azure Synapse 工作區中成功啟動 Spark 集區，Azure Synapse 受控識別需要此儲存體帳戶上的*儲存體 Blob 資料參與者*角色。 Azure Synapse 中的管線協調流程也受益于此角色。

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>在工作區建立期間將許可權授與受控識別

當您使用 Azure 入口網站建立 Azure Synapse 工作區之後，Azure Synapse 會嘗試將儲存體 Blob 資料參與者角色授與受控識別。 您會在 [**基本**] 索引標籤中提供 ADLS Gen2 儲存體帳戶詳細資料。

![工作區建立流程中的 [基本] 索引標籤](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

選擇 [**帳戶名稱**] 和 [**檔案系統名稱**] 中的 [ADLS Gen2 儲存體帳戶] 和 [filesystem]。

![提供 ADLS Gen2 儲存體帳戶詳細資料](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

如果工作區建立者也是 ADLS Gen2 儲存體帳戶的**擁有**者，則 Azure Synapse 會將*儲存體 Blob 資料參與者*角色指派給受控識別。 您會在您輸入的儲存體帳戶詳細資料下方看到下列訊息。

![成功的儲存體 Blob 資料參與者指派](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

如果工作區建立者不是 ADLS Gen2 儲存體帳戶的擁有者，則 Azure Synapse 不會將*儲存體 Blob 資料參與者*角色指派給受控識別。 出現在儲存體帳戶詳細資料下方的訊息會通知工作區建立者，他們沒有足夠的許可權可將*儲存體 Blob 資料參與者*角色授與受控識別。

![不成功的儲存體 Blob 資料參與者指派](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

如訊息所述，您無法建立 Spark 集區，除非*儲存體 Blob 資料參與者*已指派給受控識別。

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>建立工作區之後授與受控識別的許可權

建立工作區期間，如果您未將*儲存體 Blob 資料參與者*指派給受控識別，則 ADLS Gen2 儲存體帳戶的**擁有**者會手動將該角色指派給身分識別。 下列步驟將協助您完成手動指派。

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>步驟1：在 Azure 入口網站中流覽至 ADLS Gen2 儲存體帳戶

在 Azure 入口網站中，開啟 ADLS Gen2 儲存體帳戶，然後從左側導覽中選取 **[總覽**]。 您只需要在容器或檔案系統層級指派*儲存體 Blob 資料參與者*角色。 選取 [容器] ****。  
![ADLS Gen2 儲存體帳戶總覽](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>步驟2：選取容器

受控識別應該具有在建立工作區時所提供的容器（檔案系統）的資料存取權。 您可以在 Azure 入口網站中找到此容器或檔案系統。 在 Azure 入口網站中開啟 Azure Synapse 工作區，然後從左側導覽中選取 [**總覽**] 索引標籤。
![ADLS Gen2 儲存體帳戶容器](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


選取該相同的容器或檔案系統，將「*儲存體 Blob 資料參與者*」角色授與受控識別。
![ADLS Gen2 儲存體帳戶容器選取範圍](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>步驟3：流覽至存取控制

選取 **[存取控制（IAM）**]。

![存取控制（IAM）](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>步驟4：加入新的角色指派

選取 [ **+ 新增**]。

![加入新的角色指派](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>步驟5：選取 RBAC 角色

選取 [**儲存體 Blob 資料參與者**] 角色。

![選取 RBAC 角色](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>步驟6：選取 Azure AD 安全性主體

從 [**指派存取權**] 下拉 Azure AD 選取 [**使用者]、[群組] 或 [服務主體**]。

![選取 AAD 安全性主體](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>步驟7：搜尋受控識別

受控識別的名稱也是工作區名稱。 在 [**選取**] 中輸入您的 Azure Synapse 工作區名稱，以搜尋受控識別。 您應該會看到列出受控識別。

![尋找受控識別](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>步驟8：選取受控識別

選取受管理的身分識別給**選取的成員**。 選取 [**儲存**] 以新增角色指派。

![選取受控識別](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>步驟9：確認儲存體 Blob 資料參與者角色已指派給受控識別

選取 **[存取控制（IAM）** ]，然後選取 [**角色指派**]。

![驗證角色指派](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

您應該會看到受控識別列在 [**儲存體 Blob 資料參與者**] 區段底下，並已指派 [*儲存體 blob 資料參與者*] 角色。 
![ADLS Gen2 儲存體帳戶容器選取範圍](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>後續步驟

深入瞭解[工作區受控識別](./synapse-workspace-managed-identity.md)
