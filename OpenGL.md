# QOpenGL封装好的类

* QOpenGLBuffer

* QOpenGLVertexArrayObject

* QOpenGLShader

* QOpenGLShaderProgram

* QOpenGLContext

* QOpenGLDebugLogger

* QOpenGLDebugMessage

* QOpenGLFramebufferObject

* QOpenGLFramebufferObjectFormat

* QOpenGLPixelTransferOptions

* QOpenGLTextureBlitter

* QOpenGLTimeMonitor

* QOpenGLTimerQuery

* QOpenGLVersionProfile

* QOpenGLFunctions

* QOpenGLPaintDevice

* QOpenGLWidget

* QOpenGLWindow

# 简介

* GLFW 
  
  http://www.glfw.org/

* GL3W

* vertex 顶点

* render 渲染

* fragment 像素片元

* fragment shader 着色器片元

* shader 着色器

* rasterization 光栅化

# 坐标系

## 标准化设备坐标

每个顶点的**x**，**y**，**z**坐标都应该在**\-1.0**到**1.0**之间，超出这个坐标范围的顶点都将不可见。我们通常会自己设定一个坐标的范围，之后再在顶点着色器中将这些坐标变换为标准化设备坐标。

## 坐标系统

物体、世界、相机空间是右手坐标系，即右手拇指x+，食指y+，中指z+

### 对我们来说比较重要的总共有5个不同的坐标系统：

#### 局部空间(Local Space，或者称为物体空间(Object Space))

通常是模型物体的坐标系，原点都是(0, 0, 0)

#### 世界空间(World Space)

所有的模型转换到世界坐标需要**模型矩阵**，包括位移、缩放和旋转，即代表物体在世界空间中的位置、大小和旋转。

#### 观察空间(View Space，或者称为视觉空间(Eye Space))

观察空间经常被人们称之OpenGL的摄像机(Camera)（所以有时也称为摄像机空间(Camera Space)或视觉空间(Eye Space)）。

这通常是由一系列的位移和旋转的组合来完成，平移/旋转场景从而使得特定的对象被变换到摄像机的前方。这些组合在一起的变换通常存储在一个**观察矩阵**(View Matrix)里，它被用来将世界坐标变换到观察空间

#### 裁剪空间(Clip Space)

因为将所有可见的坐标都指定在-1.0到1.0的范围内不是很直观，所以我们会指定自己的坐标集(Coordinate Set)并将它变换回标准化设备坐标系，就像OpenGL期望的那样。

为了将顶点坐标从观察变换到裁剪空间，我们需要定义一个**投影矩阵**(Projection Matrix)，它指定了一个范围的坐标，比如在每个维度上的-1000到1000。投影矩阵接着会将在这个指定的范围内的坐标变换为标准化设备坐标的范围(-1.0, 1.0)。所有在范围外的坐标不会被映射到在-1.0到1.0的范围之间，所以会被裁剪掉。

由投影矩阵创建的**观察箱**(Viewing Box)被称为平截头体(Frustum)。

将观察坐标变换为裁剪坐标的投影矩阵可以为两种不同的形式，每种形式都定义了不同的平截头体。我们可以选择创建一个**正射投影矩阵****(Orthographic Projection Matrix)或一个**透视投影矩阵**(Perspective Projection Matrix)。

* **正射投影矩阵**
  
  它由宽、高、近(Near)平面和远(Far)平面所指定;任何出现在近平面之前或远平面之后的坐标都会被裁剪掉。
  
  glm::ortho(0.0f, 800.0f, 0.0f, 600.0f, 0.1f, 100.0f);
  
  前两个参数指定了平截头体的左右坐标，第三和第四参数指定了平截头体的底部和顶部。通过这四个参数我们定义了近平面和远平面的大小，然后第五和第六个参数则定义了近平面和远平面的距离。

