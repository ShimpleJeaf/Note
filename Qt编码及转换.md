# Qt编码

* QString内部使用Unicode编码（utf-16）

* QString::fromStdString()调用的是fromUtf8，即把传入参数认为是utf8编码

* QString::toStdString()和fromStdString()一样

* utf8，兼容ascii码
  
  | Binary   | Hex        | Comments                                                                  |
  | -------- | ---------- | ------------------------------------------------------------------------- |
  | 0xxxxxxx | 0x00..0x7F | Only byte of a 1-byte character encoding<br>**单字节字符**                     |
  | 10xxxxxx | 0x80..0xBF | Continuation byte: one of 1-3 bytes following the first<br>**多字节字符的后续字节** |
  | 110xxxxx | 0xC0..0xDF | First byte of a 2-byte character encoding<br>**双字节字符的首字节**                |
  | 1110xxxx | 0xE0..0xEF | First byte of a 3-byte character encoding<br>**三字节字符的首字节**                |
  | 11110xxx | 0xF0..0xF7 | First byte of a 4-byte character encoding<br>**四字节字符的首字节**                |

# QTextCodec 编码转换类


