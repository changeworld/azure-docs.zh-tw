---
title: 使用 Azure 入口網站來搭配媒體服務使用客戶自控金鑰或 BYOK
description: 在本教學課程中，您將使用 Azure 入口網站讓客戶自控金鑰或攜帶您自己的金鑰 (BYOK) 可以與 Azure 媒體服務儲存體帳戶搭配使用。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 003b8e066a6161baedbc70e9becbca23566813ef
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "96013212"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>教學課程：使用 Azure 入口網站來搭配媒體服務使用客戶自控金鑰或 BYOK

透過 2020-05-01 API，您可以搭配使用客戶自控的 RSA 金鑰與具有系統管理身分識別的 Azure 媒體服務帳戶。此教學課程涵蓋 Azure 入口網站中的步驟。

使用的服務包括：

- Azure 儲存體
- Azure 金鑰保存庫
- Azure 媒體服務

在本教學課程裡，您將學習如何使用 Azure 入口網站來：

> [!div class="checklist"]
> - 建立資源群組。
> - 建立具有系統管理身分識別的儲存體帳戶。
> - 建立具有系統管理身分識別的媒體服務帳戶。
> - 建立金鑰保存庫以儲存客戶自控的 RSA 金鑰。

## <a name="prerequisites"></a>必要條件

Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/free/)。

## <a name="system-managed-keys"></a>系統管理的金鑰

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> 針對下列儲存體帳戶的建立步驟，您將在 [進階設定] 中選取 [系統管理的金鑰] 選項。

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> 針對下列儲存體加密的步驟，您將選取 [客戶自控金鑰] 選像。

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>變更金鑰

金鑰發生變更時，媒體服務會自動偵測到。 選擇性：若要測試此流程，請為相同的金鑰建立另一個金鑰版本。 媒體服務應該會偵測到金鑰已經變更。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用您所建立的資源，且 *不想繼續產生費用*，請將資源刪除。

## <a name="next-steps"></a>後續步驟

請前往下一篇文章以了解如何：
> [!div class="nextstepaction"]
> [使用 REST 編碼以 URL 為基礎的遠端檔案及串流處理影片](stream-files-tutorial-with-rest.md)
