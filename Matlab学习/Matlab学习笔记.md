# Matlab学习笔记

- 笔记时间：2021.12.04

- 目的：为了学习/复习数学知识使用可视化

## 1. 基本类型与使用

- 命令行窗口可以直接使用脚本语言进行操作，比如简单计算、变量赋值等
- ...表示续行符，命令对象搜索顺序 变量→函数→文件
- 命令
  - path 设置文件搜索路径
  - class 查看数据类型
  - format 指定输出格式
  - sin 和 sind 差别在于后者使用角度
- 类型
  - 数值型-整型和浮点型，默认是double，
    - fix 靠近0的整数，rem表示取余，find查找下标
    - who / whos 可以查看变量，clear 清空变量，save/load 保存变量文件
    
  - 矩阵，使用中括号声明，分号换行
    - 冒号表达式 初始值：步长：终止值，生成一维向量；可以使用linspace代替
    - 结构矩阵 比如学生矩阵，用点设置成员值 ，类型属于结构体
    - 单元矩阵和普通矩阵一致，使用大括号表示
    - 访问使用小括号，支持自动扩充，按列存储，下标从1开始，可以使用冒号表达式
    - 赋值为空矩阵实现删除，reshape函数改变显示结构
    - 点乘 和 乘，前者是元素运算，后者是矩阵运算，除法分为左除和右除（区别在于哪个矩阵取逆）
    
  - 字符串
  
    - 单引号，当成一个向量，单引号可以充当转义符
      - length查看长度
      - eval 执行字符串
      - abs 和 char 在ASCII码和字符相互转换
      - strcmp 具有四个版本，比较字符串是否相等
  
    

## 2. 矩阵处理

- 特殊矩阵
  - zeros 零矩阵，ones 幺矩阵，eye单位矩阵，rand （0，1）范围随机矩阵，
  - magic魔方矩阵，vander 范德蒙矩阵，hilb 希尔伯特矩阵，compan伴随矩阵

- 矩阵处理
  - 矩阵变换
    - diag 提取主对角线产生列向量，对角阵（单位矩阵、对角矩阵、数量矩阵）
    - triu tril 上、下三角阵
    - .' 转置  '共轭转置
    - rot 旋转矩阵
    - fliplr 左右翻转，flipud 上下翻转
    - inv 求取逆矩阵





