* **透视投影矩阵**
  
  glm::mat4 proj = glm::perspective(glm::radians(45.0f), (float)width/(float)height, 0.1f, 100.0f);
  
  * 它的第一个参数定义了fov的值，它表示的是视野(Field of View)，并且设置了观察空间的大小。如果想要一个真实的观察效果，它的值通常设置为45.0f。
  
  * 第二个参数设置了宽高比，由视口的宽除以高所得。
  
  * 第三和第四个参数设置了平截头体的**近**和**远**平面。我们通常设置近距离为0.1f，而远距离设为100.0f。
  
  所有在近平面和远平面内且处于平截头体内的顶点都会被渲染。

一个顶点坐标将会根据以下过程被变换到裁剪坐标：

$V_{clip}=M_{projection} \cdot M_{view} \cdot M_{model} \cdot V_{local}$

#### 屏幕空间(Screen Space)

# 第1章 OpenGL概述

1. 主要操作
   
   1. 从OpenGL的几何图元中设置数据，用于构建形状。
   
   2. 使用不同的着色器（shader）对输入的图元数据执行计算操作，判断它们的位置、颜色，以及其他的渲染属性。
   
   3. 将输入图元的数学描述转换为与屏幕位置对应的像素片元（fragment）。这一步也称作光栅化（rasterization）。（OpenGL中的片元若最终渲染为图像，那它就是像素。）
   
   4. 最后，针对光栅化过程产生的每个片元，执行片元着色器（fragment shader），从而决定这个片元的最终颜色和位置。
   
   5. 如果有必要，还需要对片元进行一些额外的操作，如判断片元对应的对象是否可见，将片元的颜色和当前屏幕位置的颜色进行融合。

2. OpenGL是使用客户端-服务端的架构，OpenGL命令会转换为相关协议，传输到服务端进行执行。

3. * OpenGL是基于光栅化的系统
   
   * 模型 model
   
   * 着色器 shader 专为图形处理单元（GPU）编译的一种小型程序
     
     OpenGL包含6个不同的着色阶段（shager stage）。其中最常用也是**必须**的是顶点着色器（vertex shader）和片元着色器，前者用于处理顶点数据，后者用于处理光栅化后的片元数据。

## OpenGL渲染管线

1. 准备向OpenGL传输数据

2. 将数据传输到OpenGL
   
   常用glDrawArray()

3. 顶点着色

4. 细分着色
   
   细分几何形状

5. 几何着色

6. 图元装配
   
   将顶点和相关的几何图元组织起来

7. 剪切 clipping
   
   裁剪到视口（viewport）之外的顶点，这是由OpenGL自动完成的。

8. 光栅化
   
   由光栅化单元生成对应的片元（候选像素，最终可能被剔除）

9. 片元着色
   
   计算片元的最终颜色（下一个阶段还可能会改变）和深度值。

10. 逐片元操作
    
    使用深度测试（depth test、z缓存）和模板测试（stencil test）的方式决定一个片元是否可见。

## 说明

* glCreate*
  
  用于分配对象，返回分配对象的名字（大于0的一个整数）用于后续使用

* glBind*
  
  将对象绑定到当前环境，即后续的操作均作用于该对象
  
  如果传入的参数为0，OpenGL将不再使用之前绑定的对象

* glDelete*
  
  释放对象

* glIs*
  
  检查对象是否可用

# 第2章 着色器基础

# 第3章 OpenGL绘制方式

# 第4章 颜色、像素和片元

# 第5章 视口变换、裁剪、剪切与反馈

# 第6章 纹理与帧缓存

## 纹理贴图 Texture Map

* Diffusion Map 漫反射贴图

* Normal Map 法线贴图

* Specular Map 高光贴图

* Matcap Materia Capture 材质捕捉

* Ambient Occlusion 环境光贴图

* Light Map 光照贴图

* Cube Map 环境贴图，天空盒

* Mip Map
  
  用于纹理过滤

* Detail Map 细节贴图

* Parallax Mapping 视差贴图

* Noise Map 噪声贴图

* 置换贴图

* 凹凸贴图 
  
  可以认为是高度图

### 纹理尺寸要求

