# 前置声明、enum、enum class

* 内部类不允许前置声明

* enum不允许前置声明，可以通过类限定名直接访问
  
  enum可隐式转换成int，但不能从int赋值，且可以可int类型比较

* enum class可以前置声明，但是必须通过枚举类型限定访问
  
  enum class 和int不能隐式转换，也不能互相比较
  
  ```cpp
  enum class EnumA
  {
      Type1,
      Type2
  };
  void setType(EnumA type)
  {
      if (type == EnumA::Type1) // 必须有限定名EnumA
      {
      }
  }
  ```
