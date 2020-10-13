---
title: 在 Azure IoT Central 中推出 x.509 憑證
description: 如何使用 IoT Central 應用程式來變換 x.509 憑證
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9e35c7d4d64279c65971dd512bcd2107dad6594
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000057"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>如何在 IoT Central 應用程式中變換 x.509 裝置憑證

在 IoT 解決方案的生命週期內，您必須輪替憑證。 輪替憑證的兩大主因是安全性缺口和憑證到期。

如果您有安全性缺口，則輪流憑證是協助保護系統安全的安全性最佳作法。 在[假想缺口方法](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)中，Microsoft 主張備妥回應式安全程序和預防性措施的需求。 輪替裝置憑證應納入這些安全程序中。 您輪替憑證的頻率將取決於您解決方案的安全性需求。 有解決方案涉及高度敏感性資料的客戶可以每天輪替，然而其他客戶則是每隔幾年輪替其憑證。


## <a name="obtain-new-x509-certificates"></a>取得新的 x.509 憑證

您可以使用 OpenSSL 之類的工具來建立您自己的 x.509 憑證。 這種方法適合用來測試 X.509 憑證，但是提供很少的安全性保證。 除非您已備妥作為自己的 CA 提供者，否則請只使用此方法進行測試。

## <a name="enrollment-groups-and-security-breaches"></a>註冊群組和安全性缺口

若要更新群組註冊以回應安全性缺口，您應該使用下列方法來立即更新目前的憑證：

1. 流覽至左窗格中的 [系統 **管理**  ]，然後選取 [ **裝置**連線]。

2. 選取 [ **註冊群組**]，然後選取清單中的組名。

3. 若要更新憑證，請選取 [ **管理主要** ] 或 [ **管理次要**]。

4. 新增並驗證註冊群組中的根 x.509 憑證。

   如果主要和次要憑證都遭到入侵，請完成這些步驟。

## <a name="enrollment-groups-and-certificate-expiration"></a>註冊群組與憑證到期

如果您要輪流處理憑證過期的憑證，請使用下列方法立即更新目前的憑證：

1. 流覽至左窗格中的 [系統 **管理**  ]，然後選取 [ **裝置**連線]。

2. 選取 [ **註冊群組**]，然後選取清單中的組名。

3. 針對 [憑證更新]，選取 [ **管理主要**]。

4. 新增並驗證註冊群組中的根 x.509 憑證。

5. 稍後當次要憑證過期時，返回並更新該次要憑證。

## <a name="individual-enrollments-and-security-breaches"></a>個別註冊和安全性缺口

如果您要輪流憑證來回應安全性缺口，請使用下列方法立即更新目前的憑證：

1. 選取 [ **裝置**]，然後選取裝置。

2. 選取 **[連線]**，然後選取 [連接方法為**個別註冊**]

3. 選取 ** (x.509) 的憑證 ** 作為機制。

    ![管理個別註冊](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 針對 [憑證更新] 選取 [資料夾] 圖示，以選取要針對註冊專案上傳的新憑證。 選取 [儲存]。

    如果主要和次要憑證都遭到入侵，請完成這些步驟

## <a name="individual-enrollments-and-certificate-expiration"></a>個別註冊和憑證到期

如果您是以輪替憑證的方式來處理憑證過期，則應使用如下所示的次要憑證組態，使嘗試佈建的裝置減少停機時間。

當次要憑證接近到期，而且需要加以匯總時，您可以使用主要設定來輪替。 如此一來，輪流使用主要與次要憑證可使嘗試佈建的裝置減少停機時間。

1. 選取 [ **裝置**]，然後選取裝置。

2. 選取 **[連線]**，然後選取 [連接方法為**個別註冊**]

3. 選取 ** (x.509) 的憑證 ** 作為機制。

    ![管理個別註冊](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 針對次要憑證更新，請選取資料夾圖示，以選取要針對註冊專案上傳的新憑證。 選取 [儲存]。

5. 稍後當主要憑證過期時，返回並更新該主要憑證。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何在 Azure IoT Central 應用程式中變換 x.509 憑證，您可以 [連線到 Azure IoT Central](concepts-get-connected.md)。


