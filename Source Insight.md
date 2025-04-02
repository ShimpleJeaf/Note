如果一个地方代码无法解析，通常都是前面的宏变量不对或宏展开不对，可以用以下方法解决

# 条件解析

Source Insight可以手动设置预编译变量的值，即#if HAS_CXX_17 后面宏变量的值，1为真，0为假。

使用方法：

1. 在代码中选中要定义的宏变量

2. 右键 > Edit Condition

3. Edit Condition Set一栏选仅本项目或全局

4. Condition一栏填入宏名，通常会自动填入选中的宏

5. Value填宏的值，也可以用下面的按钮选择Set Flase、Set True或Ignore

6. 最后选ok，确认。

7. 会弹出一个重新解析代码的弹窗，确认即可。

8. 如果需要编辑多个宏，在第7步可以选否，在最后一个宏编辑完成后再统一解析一次。
   
   或者在3步时选右边的Edit List手动添加多个宏定义。

注意：千万不要选扫描文件中的宏（第3步中右边的Scan Files)，这会将所有的宏（几百个，自己之前设置的哪几个，完全找不到了，呵呵）都添加到列表里，还不能撤销 : (

# 解析Qt代码

Qt用了很多复杂的宏，Source Insight自动解析不完全，很多地方的代码都不能跳转。

Source Insight提供了手动的宏展开解析，定义在C.tom文件中。只需要将宏写进文件，Source Insight就能解析了，而且手动宏的优先级高于自动宏解析。

C.tom的位置：F:\Users\Administrator\Documents\Source Insight 4.0

如果找不到，用Everything全局搜索。

```tom
; C.tom
; Qt Src
; 分号开头的行为注释

QT_VERSION_MAJOR 5
QT_VERSION_MINOR 15
QT_VERSION_PATCH 2

__declspec(x)
Q_DECL_EXPORT __declspec(dllexport)
Q_CORE_EXPORT
Q_WIDGETS_EXPORT

Q_OBJECT public:
Q_GADGET public:

Q_DECLARE_PRIVATE(Class) Class##Private* d_func(); const Class##Private* d_func() const;
Q_DECLARE_PRIVATE_D(d, Class) Class##Private* d_func(); const Class##Private* d_func() const;

Q_PROPERTY(...) public:
Q_PROPERTY(text) public:
QDOC_PROPERTY(text) public:


QT_BEGIN_NAMESPACE
QT_END_NAMESPACE
QT_CONFIG(feature) (1/QT_FEATURE_##feature == 1)

Q_FLAG(Flags)
Q_DECLARE_FLAGS(Flags, Enum) typedef QFlags<Enum> Flags;
Q_DECLARE_OPERATORS_FOR_FLAGS(Flags)

QT_Q_ENUM(ENUM)
Q_ENUM(ENUM)
Q_DECL_ENUMERATOR_DEPRECATED
Q_DECL_ENUMERATOR_DEPRECATED_X(x)

QT_DEPRECATED_X(text)
QT_DEPRECATED_SINCE(major, minor) 1

Q_D(Class) Class##Private * const d = d_func()
Q_Q(Class) Class * const q = q_func()
Q_UNUSED(x) (void)x;
Q_DISABLE_COPY(Class) Class(const Class &) = delete; Class &operator=(const Class &) = delete;

_MSVC_LANG 201401L
_EXPORT_STD
_NODISCARD
```
