[TOC]



# springboot测试连接 

>生成和 controller  mapper  entity  service



```java

// 记得这两个注解
@RunWith(SpringRunner.class)
@SpringBootTest
public class DbTest {
	
	@Autowired
	private xxxMapper xxMapper;

	@Test
	public void test2() {
			xxxMapper.SelectOne(xxxx);
	}	
}


```



# 自定义IPages分页和wrapper



```java
## mapper
List<T> getAll(Page<T> page,@Param(Constants.WRAPPER) Wrapper wrapper )

## service

## mapper.xml

<select id="getAll" resultType="xxx">
	xxx FROM xxx ${ew.customSqlSegment}
</select>



```

