---
title: 在 Synapse 工作區中授與受控識別的許可權
description: 說明如何在 Azure Synapse 工作區中設定受控識別許可權的文章。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459002"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>將權限授與工作區受控識別

本文說明如何在 Azure synapse 工作區中授與受控識別的許可權。 許可權接著允許存取工作區中的專用 SQL 集區，並透過 Azure 入口網站 ADLS Gen2 儲存體帳戶。

>[!NOTE]
>本文件的其餘部分會將此工作區受控識別稱為受控識別。

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>將受控識別許可權授與專用的 SQL 集區

受控識別會將許可權授與工作區中的專用 SQL 集區。 授與許可權之後，您就可以協調執行專用 SQL 集區相關活動的管線。 當您使用 Azure 入口網站建立 Azure Synapse 工作區時，可以在專用的 SQL 集區上授與受控識別控制許可權。

當您建立 Azure Synapse 工作區時，請選取 [ **安全性** ]。 然後選取 **[允許管線 (正在執行作為工作區的系統指派身分識別]) 存取 SQL 集區**。

![專用 SQL 集區的 CONTROL 許可權](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>將受控識別許可權授與 ADLS Gen2 儲存體帳戶

需要 ADLS Gen2 儲存體帳戶才能建立 Azure Synapse 工作區。 若要在 Azure Synapse 工作區中成功啟動 Spark 集區，Azure Synapse 受控識別需要此儲存體帳戶上的 *儲存體 Blob 資料參與者* 角色。 Azure Synapse 中的管線協調流程也受益于此角色。

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>在工作區建立期間將許可權授與受控識別

當您使用 Azure 入口網站建立 Azure Synapse 工作區之後，azure Synapse 會嘗試將儲存體 Blob 資料參與者角色授與受控識別。 您可以在 [ **基本** ] 索引標籤中提供 ADLS Gen2 的儲存體帳戶詳細資料。

![工作區建立流程中的 [基本] 索引標籤](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

選擇 **帳戶名稱** 和 **檔案系統名稱** 中的 ADLS Gen2 儲存體帳戶和檔案系統。

![提供 ADLS Gen2 儲存體帳戶詳細資料](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

如果工作區建立者也是 ADLS Gen2 儲存體帳戶的 **擁有** 者，則 Azure Synapse 會將 *儲存體 Blob 資料參與者* 角色指派給受控識別。 您將會在您輸入的儲存體帳戶詳細資料下方看到下列訊息。

![成功的儲存體 Blob 資料參與者指派](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

如果工作區建立者不是 ADLS Gen2 儲存體帳戶的擁有者，則 Azure Synapse 不會將 *儲存體 Blob 資料參與者* 角色指派給受控識別。 出現在儲存體帳戶詳細資料下方的訊息會通知工作區建立者，他們沒有足夠的許可權可將 *儲存體 Blob 資料參與者* 角色授與受控識別。

![不成功的儲存體 Blob 資料參與者指派](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

當訊息指出，除非將 *儲存體 Blob 資料參與者* 指派給受控識別，否則您無法建立 Spark 集區。

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>建立工作區之後，將許可權授與受控識別

在工作區建立期間，如果您未將 *儲存體 Blob 資料參與者* 指派給受控識別，則 ADLS Gen2 儲存體帳戶的 **擁有** 者會手動將該角色指派給身分識別。 下列步驟可協助您完成手動指派。

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>步驟1：流覽至 Azure 入口網站中的 ADLS Gen2 儲存體帳戶

在 Azure 入口網站中，開啟 ADLS Gen2 儲存體帳戶，然後從左側導覽中選取 **[總覽** ]。 您只需要指派容器或檔案系統層級的 *儲存體 Blob 資料參與者* 角色。 選取 [容器] 。  
![ADLS Gen2 儲存體帳戶總覽](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>步驟2：選取容器

受控識別應該具有在建立工作區時所提供的容器 (檔案系統) 的資料存取權。 您可以在 Azure 入口網站中找到此容器或檔案系統。 在 Azure 入口網站中開啟 Azure Synapse 工作區，然後從左側導覽中選取 [ **總覽** ] 索引標籤。
![ADLS Gen2 儲存體帳戶容器](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


選取相同的容器或檔案系統，以將「 *儲存體 Blob 資料參與者* 」角色授與受控識別。
![顯示您應該選取之容器或檔案系統的螢幕擷取畫面。](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>步驟3：流覽至存取控制

選取 [存取控制 (IAM)]。

![ (IAM) 的存取控制](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>步驟4：新增角色指派

選取 [+ 新增]  。

![新增角色指派](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>步驟5：選取 Azure 角色

選取 [ **儲存體 Blob 資料參與者** ] 角色。

![選取 Azure 角色](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>步驟6：選取 Azure AD 安全性主體

從 [**指派存取權至**] 下拉式清單中選取 **Azure AD 使用者、群組或服務主體**。

![選取 AAD 安全性主體](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>步驟7：搜尋受控識別

受控識別的名稱也是工作區名稱。 在 [ **選取**] 中輸入您的 Azure Synapse 工作區名稱，以搜尋受控識別。 您應該會看到列出的受控識別。

![尋找受控識別](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>步驟8：選取受控識別

選取所 **選成員** 的受控識別。 選取 [ **儲存** ] 以新增角色指派。

![選取受控識別](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>步驟9：確認儲存體 Blob 資料參與者角色已指派給受控識別

選取 [ **存取控制] (IAM)** ，然後選取 [ **角色指派**]。

![確認角色指派](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

您應該會看到您的受控識別列在 [ **儲存體 Blob 資料參與者** ] 區段底下，並獲派 *儲存體 blob 資料參與者* 角色。 
![ADLS Gen2 儲存體帳戶容器選取專案](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>後續步驟

深入瞭解 [工作區受控識別](./synapse-workspace-managed-identity.md)
