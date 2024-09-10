# boost

## 单元测试

```cpp
// 1. 定义测试用例main函数，BoostStudyTest是测试名字，可以为空
// 必须在包含单元测试头文件之前定义，且只能定义一次
#define BOOST_TEST_MODULE BoostStudyTest

// 2. boost单元测试有三种使用方式：仅头文件、静态链接、动态链接
// 2.1 仅头文件
#include <boost/test/included/unit_test.hpp>
// 2.2 静态链接
#include <boost/test/unit_test.hpp>
// 2.3 动态链接
#define BOOST_TEST_DYN_LINK
#include <boost/test/unit_test.hpp>

// 包含待测试的目标文件，如果是测试库，可以通过静态或动态链接的方式进行
// 也可以通过直接包含cpp文件的方式进行，看情况选用
#include "interpreter.cpp"

// 测试对象：Interpreter类

// 3. 测试套件夹具，就是测试套件用的全局变量
// 另外还有测试用例夹具
// 夹具构造在每个测试用例运行前执行，夹具析构在每个测试用例运行完后执行
struct interpreter_fixture
{
    interpreter_fixture()
    {
        valueMap["a"] = 2;
        valueMap["b"] = 3;
        valueMap["c"] = 4;
        valueMap["d"] = 5;
    }
    ~interpreter_fixture() {}
    map<string, double> valueMap;
};

// 4. 测试套件
// 测试套件的起始位置，要以BOOST_AUTO_TEST_SUITE_END()结尾，将测试用例括在中间
// 有测试夹具的声明方式，第一个参数是测试套件名字，第二个参数是测试套件夹具类名
BOOST_FIXTURE_TEST_SUITE(suite_interpreter, interpreter_fixture)
// 无测试夹具的声明方式
BOOST_AUTO_TEST_SUITE(suite_interpreter)

// 5. 测试用例
BOOST_AUTO_TEST_CASE(test_find_first_char)
{
    string str = "a+b*c/d-a";
    char c;
    auto pos = find_first_char(str, "*/", c);
    BOOST_CHECK_EQUAL(pos, 3);
    BOOST_CHECK_EQUAL(c, '*');
    pos = find_first_char(str, "/*", c);
    BOOST_CHECK_EQUAL(pos, 3);
    BOOST_CHECK_EQUAL(c, '*');
    pos = find_first_char(str, "+-", c);
    BOOST_CHECK_EQUAL(pos, 1);
    BOOST_CHECK_EQUAL(c, '+');
    pos = find_first_char(str, "-+", c);
    BOOST_CHECK_EQUAL(pos, 1);
    BOOST_CHECK_EQUAL(c, '+');
    pos = find_first_char(str, "72934", c);
    BOOST_CHECK_EQUAL(pos, string::npos);
}

// 5.1 测试用例夹具
struct valueinterpreter_fixture
{
    int ta;
    valueinterpreter_fixture() { ta = 3; }
    ~valueinterpreter_fixture() {}
}
// 有测试用例夹具时的测试用例声明，第一个参数是测试用例名字，第二个参数是测试用例夹具类名
BOOST_FIXTURE_TEST_CASE(test_ValueInterpreter, valueinterpreter_fixture)
// 没有测试用例夹具时的测试用例声明
BOOST_AUTO_TEST_CASE(test_ValueInterpreter)
{
    string str = "a";
    auto inter = Interpreter::analyse(str);
    BOOST_CHECK_CLOSE(inter->interpret(valueMap), 2, 0.0001);
    delete inter;
}

// 测试套件结束
BOOST_AUTO_TEST_SUITE_END()

// 此外还有针对模板Template的测试用例

// 6. 检查宏
BOOST_<level>                       // bool
BOOST_<level>_BITWISE_EQUAL
BOOST_<level>_EQUAL                 // 等于
BOOST_<level>_EQUAL_COLLECTIONS
BOOST_<level>_CLOSE                 // 浮点数等于
BOOST_<level>_CLOSE_FRACTION
BOOST_<level>_GE                    // 大于等于
BOOST_<level>_GT                    // 大于
BOOST_<level>_LE                    // 小于等于
BOOST_<level>_LT                    // 小于
BOOST_<level>_MESSAGE
BOOST_<level>_NE                    // 不等于
BOOST_<level>_PREDICATE
BOOST_<level>_NO_THROW
BOOST_<level>_THROW
BOOST_<level>_EXCEPTION
BOOST_<level>_SMALL


<level> 分为REQUIRE、CHECK、WARN
REQUIRE 测试失败立即停止测试用例，如果测试失败会影响剩下的测试，可用
CHECK   最常用的模式，测试失败测试用例依然继续
WARN    即使测试失败，测试用例的结果依然是成功，但是会写进日志
```
