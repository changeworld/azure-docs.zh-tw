---
title: 說話者辨識 API 是什麼？
titleSuffix: Azure Cognitive Services
description: 透過認知服務中的說話者辨識 API，進行說話者驗證和說話者辨識。
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73464912"
---
# <a name="speaker-recognition-api---preview"></a>說話者辨識 API - 預覽

說話者辨識 API 是雲端式 API，提供先進的 AI 演算法進行說話者驗證和說話者辨識。 說話者辨識分為兩類：說話者驗證和說話者辨識。

## <a name="speaker-verification"></a>說話者驗證

語音具有與個人相關聯的獨特性。  在話務中心和 Web 服務等案例中，應用程式可以將語音作為驗證的額外因素。

說話者驗證 API 是一種智慧型工具，可運用使用者的語音和語音複雜密碼，來協助驗證使用者。

### <a name="enrollment"></a>申請

說話者驗證註冊因文字而異，這表示說話者需要選擇在註冊和驗證階段期間所用的特定複雜密碼。

在說話者註冊階段中，會記錄說話者說出特定片語的語音。 系統在辨識選擇的片語時，會擷取語音特徵以形成唯一的語音簽章。 同時，此說話者註冊資料將用於驗證說話者。 說話者註冊資料會儲存在安全的系統中。 客戶可控制應保留資料的時間長度。 客戶可以透過 API 呼叫來建立、更新，以及移除個別說話者的註冊資料。  刪除訂閱後，與該訂閱相關聯的所有說話者註冊資料也會一併刪除。

客戶應確保他們從使用者獲得適當權限，以進行說話者驗證。

### <a name="verification"></a>驗證

在驗證階段，客戶應該使用與待驗證個人相關聯的識別碼來呼叫說話者驗證 API。  此服務會從輸入語音錄製中擷取語音特徵和複雜密碼。 然後，它會針對客戶要搜尋的說話者，將特徵與說話者註冊資料中的對應元素進行比較，並確定是否相符。  回應會透過不同信賴等級傳回「接受」或「拒絕」。  接著客戶會判斷如何使用結果，以協助決定此人是否為註冊的說話者。

閾值信賴等級應根據案例和其他使用的驗證因素來設定。 我們建議您對信賴等級進行測試，並針對每個應用程式考量適當的設定。 API 的用途不是為了判斷音訊是來自實際人員，或是來自已註冊說話者的模擬或錄製內容。

這項服務不會保留在驗證階段傳送給該服務的語音錄製或擷取的語音特徵。

如需說話者驗證的詳細資訊，請參閱[說話者 - 驗證](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652) API。

## <a name="speaker-identification"></a>說話者識別

在一群已註冊的說話者中，應用程式可以使用語音來識別「誰在說話」。 說話者辨識 API 可用於滿足生產力、個人化和話務中心轉譯等案例。

### <a name="enrollment"></a>申請

說話者識別註冊與文字無關，這表示說話者在音訊中什麼都可以說。 不需要複雜密碼。

系統在註冊階段會錄下說話者的聲音，並擷取語音特徵以形成唯一的語音簽章。 擷取的語音音訊和特徵會儲存在安全的系統中。 客戶可控制應保留資料的時間長度。 客戶可以透過 API 呼叫，針對個別說話者來建立、更新和移除該說話者的註冊資料。 刪除訂閱後，與該訂閱相關聯的所有說話者註冊資料也會一併刪除。

客戶應確保他們從使用者獲得適當權限，以進行說話者驗證。

### <a name="identification"></a>識別

在識別階段，說話者辨識服務會從輸入語音錄製擷取語音特徵。 然後，該服務會將特徵與指定說話者清單的註冊資料進行比較。 找到已註冊說話者的相符項目時，回應會傳回具有信賴等級的說話者識別碼。  否則，當沒有任何說話者符合已註冊的說話者時，回應會傳回「拒絕」。

閾值信賴等級應該根據案例進行設定。 我們建議您對信賴等級進行測試，並針對每個應用程式考量適當的設定。 API 的用途不是為了判斷音訊是來自實際人員，或是來自已註冊說話者的模擬或錄製內容。

這項服務不會保留在識別階段傳送給該服務的語音錄製或擷取的語音特徵。

如需說話者識別的詳細資訊，請參閱 [說話者 - 識別](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e) API。
