---
title: 範圍 API
description: 本指南說明常用的範圍方法。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/5/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 39770fe7aa7b11cae03304fda8901e81e0f1877a
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208401"
---
# <a name="horizon-api"></a>範圍 API 

本指南說明常用的範圍方法。

### <a name="getting-more-information"></a>取得詳細資訊

如需使用適用于 IoT 平臺的範圍和 Defender 的詳細資訊，請參閱下列資訊：

- 針對 (NODE.JS) SDK 的水準開放開發環境，請洽詢您的 Defender for IoT 代表。
- 如需支援和疑難排解資訊，請聯絡 <support@cyberx-labs.com> 。

- 若要從 Defender for IoT 主控台存取 Defender for IoT 使用者指南，請選取， :::image type="icon" source="media/references-horizon-api/profile.png"::: 然後選取 [ **下載使用者指南**]。


## `horizon::protocol::BaseParser`

所有外掛程式的摘要。 這包含兩種方法：

- 用於處理您上面定義的外掛程式篩選。 如此一來，範圍就知道如何與剖析器通訊。
- 用於處理實際的資料。

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

針對您的外掛程式所呼叫的第一個函式會建立一個剖析器實例，以供範圍辨識並註冊。

### <a name="parameters"></a>參數 

無。

### <a name="return-value"></a>傳回值

shared_ptr 剖析器實例。

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

針對上述註冊的每個外掛程式都會呼叫此函式。 

在大部分的情況下，這會是空的。 擲回範圍例外狀況，以得知發生錯誤。

### <a name="parameters"></a>參數 

- 包含 dissect_as 結構的地圖，如其他外掛程式的 config.js中所定義，而該外掛程式想要向您註冊。

### <a name="return-value"></a>傳回值 

Uint64_t 的陣列，也就是已處理為一種 uint64_t 的註冊。 這表示在對應中，您將會有一個埠清單，其值將會是 uin64_t。

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Main 函數。 具體來說，每次有新的封包到達剖析器時，外掛程式的邏輯。 系統會呼叫此函式，所有與封包處理相關的專案都應該在此進行。

### <a name="considerations"></a>考量

您的外掛程式應具備執行緒安全，因為此函式可從不同的執行緒呼叫。 最好的方法是定義堆疊上的所有專案。

### <a name="parameters"></a>參數

- 負責儲存資料和建立 SDK 相關物件（例如 ILayer 和欄位）的 SDK 控制單位。
- 讀取原始封包資料的 helper。 它已設定為您在 config.js中定義的位元組順序。

### <a name="return-value"></a>傳回值 

處理的結果。 這可能是 *成功*、 *格式不正確* 或不 *穩定*。

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

將處理常式標示為清理失敗，這表示目前的通訊協定無法辨識封包，而且如果有任何註冊在相同的篩選器上，範圍應該將它傳遞給其他剖析器。

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

建構函式

### <a name="parameters"></a>參數 

- 定義記錄範圍所使用的錯誤碼，如 config.js中所定義。

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

結果格式不正確，表示我們已辨識出封包為我們的通訊協定，但某些驗證 (保留的位已開啟，或某些欄位缺少) 。

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

建構函式

### <a name="parameters"></a>參數  

- 錯誤碼，如 config.js中所定義。

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

通知成功處理的範圍。 成功時，會接受封包，資料屬於美國，而且所有資料都會解壓縮。

## `horizon::protocol::SuccessResult()`

建構函式。 建立基本的成功結果。 這表示我們不知道任何關於封包的方向或任何其他中繼資料。

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

建構函式。

### <a name="parameters"></a>參數 

- 封包的方向（如果已識別）。 值可以是 *要求* 或 *回應*。

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

建構函式。

### <a name="parameters"></a>參數

- 封包的方向（如果我們已識別），可以是 *要求*、 *回應*。
- 警告： 這些事件將不會失敗，但會收到範圍的通知。

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

建構函式。

### <a name="parameters"></a>參數 

-  警告： 這些事件將不會失敗，但會收到範圍的通知。

## `HorizonID HORIZON_FIELD(const std::string_view &)`

將字串型參考轉換為功能變數名稱 (例如，function_code) 為 HorizonID。

