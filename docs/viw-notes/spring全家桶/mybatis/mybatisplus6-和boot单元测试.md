[TOC]



# 和springboot测试 

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

