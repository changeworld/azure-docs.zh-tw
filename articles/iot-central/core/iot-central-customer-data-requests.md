---
title: Azure IoT Central 的客戶資料要求功能 | Microsoft Docs
description: 本文說明如何在 Azure IoT Central 應用程式中識別、刪除和匯出客戶資料。
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dabcadea96f4ced5bdf73a35ef533e6d290595c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87001871"
---
# <a name="azure-iot-central-customer-data-request-features"></a>Azure IoT Central 客戶資料要求功能

Azure IoT 中心是完全受控的物聯網 (IoT) 軟體即服務解決方案，可讓您輕鬆地大規模連線、監視和管理 IoT 資產，從 IoT 資料建立深入見解，並採取明智的動作。

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>識別客戶資料

Azure Active Directory 物件識別碼可用來識別使用者和指派角色。 Azure IoT 中心入口網站會顯示使用者電子郵件地址以便指派角色，但只儲存了 Azure Active Directory 物件識別碼，電子郵件地址是動態查詢自 Azure Active Directory。 Azure IoT 中心系統管理員可以在 Azure IoT 中心應用程式的使用者管理區段中檢視、匯出和刪除應用程式使用者。

在應用程式中，也可以設定電子郵件地址以接收警示。 在此情況下，電子郵件地址會儲存在 IoT 中心之內，且必須從應用程式內的帳戶管理頁面來管理。

至於裝置，Microsoft 不會保留任何資訊，且無權存取可讓裝置與使用者相互關聯的任何資料。 在 Azure IoT 中心中受管理的許多裝置都不是個人裝置，例如販賣機或咖啡機。 但是，客戶可能會認為某些裝置能夠識別個人身分，而決定要自行維護資產或是可連結裝置與個人的庫存管理系統。 Azure IoT 中心可管理和儲存與裝置相關聯的所有資料，如同處理個人資料一樣。

當您使用 Microsoft 企業服務時，Microsoft 會產生一些資訊，稱為系統產生的記錄。 這些記錄可視為服務內進行的實際動作，以及個別裝置相關的診斷資料，且與使用者活動無關。 應用程式系統管理員無法存取或匯出 Azure IoT 中心的系統產生記錄。

## <a name="deleting-customer-data"></a>刪除客戶資料

若要刪除使用者資料，只能透過 IoT 中心管理頁面。 應用程式系統管理員可以選取要刪除的使用者，然後選取應用程式右上角的 [ **刪除** ] 來刪除記錄。 應用程式系統管理員也可以移除不再與特定應用程式相關聯的個人帳戶。

刪除使用者之後，就不會再將警示以電子郵件傳送給他們。 不過，這些使用者的電子郵件地址必須從每個已設定的警示中個別移除。

## <a name="exporting-customer-data"></a>匯出客戶資料

若要匯出資料，只能透過 IoT 中心管理頁面。 應用程式系統管理員可以選取、複製和貼上客戶資料 (包括已指派的角色)。

## <a name="links-to-additional-documentation"></a>其他文件的連結

如需有關帳戶管理的詳細資訊，包括角色定義，請參閱[如何管理應用程式](howto-administer.md)。
