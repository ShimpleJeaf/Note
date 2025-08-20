# 命令行

* 直接在Doxyfile目录下直接运行doxygen

* 如果Doxyfile文件版本过低，可以用doxygen -u进行升级

# 注释语法

* 块注释
  
  ```cpp
  /**
   * 
   */
  ```

* 行注释，代码前注释
  
  ```cpp
  ///
  或
  /** */
  ```

* 批注前面的代码
  
  ```cpp
  /*!< ... */
  /**< ... */ （推荐）
  //!< ...
  ///< ...    （推荐）
  ```

## 文件注释

```cpp
/**
 * @file 文件名
 * @brief 简介
 * @details 细节
 * @mainpage 工程概述
 * @author 作者
 * @email 邮箱
 * @version 版本号
 * @date 日期
 * @license 版权
 */
```

## 函数注释

```cpp
/**
 * @brief 简介
 * @detail 详细说明
 * @param 形参
 * @param 形参
 * @return 返回说明
 *   @retval 0 程序执行成功 返回值说明
 *   @retval 1 程序执行失败 返回值说明
 * @note 注解
 * @attention 注意
 * @warning 警告
 * @exception 异常
 */
```

## 其他命令

| 命令          | 生成字段名  |                      |
| ----------- | ------ | -------------------- |
| @see        | 参考     |                      |
| @class      | 引用类    | 用于文档生成连接             |
| @var        | 引用变量   | 用于文档生成连接             |
| @enum       | 引用枚举   | 用于文档生成连接             |
| @code       | 代码块开始  | 与@endcode成对使用        |
| @endcode    | 代码块结束  | 与@code成对使用           |
| @bug        | 缺陷     | 链接到所有缺陷汇总的缺陷列表       |
| @todo       | TODO   | 链接到所有TODO 汇总的TODO 列表 |
| @example    | 使用例子说明 |                      |
| @remarks    | 备注说明   |                      |
| @pre        | 函数前置条件 |                      |
| @deprecated | 函数过时说明 |                      |