### <a name="parameters"></a>參數 

- 要轉換的字串。

### <a name="return-value"></a>傳回值

- 從字串建立的 HorizonID。

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

建立新的圖層，如此一來，就會知道外掛程式要儲存一些資料。 這是您應該使用的基底儲存體單位。

### <a name="return-value"></a>傳回值

已建立之圖層的參考，因此您可以在其中新增資料。

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

取得欄位管理物件，此物件負責在不同的物件上建立欄位，例如在 ILayer 上。

### <a name="return-value"></a>傳回值

管理員的參考。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

在具有所要求識別碼的圖層上，建立64位的新數值欄位。

### <a name="parameters"></a>參數 

- 您稍早建立的層。
- **HORIZON_FIELD** 宏所建立的 HorizonID。
- 您要儲存的原始值。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

在具有所要求識別碼的圖層上建立新的字串欄位。 記憶體將會移動，所以請務必小心。 您將無法再使用此值。

### <a name="parameters"></a>參數  

- 您稍早建立的層。
- **HORIZON_FIELD** 宏所建立的 HorizonID。
- 您要儲存的原始值。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

使用要求的識別碼，在圖層上 (位元組陣列) 欄位建立新的原始值。 記憶體將會移動，所以請注意，您將無法再使用此值。

### <a name="parameters"></a>參數

- 您稍早建立的層。
- **HORIZON_FIELD** 宏所建立的 HorizonID。
- 您要儲存的原始值。

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

在具有所要求識別碼之指定型別的層級上，建立陣列值 (陣列) 欄位。

### <a name="parameters"></a>參數

- 您稍早建立的層。
- **HORIZON_FIELD** 宏所建立的 HorizonID。
- 將儲存在陣列中的值型別。

### <a name="return-value"></a>傳回值

參考您應該將值附加至其中的陣列。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

將新的整數值附加至稍早建立的陣列。

### <a name="parameters"></a>參數

- 稍早建立的陣列。
- 要儲存在陣列中的原始值。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

將新的字串值附加至稍早建立的陣列。 記憶體將會移動，所以請注意，您將無法再使用此值。

### <a name="parameters"></a>參數

- 稍早建立的陣列。
- 要儲存在陣列中的原始值。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

將新的原始值附加至稍早建立的陣列。 記憶體將會移動，所以請注意，您將無法再使用此值。

### <a name="parameters"></a>參數

- 稍早建立的陣列。
- 要儲存在陣列中的原始值。

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

檢查緩衝區至少包含 X 個位元組。

### <a name="parameters"></a>參數

應存在的位元組數目。

### <a name="return-value"></a>傳回值

如果緩衝區至少包含 X 個位元組，則為 True。 否則，會是 `False`。

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

根據位元組順序，從緩衝區讀取 uint8 值 (1 位元組) 。

### <a name="return-value"></a>傳回值

從緩衝區讀取的值。

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

根據位元組順序，從緩衝區讀取 uint16 值 (2 個位元組) 。

### <a name="return-value"></a>傳回值

從緩衝區讀取的值。

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

根據位元組順序，從緩衝區讀取 uint32 值 (4 位元組) 。

### <a name="return-value"></a>傳回值

從緩衝區讀取的值。

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

根據位元組順序，從緩衝區讀取 uint64 值 (8 個位元組) 。

### <a name="return-value"></a>傳回值

從緩衝區讀取的值。

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

以指定的大小讀入預先配置的記憶體，實際上會將資料複製到您的記憶體區域。

### <a name="parameters"></a>參數 

- 要將資料複製到其中的記憶體區域。
- 記憶體區域的大小，此參數也會定義要複製的位元組數目。

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

從緩衝區讀入字串。

### <a name="parameters"></a>參數 

- 應讀取的位元組數目。

### <a name="return-value"></a>傳回值

字串之記憶體區域的參考。

## `size_t horizon::general::IDataBuffer::getRemainingData()`

告訴您緩衝區中剩餘的位元組數目。

### <a name="return-value"></a>傳回值

緩衝區的剩餘大小。

## `void horizon::general::IDataBuffer::skip(size_t)`

略過緩衝區中的 X 位元組。

### <a name="parameters"></a>參數

- 要略過的位元組數目。
