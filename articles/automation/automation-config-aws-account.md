---
title: 使用 Amazon Web Services 驗證 Azure 自動化 Runbook
description: 本文說明如何向 Amazon Web Services 驗證 Runbook。
keywords: aws 驗證, 設定 aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837176"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>使用 Amazon Web Services 驗證 Runbook

使用 Amazon Web Services (AWS) 中的資源自動執行常見工作可透過 Azure 中的自動化 Runbook 來完成。 您可以和使用 Azure 中的資源一樣，在 AWS 中使用自動化 Runbook 自動執行許多工作。 若要進行驗證，您必須有 Azure 訂用帳戶。

## <a name="obtain-aws-subscription-and-credentials"></a>取得 AWS 訂用帳戶和認證

若要向 AWS 驗證，您必須取得 AWS 訂用帳戶，並指定一組 AWS 認證，以驗證從 Azure 自動化執行的 Runbook。 具體需要的認證為 AWS 存取金鑰和秘密金鑰。 請參閱[使用 AWS 認證](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)。

## <a name="configure-automation-account"></a>設定自動化帳戶

您可以使用現有的自動化帳戶向 AWS 驗證。 或者，針對以 AWS 資源為目標的 Runbook，您可以專用一個帳戶。 在此情況下，請建立新的[自動化帳戶](automation-create-standalone-account.md)。  

## <a name="store-aws-credentials"></a>儲存 AWS 認證

您必須將 AWS 認證儲存為 Azure 自動化中的資產。 如需有關建立存取金鑰和秘密金鑰的指示，請參閱[管理 AWS 帳戶的存取金鑰](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)。 有金鑰可用時，請將存取金鑰識別碼和秘密金鑰識別碼複製到安全的地方。 您可以下載金鑰檔案，並儲存在某個安全的地方。

## <a name="create-credential-asset"></a>建立認證資產

建立並複製 AWS 安全性金鑰之後，您必須使用自動化帳戶建立認證資產。 此資產可讓您安全地儲存 AWS 金鑰，並在 Runbook 中參考 AWS 金鑰。 請參閱[使用 Azure 入口網站建立新的認證資產](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal)。 在提供的欄位中輸入下列 AWS 資訊：
    
* **名稱** - **AWScred**，或遵循命名標準的適當值
* **使用者名稱** - 您的存取識別碼
* **密碼** - 秘密金鑰的名稱 

## <a name="next-steps"></a>後續步驟

* 若要了解如何建立 Runbook 將 AWS 中的工作自動化，請參閱 [使用 Runbook 部署 Amazon Web Services VM](automation-scenario-aws-deployment.md)。