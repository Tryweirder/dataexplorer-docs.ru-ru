---
title: predict_onnx_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция predict_onnx_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2020
ms.openlocfilehash: 9cc70418b39ece5c33d6059c7fc90c47f3039ece
ms.sourcegitcommit: 5aba5f694420ade57ef24b96699d9b026cdae582
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90998982"
---
# <a name="predict_onnx_fl"></a>predict_onnx_fl()

Функция `predict_onnx_fl()` прогнозирует использование существующей обученной модели машинного обучения. Эта модель преобразована в формат [ONNX](https://onnx.ai/) , сериализована в строку и сохранена в стандартной таблице обозреватель данных Azure.

> [!NOTE]
> * `predict_onnx_fl()` — Это [определяемая пользователем функция (UDF)](../query/functions/user-defined-functions.md).
> * Эта функция содержит встроенный язык Python и требует [включения подключаемого модуля Python ()](../query/pythonplugin.md#enable-the-plugin) в кластере. Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`T | invoke predict_onnx_fl(`*models_tbl* `,` *MODEL_NAME* `,` *features_cols* `,` *pred_col*`)`

## <a name="arguments"></a>Аргументы

* *models_tbl*: имя таблицы, содержащей все сериализованные модели. Эта таблица должна содержать следующие столбцы:
    * *имя*: имя модели.
    * *Метка*времени: время обучения модели
    * *model*: строковое представление сериализованной модели
* *MODEL_NAME*: имя конкретной модели для использования.
* *features_cols*: динамический массив, содержащий имена столбцов функций, которые используются моделью для прогнозирования.
* *pred_col*: имя столбца, в котором хранятся прогнозы.

## <a name="usage"></a>Использование

`predict_onnx_fl()` определяемая пользователем [Табличная функция](../query/functions/user-defined-functions.md#tabular-function) , применяемая с помощью [оператора Invoke](../query/invokeoperator.md). Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let predict_onnx_fl=(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code =
    '\n'
    'import pickle\n'
    'import binascii\n'
    '\n'
    'smodel = kargs["smodel"]\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    'bmodel = binascii.unhexlify(smodel)\n'
    '\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    '\n'
    'import onnxruntime as rt\n'
    'sess = rt.InferenceSession(bmodel)\n'
    'input_name = sess.get_inputs()[0].name\n'
    'label_name = sess.get_outputs()[0].name\n'
    'df1 = df[features_cols]\n'
    'predictions = sess.run([label_name], {input_name: df1.values.astype(np.float32)})[0]\n'
    '\n'
    'result = df\n'
    'result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])'
    '\n'
    ;
    samples | evaluate python(typeof(*), code, kwargs)
};
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=bool(0)
| invoke predict_onnx_fl(ML_Models, 'ONNX-Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [функцию. Create](../management/create-function.md). Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Однократная установка

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\ML", docstring = "Predict using ONNX model")
predict_onnx_fl(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code =
    '\n'
    'import pickle\n'
    'import binascii\n'
    '\n'
    'smodel = kargs["smodel"]\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    'bmodel = binascii.unhexlify(smodel)\n'
    '\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    '\n'
    'import onnxruntime as rt\n'
    'sess = rt.InferenceSession(bmodel)\n'
    'input_name = sess.get_inputs()[0].name\n'
    'label_name = sess.get_outputs()[0].name\n'
    'df1 = df[features_cols]\n'
    'predictions = sess.run([label_name], {input_name: df1.values.astype(np.float32)})[0]\n'
    '\n'
    'result = df\n'
    'result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])'
    '\n'
    ;
    samples | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=bool(0)
| invoke predict_onnx_fl(ML_Models, 'ONNX-Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

---

Матрица путаницы:
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Occupancy   pred_Occupancy  n
TRUE        TRUE            3006
FALSE       TRUE            112
TRUE        FALSE           15
FALSE       FALSE           9284
```