纹理的长宽必须为2的次幂，如2、4、8、16、32等。

## obj的材质mtl

```mtl
newmtl my_material                  定义了一个名为"my_material"的新材质
Ka 1.0 1.0 1.0                      指定了材质的环境光颜色
Kd 0.8 0.8 0.8                      指定了材质的漫反射颜色。
Ks 0.2 0.2 0.2                      指定了材质的镜面反射颜色
Ns 10.0                             指定了材质的镜面反射指数
d 1.0                               指定了材质的透明度，0-1，1表示完全不透明
illum 2                             指定了要使用的光照模型
map_Ka texture_ambient.jpg          指定了环境光纹理映射
map_Kd texture_diffuse.jpg          指定了漫反射纹理映射
map_Ks texture_specular.jpg         指定了镜面反射纹理映射
map_Ns texture_specular_highlight.jpg    指定了镜面反射高光纹理映射
map_d texture_opacity.jpg           指定了透明度纹理映射
disp texture_displacement.jpg       指定了位移纹理映射
decal texture_decal.jpg             指定了贴花纹理映射
bump texture_bump.jpg               指定了凹凸纹理映射
```

* illum取值
  
  1. **`illum 0`**：颜色只取决于 `Ka`（环境光颜色），不进行光照计算，没有高光效果。常用于模拟自发光物体或者一些不需要光照影响颜色的物体，像纯颜色的平面图形、简单的2D纹理等。
  2. **`illum 1`**：颜色由 `Ka` 和 `Kd`（漫反射颜色）决定，没有高光效果。适用于表面粗糙的物体，如砖块、未经打磨的石头等，这些物体表面不会产生明显的高光反射。
  3. **`illum 2`**：颜色由 `Ka`、`Kd` 和 `Ks`（镜面反射颜色）决定，有高光效果。这是最常用的光照模型，用于模拟有光泽的物体，如塑料、金属等。
  4. **`illum 3`**：与 `illum 2` 类似，但使用了光线追踪反射，可模拟更真实的反射效果。常用于需要精确反射效果的场景，如镜子、高反光的金属表面等。
  5. **`illum 4`**：有漫反射和镜面反射，并且使用了菲涅尔反射。适用于模拟玻璃、水面等具有菲涅尔效应的物体。
  6. **`illum 5`**：漫反射加光线追踪反射，有菲涅尔效应。用于模拟一些既需要漫反射又需要精确反射和菲涅尔效果的物体，如半透明的宝石等。
  7. **`illum 6`**：与 `illum 5` 类似，但不使用漫反射颜色。适用于一些特殊的材质效果，如某些科幻风格的发光物体。
  8. **`illum 7`**：仅使用镜面反射和光线追踪反射，没有漫反射。用于模拟完全反射的物体，如理想的镜子。
  9. **`illum 8`**：漫反射加折射，有菲涅尔效应。常用于模拟透明物体，如水、玻璃等。
  10. **`illum 9`**：与 `illum 8` 类似，但使用光线追踪折射。适用于需要精确折射效果的场景，如光学透镜等。
  11. **`illum 10`**：漫反射加折射，没有菲涅尔效应。可用于模拟一些简单的透明物体，其折射效果不考虑菲涅尔效应。
  12. **`illum 11`**：仅使用折射，有菲涅尔效应。用于模拟完全透明且有菲涅尔效果的物体，如清澈的水晶。
  13. **`illum 12`**：仅使用折射，没有菲涅尔效应。适用于简单的透明物体模拟，不考虑菲涅尔效应的影响。
  14. **`illum 13`**：自发光，没有反射和折射。常用于模拟发光的物体，如灯泡、霓虹灯等。
  15. **`illum 14`**：自发光加反射，没有折射。可用于模拟一些既发光又有反射效果的物体，如发光的金属雕塑。
  16. **`illum 15`**：自发光加反射和折射。适用于模拟复杂的发光、反射和折射效果的物体，如科幻场景中的能量球等。

# 第7章 光照与阴影

* 光照颜色
  
  color = lightColor * objectColor

