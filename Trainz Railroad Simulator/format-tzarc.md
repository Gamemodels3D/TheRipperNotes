# Формат архивов TZARC

### Заголовок архива

Файл архива начинается с сигнатуры "TZac".

```
HEADER {
  char[4] signature // "TZac"
  uint32be variant    // 1 или 2   
}
```

### Распаковка сжатых данных

Если значение `variant` равно 0x00000002, то файлы в архиве сжаты методом zlib Сжатие проводится блоками по 65000 байт.

[![Сжатие tzarc.png](http://knowledge.local/uploads/images/gallery/2024-09/scaled-1680-/szatie-tzarc.png)](http://knowledge.local/uploads/images/gallery/2024-09/szatie-tzarc.png)

Формат сжатых данных:

```
COMPRESSED_DATA {
  int64 uncompressedSize
  uint32 chunkNum
  uint16 chunkSize[chunkNum]
  byte[] compressedData
}
```

Размер chunkSize на 1 меньше реального размера блока.   
Для получения данных необходимо chunkNum раз прочитать и распаковать chunkSize+1 байт сжатых данных .

### Данные файлов

Сами данные хранятся в следующем виде:

```
UNCOMPRESSED_DATA {
  uint32be fileNum
  FAT_ENTRY {
    byte nameLength 
    char[nameLenght] fileName
    uint64be fileSize
  }[fileNum] fat
  uint32be unknown
  byte[fileNum][] fileData
}
```

### Источники

1. TZarchiver. Utility aplication to read and write Trainz tzarc files [https://github.com/SilverGreen93/TZarchiver](https://github.com/SilverGreen93/TZarchiver)