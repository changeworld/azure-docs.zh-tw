---
title: 在 Azure API for FHIR 中設定匯出設定
description: 本文說明如何在 Azure API for FHIR 中設定匯出設定
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871018"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>設定匯出設定並將資料匯出至儲存體帳戶

Azure API for FHIR 支援 $export 命令，可讓您將 Azure API for FHIR 帳戶的資料匯出至儲存體帳戶。

在 Azure API for FHIR 中執行匯出包含四個步驟：

1. 在 Azure API for FHIR 服務上啟用受控識別
2. 建立 Azure 儲存體帳戶（如果之前未完成），並將 Azure API for FHIR 的許可權指派給儲存體帳戶
3. 在 Azure API for FHIR 中選取儲存體帳戶作為匯出儲存體帳戶
4. 藉由在 Azure API for FHIR 上叫用 $export 命令來執行匯出

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>在 Azure API for FHIR 上啟用受控識別

設定匯出 Azure API for FHIR 的第一個步驟是在服務上啟用全系統受控識別。 您可以在[這裡](../active-directory/managed-identities-azure-resources/overview.md)閱讀 Azure 受控識別的所有相關資訊。

若要這麼做，請流覽至 Azure API for FHIR 服務，然後選取 [身分識別] 分頁。 將狀態變更為 [開啟]，將會在 Azure API for FHIR 服務中啟用受控識別。

![啟用受控識別](media/export-data/fhir-mi-enabled.png)

我們現在可以移至下一個步驟，並建立儲存體帳戶，並將許可權指派給我們的服務。

## <a name="adding-permission-to-storage-account"></a>正在將許可權新增至儲存體帳戶

匯出的下一個步驟是將許可權指派給 Azure API for FHIR 服務，以寫入儲存體帳戶。

建立儲存體帳戶之後，流覽至儲存體帳戶中的存取控制（IAM）分頁，然後選取 [新增角色指派]

![啟用受控識別](media/export-data/fhir-export-role-assignment.png)

在這裡，我們會將角色儲存體 Blob 資料參與者新增至我們的服務名稱。

![啟用受控識別](media/export-data/fhir-export-role-add.png)

現在，我們已準備好進行下一個步驟，讓我們可以在 Azure API for FHIR 中選取儲存體帳戶，做為 $export 的預設儲存體帳戶。

## <a name="selecting-the-storage-account-for-export"></a>選取 $export 的儲存體帳戶

叫用 $export 命令前的最後一個步驟是指派 Azure API for FHIR 用來將資料匯出至其中的 Azure 儲存體帳戶。 若要這麼做，請流覽至 Azure 入口網站中 Azure API for FHIR 服務的整合分頁，然後選取儲存體帳戶 

![啟用受控識別](media/export-data/fhir-export-storage.png)

之後，我們就可以使用 $export 命令來匯出資料。

## <a name="exporting-the-data-using-export-command"></a>使用 $export 命令來匯出資料

在設定 Azure API for FHIR 進行匯出之後，我們現在可以開始使用 $export 命令，將資料從服務匯出到我們指定的儲存體帳戶。 若要瞭解如何在 FHIR server 中叫用 $export 命令，請參閱 $export 規格的檔，網址為[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> 請注意，目前 Azure API for FHIR 僅支援匯出規格中所定義的系統層級匯出 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) 。 我們目前也不支援使用 $export 的查詢參數。

>[!div class="nextstepaction"]
>[其他設定](azure-api-for-fhir-additional-settings.md)