# Qt

## 用windeployqt工具对Qt程序进行打包

**Windeployqt**

```batch
cd $QTDIR\bin\
windeployqt.exe D:\a\xx.exe
:: 或者：
cd D:\a\
$QTDIR\bin\windeployqt.exe xx.exe
:: 所有依赖的Qt动态库都会复制到xx.exe所在目录下
```

xx.exe依赖的所有动态库都会拷贝到xx.exe所在的目录下

**注意**：依赖的mingw编译器的动态库不会复制，运行xx.exe更新错误提示复制缺的动态库就行了。通常下面三个动态库都是必须的。

> libgcc_s_seh-1.dll
> libstdc++-6.dll
> libwinpthread-1.dll

**特别注意**：缺libwinpthread-1.dll库不会报缺库错误，而是报“应用程序无法正常启动（0xc000007b）”错误。

可选项：

```
Options:
  -?, -h, --help            Displays this help.
  -v, --version             Displays version information.  --dir <directory>         Use directory instead of binary directory.
  --libdir <path>           Copy libraries to path.
  --plugindir <path>        Copy plugins to path.
  --debug                   Assume debug binaries.
  --release                 Assume release binaries.
  --pdb                     Deploy .pdb files (MSVC).
  --force                   Force updating files.
  --dry-run                 Simulation mode. Behave normally, but do not
                            copy/update any files.
  --no-patchqt              Do not patch the Qt5Core library.
  --no-plugins              Skip plugin deployment.
  --no-libraries            Skip library deployment.
  --qmldir <directory>      Scan for QML-imports starting from directory.
  --no-quick-import         Skip deployment of Qt Quick imports.
  --no-translations         Skip deployment of translations.
  --no-system-d3d-compiler  Skip deployment of the system D3D compiler.
  --compiler-runtime        Deploy compiler runtime (Desktop only).
  --no-compiler-runtime     Do not deploy compiler runtime (Desktop only).
  --webkit2                 Deployment of WebKit2 (web process).
  --no-webkit2              Skip deployment of WebKit2.
  --json                    Print to stdout in JSON format.
  --angle                   Force deployment of ANGLE.
  --no-angle                Disable deployment of ANGLE.
  --no-opengl-sw            Do not deploy the software rasterizer library.
  --list <option>           Print only the names of the files copied.
                            Available options:
                             source:   absolute path of the source files
                             target:   absolute path of the target files
                             relative: paths of the target files, relative
                                       to the target directory
                             mapping:  outputs the source and the relative
                                       target, suitable for use within an
                                       Appx mapping file
  --verbose <level>         Verbose level (0-2).
```

## 翻译（国际化）

1. 生成ts文件
   
   **QtCreator**
* 在pro中加上TRANSLATIONS设置，如TRANSLATIONS += trans_zh_CN.ts，可以设置多个，每个对应生成的ts文件名

* 并在Qt Creator中点击菜单栏-工具-外部-Qt语言家-更新翻译，默认在pro目录生成对应的ts文件
  
   **VS**

* 扩展-Qt VS Tools-Create New Translation File

* 右键新生成的ts文件，选择lupdate
2. 翻译ts文件
   
   使用Qt目录下的Linguist软件或文本编辑器翻译ts文件

3. 生成qm文件
   
   点击Qt Linguist文件-保存，再点文件-发布，
   
   或者保存后点QtCreator菜单栏-工具-外部-Qt语言家-发布翻译，
   
   或者VS右键保存的ts文件，选择lrelease

4. 使用翻译文件
   
   ```cpp
   QApplication app(argc, argv);
   QTranslator translator;
   // 直接加载指定的翻译文件
   translator.load("trans_zh_CN.qm");
   // 按系统设置自动加载不同的翻译文件，第二个参数是qm文件的相对目录
   translator.load("trans_" + QLocale::system().name(), "translations");
   app.installTranslator(&translator);
   ```

## 单元测试

1. 基本设置

pro工程文件加QT += testlib

```cpp
// 1. 包含头文件并且测试用例类必须继承QObject，测试函数必须声明为private slots
#include <QtTest/QtTest>

class TestQString : public QObject
{
    Q_OBJECT
private slots:
    void toUpper()
    {
        QString str = "Hello";
        QVERIFY(str.toUpper() == "HELLO");
    }
};

// 2. 声明运行单元测试所需的main函数
QTEST_MAIN(TestQString)
// 3. 如果测试用例类的声明和定义在同一个cpp里还需要包含对应的moc文件
#include "testqstring.moc"
```

