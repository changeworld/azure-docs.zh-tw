---
title: 在 Azure API for FHIR 中設定匯出設定
description: 本文說明如何在 Azure API for FHIR 中設定匯出設定
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 7ed4e471764fbf8ecc9b82a7d614ae52b97360f1
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267253"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>設定匯出設定並設定儲存體帳戶

Azure API for FHIR 支援 $export 命令，可讓您將 Azure API for FHIR 帳戶的資料匯出至儲存體帳戶。

在 Azure API for FHIR 中設定匯出包含三個步驟：

1. 在 Azure API for FHIR 服務上啟用受控識別
2. 建立 Azure 儲存體帳戶 (在) 之前未完成，並將 Azure API for FHIR 的許可權指派給儲存體帳戶
3. 在 Azure API for FHIR 中選取儲存體帳戶作為匯出儲存體帳戶

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>在 Azure API for FHIR 上啟用受控識別

設定匯出 Azure API for FHIR 的第一步，是在服務上啟用系統廣泛的受控識別。 您可以在 [這裡](../active-directory/managed-identities-azure-resources/overview.md)閱讀 Azure 中受控識別的所有相關資訊。

若要這樣做，請流覽至 Azure API for FHIR 服務，然後選取 [身分識別] 分頁。 將狀態變更為 [開啟] 會在 Azure API for FHIR 服務中啟用受控識別。

![啟用受控識別](media/export-data/fhir-mi-enabled.png)

現在我們可以移至下一個步驟，並建立儲存體帳戶，並將許可權指派給我們的服務。

## <a name="adding-permission-to-storage-account"></a>正在將許可權新增至儲存體帳戶

匯出的下一個步驟是指派 Azure API for FHIR 服務的許可權，以寫入儲存體帳戶。

建立儲存體帳戶之後，請流覽至儲存體帳戶中的 [存取控制] (IAM) 分頁，然後選取 [新增角色指派]

![啟用受控識別](media/export-data/fhir-export-role-assignment.png)

接著，我們會在服務名稱中新增角色儲存體 Blob 資料參與者。

![啟用受控識別](media/export-data/fhir-export-role-add.png)

現在我們已準備好進行下一個步驟，我們可以在 Azure API for FHIR 中選取儲存體帳戶，作為 $export 的預設儲存體帳戶。

## <a name="selecting-the-storage-account-for-export"></a>選取 $export 的儲存體帳戶

最後一個步驟是指派 Azure API for FHIR 將用來匯出資料的 Azure 儲存體帳戶。 若要這樣做，請在 Azure 入口網站的 Azure API for FHIR 服務中流覽至 Integration blade，然後選取儲存體帳戶

![啟用受控識別](media/export-data/fhir-export-storage.png)

之後，我們就可以使用 $export 命令來匯出資料。

>[!div class="nextstepaction"]
>[其他設定](azure-api-for-fhir-additional-settings.md)
