## 实现栈的思路分析

1. 使用数组来模拟栈
2. 定义top表示为栈顶，初始为-1
3. 入栈的操作：top++;stack[top] = data;
4. 出栈操作：int value = stack[top];top--;return value;

- 使用栈完成计算表达式的结果(Calculator)

## 前缀表达式(波兰表达式)

- 运算符位于操作数之前
- 例: (3+4)*5-6 对应的前缀表达式：-*+3456 

- 前缀表达式的计算机求值  
  从右至左扫描表达式，遇到数字时，将数字压入栈；遇到运算符时，弹出栈顶的两个数，并用运算符将  
  它们做相应的运算，再将结果入栈。重复上述过程直到表达式最左端，最后运算得出的结果即为表达式的值。

## 中缀表达式

- 常见的运算表达式：(3+4)*5-6
- 中缀表达式是人最熟悉的，但是对计算机来说不好操作，计算机计算时一般先将其转成后缀表达式来操作

## 后缀表达式(逆波兰表达式)

- 与前缀表达式相似，只是运算符位于操作数之后
- 例: (3+4)*5-6 对应的后缀表达式：34+5*6-
- 后缀表达式的计算机求值
  从左至右扫描，遇到数字时，将数字压入栈；遇到运算符时，弹出栈顶的两个数，并用运算符将  
  它们做相应的运算，再将结果入栈。重复上述过程直到表达式最右端，最后运算得出的结果即为表达式的值。
- 应用：逆波兰计算器(PolandNotation)
- 中缀表达式转后缀表达式
  1. 初始化两个栈：运算符栈s1和存储中间结果的栈s2
  2. 从左至右扫描中缀表达式
  3. 遇到操作数时，压入s2
  4. 遇到运算符时，比较其与s1栈顶运算符的优先级
      - 如果s1为空或栈顶运算符为"("，则直接将次运算符入栈
      - 若优先级比栈顶运算符高，也直接入栈
      - 否则，将s1栈顶的运算符弹出并压入s2中，再次回到第四步比较
  5. 遇到括号时：
      - 如果是"("，则直接压入s1
      - 如果是")"，则依次弹出s1栈顶的运算符，并压入s2，直到遇到左括号为止，此时将这一对括号丢弃
  6. 重复步骤2-5，直到表达式的最右边
  7. 将s1中剩余运算符依次弹出，压入s2
  8. 依次弹出s2中的元素并输出，逆序结果即中缀表达式对应的后缀表达式