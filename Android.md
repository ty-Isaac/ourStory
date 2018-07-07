---
Title: Base of Android
Date: 2017-11-05 09:23:48
Categories: 编程
Tags:  
---



# Android

## Adapter

### MVC 

- Model : 通常可以理解为数据，负责执行程序的核心运算与判断逻辑。通过view获得用户输入的数据，然后根据从数据库查询相关的信息，最后进行运算和判断，再将得到的结果交给view来显示。
- View : 用户的操作接口，说白了就是GUI，应该使用哪种接口组件,组件间的排列位置与顺序都需要设计
- Controller : 控制器，作为 model 与 view 之间的枢纽，负责控制程序的执行流程以及对象之间的互动











| UI名称          | 类名/方法名                | 是否实现 |
| ------------- | --------------------- | ---- |
| ListView      |                       | 是    |
| ActionBar     |                       |      |
| Menu          | onCreateOptionsMenu() | 是    |
| ViewPager     |                       |      |
| Gallery       |                       |      |
| GridView      | GridView_init()       | 是    |
| ImageView     |                       | 是    |
| ProgressBar   |                       |      |
| TextView      |                       | 是    |
| ScrollView    |                       |      |
| TimeView      |                       |      |
| TipView       |                       |      |
| FlipView      | flipperPage_init()    | 是    |
| ColorPickView |                       |      |
| GraphView     |                       |      |
|               |                       |      |
|               |                       |      |