2. 数据驱动的单元测试

```cpp
class TestQString: public QObject
{
    Q_OBJECT

private slots:
    void toUpper_data();    // toUpper测试的数据准备，必须以_data结尾
    void toUpper();
};
// 整个数据就是一个表格
void TestQString::toUpper_data()
{
    QTest::addColumn<QString>("string");
    QTest::addColumn<QString>("result");

    QTest::newRow("all lower") << "hello" << "HELLO";
    QTest::newRow("mixed")     << "Hello" << "HELLO";
    QTest::newRow("all upper") << "HELLO" << "HELLO";
}
// 测试会循环3次
void TestQString::toUpper()
{
    QFETCH(QString, string);
    QFETCH(QString, result);

    QCOMPARE(string.toUpper(), result);
}
```

## 属性系统 Q_PROPERTY

```cpp
Q_PROPERTY(bool focus READ hasFocus)
Q_PROPERTY(bool enabled READ isEnabled WRITE setEnabled)
Q_PROPERTY(QCursor cursor READ cursor WRITE setCursor RESET unsetCursor)
```

**动态属性**

```cpp
bool setProperty(QString name, QVariant value)
QVariant property(QString name)
```

使用setProperty()设置属性时，如果name不存在，会新建一个属性，该属性就为**动态属性**，该属性只能通过property()读取。

**在qss中使用**

```css
*[required="true"] {
    background-color: lime;
}
```

**注意**

Q_PROPERTY只是声明cursor是一个属性，cursor、setCursor、unsetCursor等函数依然需要自己在类内进行声明和定义。

## QElapsedTimer 计算时间间隔ns

```cpp
elapsed()         // 毫秒
nsecsElapsed()    // 纳秒
```

elapsed 过去的，经过的

## processEvents 强制处理Qt事件

如果某处理需要长时间占用CPU时间片，就可以偶尔调用该函数将事件队列里未处理的时间派发出去，让事件接收对象处理，避免界面卡住。

void QCoreApplication::processEvents(QEventLoop::ProcessEventsFlags flags = QEventLoop::AllEvents)

## QScrollArea和AdjustSize()

- **QScrollArea**不会因为自动布局而改变大小，必须手动调用setFixedSize()、setFixedHeight()、setFixedWidth()改变其大小。
  
  其默认会内嵌一个QWidget，该widget大小大于QScrollArea的大小时就会显示滚动条。

- 当一个窗口内的某个控件隐藏时，其他同级（在同一个layout里）的控件会自动占有空出的空间，整个窗口的大小是不会变的。如果想要整个窗口改变大小适应控件显隐造成的大小改变，可以调用**adjustSize()** 函数，该函数会递归调整子窗口，适应各自内部的内容。

## 文件系统操作和文件读写

### 特定格式文件的读写

#### xml

* **QDomDocument**

* **QXmlStreamReader、QXmlStreamWriter**流式读写

#### json

相比于xml，json文件编解码难度低，文件更小。

* **QJsonDocument**
  
  读写json文件
- **QJsonArray、QJsonObject、QJsonValue**
  
  分别封装json的数组、对象和值类，json有6种基本数据类型：bool、double、string、array、object、 null。

#### 图片

* **QImage、QPixmap**

* **QImageReader、QImageWriter**

### 目录和文件操作

* QCoreApplication

* QFile

* QFileInfo

* QDir

* QTemporaryDir

* QTemporaryFile

* QFileSystemWatcher

#### QCoreApplication

* QString applicationDirPath()

* QString applicationFilePath()

* QString applicationName() 无后缀

* QStringList libraryPath()

* void addLibraryPath(QString& path)

* QString organizationName() 组织名

* void setOrganizationName(QString& name)

#### QFile

#### QFileInfo

#### QTemporaryDir

#### QTemporaryFile

#### QFileSystemWatcher

当目录下发生新建、删除文件等操作时，会发射directoryChanged()信号

当所监视的文件发生修改、重命名等操作时，会发射fileChanged()信号

### 读写文本文件

#### 用QFile读写文本文件

read()

readChar()

readLine()

readAll()

write()

putChar()

flush() 将缓存写入文件

bool atEnd() 判断是否到达文件末尾

open()

close()

#### 用QSaveFile保存文件

会生成一个临时文件，commit时才写入修改目标文件。

commit()

cancelWriting()

#### 用QFile和QTextStream读写文本文件

QTextStream(QIODevice* device)

支持<<和>>操作

### 读写二进制文件 QDataStream
