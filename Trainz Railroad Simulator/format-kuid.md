# Формат KUID

## <span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">KUID</span></span></span>

<span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">KUID — это идентификатор, используемый для определения конкретного ресурса в Trainz.</span></span> <span class="jCAhz ChMk0b"><span class="ryNqvb">Стандартный человекочитаемый формат: &lt;kuid:474195:10010&gt; или &lt;kuid2:474195:10010:1&gt;. Общий формат: &lt;kuid2:UserID:ContentID:Version&gt;.</span></span> </span>

<span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">Если версия равна 0, Trainz отображает kuid в устаревшем формате kuid (&lt;kuid:), иначе отображает его в формате kuid2 (&lt;kuid2:) </span></span></span>

## <span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">Формат хранения Kuid</span></span></span>

<span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">Теоретически хранится в виде восьми байтов UUUV</span></span></span>CCCC, где UUU это 25 бит UserID, V - 7 бит Version и CCCC - ContentID.

```
Kuid {
  int32 low    // UserID и Version
  int32 high   // ContentID
}
```

<span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">Из-за странного устройства и поведения Kuid, UserID и Version можно вычислить вот так</span></span></span>

```
if( low >= 0 ) {
  UserID  = low & 0x1FFFFFF;
  Version = low >> 25;
}
else {
  UserID = low;
  Version = 0;
}
```

## Исключения  


Если UserID отрицательный, то Version будет неверный, потому что в этом случае UserID захватывает старший байт и устанавливает Version в 127.

Так же исключения возникают при переполнении. Например, если UserID больше чем 16777215, допустим 16777216, возникнет переполнение и биты Version будут сброшены в 0. Такой KUID будет показан в Trainz как &lt;kuid2:-16777216:12345:0&gt; вместо &lt;kuid:-16777216:12345&gt;.

Несмотря на то, что kuid хранятся в виде UUUVNNNN, некоторые файлы (\*.trc, \*.trk, \*.gnd...) и assets.tdx хранят kuid в формате NNNNUUUV, меняя старшие и младшие 4 байта местами (a-b-c-d:e-f-g-h -&gt; e-f-g-h:a-b-c-d).

## Источники

1. [https://github.com/SilverGreen93/CDPExplorer/blob/master/kuid-format.md](https://github.com/SilverGreen93/CDPExplorer/blob/master/kuid-format.md)