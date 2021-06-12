> # JAVA项目报错Implicit super constructor Object() is undefined for default constructor. Must define an explicit constructor
>
> **今天看了一下之前的JAVA项目，但是发现很多地方都报错，报的错误是Implicit super constructor Object() is undefined for default constructor. Must define an explicit constructor**
>
> **然后在网上查询**
>
>  
>
> **把java的类库加载进去，在工程上右键选择属性->Java Build Path的Libraries->Add Library选择JRE System Library->点击Next->选择Execution environment并选择版本或workspace default jre->点击Finish。**