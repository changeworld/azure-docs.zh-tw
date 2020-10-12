---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 3312d1ec7c2535e103cf8959599c0d4c3014f520
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86218320"
---
1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用 `Get-HcsApplianceInfo` 取得裝置的資訊。

    下列範例顯示此 Cmdlet 的使用方式：

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    以下是摘要一些重要裝置資訊的表格：

    | 參數 | 說明 |
    |-----------|-------------|
    | FriendlyName                   | 裝置在部署期間透過本機 web UI 設定的易記名稱。 預設的易記名稱是裝置序號。  |
    | SerialNumber                   | 裝置序號是在工廠指派的唯一編號。                                                                             |
    | 模型                          | Azure Stack Edge 或資料箱閘道裝置的模型。 模型是適用于 Azure Stack Edge 的實體，而適用于資料箱閘道的虛擬。                   |
    | FriendlySoftwareVersion        | 對應至裝置軟體版本的易記字串。 針對執行預覽的系統，易記的軟體版本會是 Data Box Edge 1902。 |
    | HcsVersion                     | 裝置上執行的 HCS 軟體版本。 例如，對應至 Data Box Edge 1902 的 HCS 軟體版本為1.4.771.324。            |
    | LocalCapacityInMb              | 裝置的本機容量總計（以 Mb 為單位）。                                                                                                        |
    | IsRegistered                   | 此值指出您的裝置是否已啟用服務。                                                                                         |


