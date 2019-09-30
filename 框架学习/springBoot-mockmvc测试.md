SpringMVC进行测试有很多种方法,常见的例如postman、或者是IDEA自带的HTTP测试工具均可以完成，而springMVC也可以使用Mock进行测试

> 以下均基于springBoot环境



```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ControllerTest {
    private MockMvc mockMvc ;
    //自动注入上下文对象
    @Autowired
    private WebApplicationContext context;
    @Before
    public void setup(){
        //实例化一个MockMvc对象，进行发送request操作
        //这里需要注意的是,极力不推荐使用MockMvcBuilders.standaloneSetup()注入Controller进行测试,会出一些乱七八糟的BUG
        //指定WebApplicationContext，将会从该上下文获取相应的控制器并得到相应的MockMvc
        mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
    }

    @Test
    public void testUserController() throws Exception{
        //如果想要批量设置参数,可以使用LinkedMultiValueMap放置参数,然后使用params()方法设置参数
//        MultiValueMap<String,String> stringStringMultiValueMap = new LinkedMultiValueMap<String, String>();
//        stringStringMultiValueMap.add("departmentName","testformock");
        RequestBuilder requestBuilder = null;

        //MockMvcRequestBuilders.get("XXX")构造一个请求。类似的还有其余几个RESTFUL的方法 例如put post 注意都是小写
        requestBuilder = MockMvcRequestBuilders.get("/dept")
                .param("departmentName","2333")
        ;
        //mockMvc.perform执行一个请求,返回一个ResultActions
        //ResultActions.andExpect添加执行完成后的断言。
        //ResultActions.andDo添加一个结果处理器，表示要对结果做点什么事情
        //ResultActions.accept(MediaType.TEXT_HTML_VALUE))设置返回类型
        //ResultActions.andReturn表示执行完成后返回相应的结果
        MvcResult mvcResult = mockMvc.perform(requestBuilder)
                .andExpect(status().isOk())
                .andDo(print()).andReturn();
        int status=mvcResult.getResponse().getStatus();                 //得到返回代码
        String content=mvcResult.getResponse().getContentAsString();    //得到返回结果
        Assert.assertEquals(200,status);                        //断言，判断返回代码是否正确

    }
}

```

其余的 基本上大同小异，可以参考文档进行学习