## 冯氏光照模型

冯氏光照模型包含以下三种光照分量

* 环境光
  
  float ambientStrenth = 0.1
  
  vec3 ambient = ambientsStrength * lightColor
  
  vec3 ambientColor = ambient * objectColor

* 漫反射
  
  vec3 lightDir = normalize(lightPos - fragPos)
  
  float diff = max(dot(normal, lightDir), 0.0); // 散射因子
  
  vec3 diffuseColor = diff * objectColor

* 镜面光照
  
  float specularStrength = 0.5f;
  
  vec3 viewDir = normalize(viewPos - FragPos); 
  
  vec3 reflectDir = reflect(-lightDir, norm);
  
  float spec = pow(max(dot(viewDir, reflectDir), 0.0), 32); // 32是反射强度
  
  vec3 specularColor = specularStrength * spec * lightColor * objectColor;

## 材质

将冯氏光照模型中三种光照中的objectColor换成相对应的颜色分量，就可以定义得到材质的定义。

```glsl
struct Material {
    vec3 ambient;    // 环境光颜色
    vec3 diffuse;    // 漫反射颜色
    vec3 specular;   // 镜面反射颜色
    float shininess; // 反光度，影响高光的半径
}; 


void main()
{    
    // 环境光
    vec3 ambient = lightColor * material.ambient;

    // 漫反射 
    vec3 norm = normalize(Normal);
    vec3 lightDir = normalize(lightPos - FragPos);
    float diff = max(dot(norm, lightDir), 0.0);
    vec3 diffuse = lightColor * (diff * material.diffuse);

    // 镜面光
    vec3 viewDir = normalize(viewPos - FragPos);
    vec3 reflectDir = reflect(-lightDir, norm);  
    float spec = pow(max(dot(viewDir, reflectDir), 0.0), material.shininess);
    vec3 specular = lightColor * (spec * material.specular);  

    vec3 result = ambient + diffuse + specular;
    FragColor = vec4(result, 1.0);
}
```

## 漫反射贴图和镜面光贴图

冯氏光照模型中的三种光颜色除了是材质中定义的固定值外，还可以用贴图定义每个片段的颜色。

通常环境颜色和漫反射颜色是一样的，所以一般环境光贴图和漫反射贴图是一样的。

使用方法如下：

```glsl
vec3 ambient  = light.ambient  * vec3(texture(material.diffuse, TexCoords));
vec3 diffuse  = light.diffuse  * diff * vec3(texture(material.diffuse, TexCoords));  
vec3 specular = light.specular * spec * vec3(texture(material.specular, TexCoords));
FragColor = vec4(ambient + diffuse + specular, 1.0);
```

## 光源

* 平行光

* 点光源

* 光强衰减
  
  $ F_{att}=\frac{1.0}{K_c+K_l*d+K_q*d^2} $
  
  它是来自Ogre3D的维基的礼物：
  
  | 距离   | 常数项 | 一次项    | 二次项      |
  | ---- | --- | ------ | -------- |
  | 7    | 1.0 | 0.7    | 1.8      |
  | 13   | 1.0 | 0.35   | 0.44     |
  | 20   | 1.0 | 0.22   | 0.20     |
  | 32   | 1.0 | 0.14   | 0.07     |
  | 50   | 1.0 | 0.09   | 0.032    |
  | 65   | 1.0 | 0.07   | 0.017    |
  | 100  | 1.0 | 0.045  | 0.0075   |
  | 160  | 1.0 | 0.027  | 0.0028   |
  | 200  | 1.0 | 0.022  | 0.0019   |
  | 325  | 1.0 | 0.014  | 0.0007   |
  | 600  | 1.0 | 0.007  | 0.0002   |
  | 3250 | 1.0 | 0.0014 | 0.000007 |

* 聚光 手电筒

## 深度测试

### 深度冲突

# 第8章 程序式纹理

# 第9章 细分着色器

# 第10章 几何着色器

# 第11章 内存

# 第12章 计算着色器
