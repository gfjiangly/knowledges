```
标题
一级标题： = 
==============

二级标题： - 
--------------

三级标题： + 
++++++++++++++

四级标题： ^
^^^^^^^^^^^^^^

长度要比文字长


图片
例子
.. image:: images/Table_3-1_python_set_operations.png
	:width: 800

	
代码
.. code-block:: python
	:linenos:	# 显示行号
	:emphasize-lines: 2	# 配置高亮的行
# 空一行，然后代码要缩进
# 上面python可以修改为其它语言

如果是python代码，可以使用::直接插入代码，默认就是Python语言


行内标记
*text* 强调 (斜体)
**text** 重点强调 (粗体)
``text`` 表示引用示例

如果星号或反引号出​​现在文本会对行内标记分隔符引起混淆，他们必须用一个反斜杠进行转义。


标注
.. note::


外部链接
内嵌网页链接
`Link text <http://example.com/>`_ 
如果链接文本是Web地址，不需要特殊标记，解析器可以识别在普通的文本的链接和邮件地址。


公式
.. math::
    \int_a^{b}f=\left[f(a+\dfrac{dx}{2})+f(a+dx+\dfrac{dx}{2})+(f(a+2dx+\dfrac{dx}{2})+\cdots \right]dx


```

