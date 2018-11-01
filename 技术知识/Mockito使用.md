# **引入mockito jar包**

代码块

```
<dependency>
   <groupId>org.mockito</groupId>
   <artifactId>mockito-all</artifactId>
   <version>2.0.2-beta</version>
</dependency>
```

## **对于Mockito而言，有两种方式创建：**

> 1.  mock为一个interface提供一个虚拟的实现，
>
> 2.  spy为object加一个动态代理，实现部分方法的虚拟化
>

* @Mock，被标注的属性是个mock

* @Spy，被标注的属性是个spy，需要赋予一个instance。提供了一种对**真实对象**操作的方法

* @InjectMocks，将本test中的mock或者spy注入到被标注的属性中，根据构造函数的参数名，或者setter，或者私有属性名。

# 例子

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext-test.xml")
public class AccountServiceTest {

    @InjectMocks
    @Autowired
    AccountService accountService;

    @Mock
    private TAccountService.Iface tAccountService;
    @Before
    public void setup() {
        /** 如果同时需要注入和mock注入，SpringJUnit4ClassRunner的前提下，注入mock */

        MockitoAnnotations.initMocks(this);

    }

    @Test
    public void testGetCustomerByAccountId() throws TException {

        TCustomer tCustomer0 = new TCustomer();

        tCustomer0.setId(222);

        int accountId = 22;

        Mockito.when(tAccountService.getTCustomer(accountId)).thenReturn(tCustomer0);
        TCustomer tCustomer = accountService.getCustomerByAccountId(22);
        System.out.println(tCustomer);

    }

}
```
# 参考资料

https://blog.csdn.net/z199172177/article/details/79731952  
https://blog.csdn.net/paincupid/article/details/53561435