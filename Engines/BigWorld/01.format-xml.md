# Сжатые файлы XML

XML файл, сжатый по словарю, т.е. все теги в документе собраны в уникальный нумерованный список.

## Заголовок файла

Начинается с сигнатуры

```
HEADER {
  uint32 signature   // сигнатура файла 0x62A14E45 (ANSI "EN¡b")
  byte version       // версия файла, всегда 0x00
}
```

## Словарь тегов

Далее следует список используемых тегов. Количество элементов определяется пустой строкой, т.е. читаем из потока пока не получим пустую строку.

```
DICT {
  cstring[] tags
}
```

## Данные

```
ELEMENT {
  uint16 childNum
  DATA_DESCRIPTOR dataDescriptor
  ELEMENT_DESCRIPTOR[childNum] elementDescriptor
  DATA data
  DATA[childNum] childData  
}
```

### Дескриптор данных

Беззнаковое 32-битное целое число, младшие 28 бит определяют позицию конца данных, старшие 4 - тип данных

```
DATA_DESCRIPTOR {
  bits[28] offset   // конец данных
  DATA_TYPE type    // тип данных
}
```

### Дескриптор элемента

```
ELEMENT_DESCRIPTOR {
  uint16 tagIndex              // индекс тега в словаре
  DATA_DESCRIPTOR descriptor   // дескритор данных
}
```

### Чтение данных DATA  


Длина данных определяется как разница между текущим положением в потоке и позицией offset в дескрипторе.

`size =  dataDescriptor->offset - current_position`

#### Типы данных

```
bits[4] DATA_TYPE (
  0x0 = DATA_ELEMENT   // вложенный элемент
  0x1 = DATA_STRING    // строка
  0x2 = DATA_UINT      // целое без знака
  0x3 = DATA_FLOAT     // число с плавающей точкой
  0x4 = DATA_BOOL      // булево значение
  0x5 = DATA_BINARY    // двоичные данные
)
```

Для DATA\_ELEMENT читаем вложенный ELEMENT.  
Для DATA\_STRING читаем строку длиной size.  
Для DATA\_UINT в зависимости от длины читаем uint8, uint16, uint32 и uint64 соответственно.  
Для DATA\_FLOAT читаем массив чисел длиной size / 4.  
Для DATA\_BOOL читаем `size ? TRUE : FALSE`, т.е. если длина равна нулю, то это FALSE, иначе TRUE.  
Для DATA\_BINARY читаем size байтов.