# Формат файлов CHUMP

Файлы `chump` и `cdp` используются для хранения ресурсов "Trainz" в компактном виде.

Файлы chump используется для хранения сжатых ресурсов config.txt, а cdp содержат набор ресурсов разного типа. Внутреннее строении обоих типов одинаковое.

## Заголовок файла  


Файл начинается сигнатуры ACS$.

```
HEADER {
  char[4] signature   // сигнатура "ACS$"
  uint32  version     // версия, текущая 0x00000001
  uint32  reserved
  uint32  dataSize    // размер данных
}
```

## Содержимое файла  


Следует за заголовком и имеет формат

```
CHUNK {
  uint32 dataSize       // длина блока данных
  byte nameLen          // длина имени
  char[nameLen] name    // имя блока, с завершающим нулём
  CHUNK_TYPE type       // тип данных
  byte[] data           // данные, длина = dataSize - nameLen - 2 
}
```

Типы используемых данных

```
uint8 CHUNK_TYPE (
  0x00 = CHUNK_TYPE_CONTAINER   // контейнер с вложенными блоками
  0x01 = CHUNK_TYPE_INTEGER     // целое число или их массив (в зависимости от длины данных)
  0x02 = CHUNK_TYPE_FLOAT       // float или их массив (в зависимости от длины данных)
  0x03 = CHUNK_TYPE_STRING      // строка с завершающим нулём
  0x04 = CHUNK_TYPE_BINARY      // двочные данные (файлы)
  0x05 = CHUNK_TYPE_NULL        // пустое значение
  0x0D = CHUNK_TYPE_KUID        // Kuid
)
```

Числовые типы могут содержать как одно значение, так и массив. Зависит от длины данных.

## Структура файлов CDP

CDP содержит набор ресурсов. Внутреннее строение файла

```
assets
{
    <kuid:474195:100073>
    {
        username        "My Asset"
        kuid            <kuid:474195:100073>
        mesh-table
        {
            default
            {
                mesh            "test.im"
                auto-create     1
            }
        }
        compression     "LZSS"
        files
        {
            test.im     #-# binary-data #-#
        }
        dsid            "7248:1375824714"
    }
}
contents-table
{
    0       <kuid:474195:100073>
}
kuid-table
{
}
obsolete-table
{
}
kind            "archive"
package-version 1
username        "unknown"

```

Содержит 4 контейнера и 3 обязательных значения.

### Контейнер assets

Содержит набор контейнеров для всех ресурсов, сохранённых в cdp файле.

Каждый контейнер имеет имя в виде kuid для ресурса и содержит копию config.txt. В конец конфигурации в контейнере `files` записаны сжатые файлы ресурса. Формат сжатия указан в `compression` и всегда равен "LZSS".

Если ресурс оригинальный, т.е. скачан из DLS, то добавляется ещё `dsid` c идентификатором Download Station ID, <span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">чтобы Trainz мог распознать и установить подлинный ресурс.</span></span></span>

### <span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">Контейнер contents-table</span></span></span>

Хранит список kuid всех ресурсов в cdp файле.

### Контейнеры kuid-table и obsolete-table

Присутствуют всегда. Содержимое не представляет интереса(?).

### Обязательные значения

- **kind** - должен быть "archive"
- **package-version** - всегда 1
- **username** - всегда "unknown"

## Источники

1. [https://github.com/SilverGreen93/CDPExplorer/blob/master/chump-format.md](https://github.com/SilverGreen93/CDPExplorer/blob/master/chump-format.md)