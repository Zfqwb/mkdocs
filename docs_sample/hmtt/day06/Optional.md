从 Java 8 引入的一个很有趣的特性是 *Optional*  类。Optional 类主要解决的问题是臭名昭著的空指针异常（NullPointerException） —— 每个 Java 程序员都非常了解的异常。

本质上，这是一个包含有可选值的包装类，这意味着 Optional 类既可以含有对象也可以为空。

Optional 是 Java 实现函数式编程的强劲一步，并且帮助在范式中实现。但是 Optional 的意义显然不止于此。

我们从一个简单的用例开始。在 Java 8 之前，任何访问对象方法或属性的调用都可能导致 *NullPointerException*：

```java
String isocode = user.getAddress().getCountry().getIsocode().toUpperCase();
```

在这个小示例中，如果我们需要确保不触发异常，就得在访问每一个值之前对其进行明确地检查：

```java
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        Country country = address.getCountry();
        if (country != null) {
            String isocode = country.getIsocode();
            if (isocode != null) {
                isocode = isocode.toUpperCase();
            }
        }
    }
}
```

你看到了，这很容易就变得冗长，难以维护。

为了简化这个过程，我们来看看用 *Optional* 类是怎么做的。从创建和验证实例，到使用其不同的方法，并与其它返回相同类型的方法相结合，下面是见证 *Optional*  奇迹的时刻。

## 

## **创建 *Optional* 实例** 

重申一下，这个类型的对象可能包含值，也可能为空。你可以使用同名方法创建一个空的 Optional。

```java
@Test(expected = NoSuchElementException.class)
public void whenCreateEmptyOptional_thenNull() {
    Optional<User> emptyOpt = Optional.empty();
    emptyOpt.get();
}
```

毫不奇怪，尝试访问 *emptyOpt* 变量的值会导致 *NoSuchElementException*。

你可以使用 *of()* 和 ofNullable() 方法创建包含值的 *Optional*。两个方法的不同之处在于如果你把 *null* 值作为参数传递进去，*of()* 方法会抛出 *NullPointerException*：

```java
@Test(expected = NullPointerException.class)
public void whenCreateOfEmptyOptional_thenNullPointerException() {
    Optional<User> opt = Optional.of(user);
}
```

你看，我们并没有完全摆脱 *NullPointerException*。因此，你应该明确对象不为 *null* 的时候使用 *of()*。

如果对象即可能是 *null* 也可能是非 null，你就应该使用 *ofNullable()* 方法：

```java
Optional<User> opt = Optional.ofNullable(user);
```

**访问 \*Optional\* 对象的值**

从 *Optional* 实例中取回实际值对象的方法之一是使用 *get()* 方法：

```java
@Test
public void whenCreateOfNullableOptional_thenOk() {
    String name = "John";
    Optional<String> opt = Optional.ofNullable(name);

    assertEquals("John", opt.get());
}
```

不过，你看到了，这个方法会在值为 *null* 的时候抛出异常。要避免异常，你可以选择首先验证是否有值：

```java
@Test
public void whenCheckIfPresent_thenOk() {
    User user = new User("john@gmail.com", "1234");
    Optional<User> opt = Optional.ofNullable(user);
    assertTrue(opt.isPresent());

    assertEquals(user.getEmail(), opt.get().getEmail());
}
```

检查是否有值的另一个选择是 *ifPresent()* 方法。该方法除了执行检查，还接受一个*Consumer(消费者*) 参数，如果对象不是空的，就对执行传入的 Lambda 表达式：

```java
opt.ifPresent( u -> assertEquals(user.getEmail(), u.getEmail()));
```

这个例子中，只有 user 用户不为 null 的时候才会执行断言。

接下来，我们来看看提供空值的方法。

### 

### **返回默认值** 

*Optional* 类提供了 API 用以返回对象值，或者在对象为空的时候返回默认值。

这里你可以使用的第一个方法是 *orElse()*，它的工作方式非常直接，如果有值则返回该值，否则返回传递给它的参数值：

```java
@Test
public void whenEmptyValue_thenReturnDefault() {
    User user = null;
    User user2 = new User("anna@gmail.com", "1234");
    User result = Optional.ofNullable(user).orElse(user2);

    assertEquals(user2.getEmail(), result.getEmail());
}
```

这里 *user* 对象是空的，所以返回了作为默认值的 *user2*。

如果对象的初始值不是 null，那么默认值会被忽略：

```java
@Test
public void whenValueNotNull_thenIgnoreDefault() {
    User user = new User("john@gmail.com","1234");
    User user2 = new User("anna@gmail.com", "1234");
    User result = Optional.ofNullable(user).orElse(user2);

    assertEquals("john@gmail.com", result.getEmail());
}
```

第二个同类型的 API 是 *orElseGet()* —— 其行为略有不同。这个方法会在有值的时候返回值，如果没有值，它会执行作为参数传入的 *Supplier(供应者)* 函数式接口，并将返回其执行结果：

```
User result = Optional.ofNullable(user).orElseGet( () -> user2);
```

***orElse()\* 和 \*orElseGet()\* 的不同之处**

乍一看，这两种方法似乎起着同样的作用。然而事实并非如此。我们创建一些示例来突出二者行为上的异同。

我们先来看看对象为空时他们的行为：

```java
@Test
public void givenEmptyValue_whenCompare_thenOk() {
    User user = null
    logger.debug("Using orElse");
    User result = Optional.ofNullable(user).orElse(createNewUser());
    logger.debug("Using orElseGet");
    User result2 = Optional.ofNullable(user).orElseGet(() -> createNewUser());
}

private User createNewUser() {
    logger.debug("Creating New User");
    return new User("extra@gmail.com", "1234");
}
```

上面的代码中，两种方法都调用了 *createNewUser()* 方法，这个方法会记录一个消息并返回 *User* 对象。

代码输出如下：

```
Using orElse
Creating New User
Using orElseGet
Creating New User
```

由此可见，当对象为空而返回默认对象时，行为并无差异。

我们接下来看一个类似的示例，但这里 *Optional*  不为空：

```java
@Test
public void givenPresentValue_whenCompare_thenOk() {
    User user = new User("john@gmail.com", "1234");
    logger.info("Using orElse");
    User result = Optional.ofNullable(user).orElse(createNewUser());
    logger.info("Using orElseGet");
    User result2 = Optional.ofNullable(user).orElseGet(() -> createNewUser());
}
```

这次的输出：

```
Using orElse
Creating New User
Using orElseGet
```

这个示例中，两个 *Optional*  对象都包含非空值，两个方法都会返回对应的非空值。不过，*orElse()* 方法仍然创建了 *User* 对象。**与之相反，\*orElseGet()\* 方法不创建 \**\*User\**\*** **对象。**

在执行较密集的调用时，比如调用 Web 服务或数据查询，**这个差异会对性能产生重大影响**。

>- `orElse(T other)` 不论容器是否为空,只要调用该方法, 则对象`other`一定存在
>- `orElseGet(Supplier<? extends T> supplier)` 只有当容器为空时,才调用`supplier.get()`方法产生对象

### **返回异常** 

除了 *orElse()* 和 *orElseGet()* 方法，Optional 还定义了 *orElseThrow()* API —— 它会在对象为空的时候抛出异常，而不是返回备选的值：

```
@Test(expected = IllegalArgumentException.class)
public void whenThrowException_thenOk() {
    User result = Optional.ofNullable(user)
      .orElseThrow( () -> new IllegalArgumentException());
}
```

这里，如果 *user* 值为 null，会抛出 *IllegalArgumentException*。

这个方法让我们有更丰富的语义，可以决定抛出什么样的异常，而不总是抛出 *NullPointerException*。

现在我们已经很好地理解了如何使用 Optional，我们来看看其它可以对 *Optional* 值进行转换和过滤的方法。

## *Optional* 类的链式方法 

为了更充分的使用 *Optional*，你可以链接组合其大部分方法，因为它们都返回相同类似的对象。

我们使用 *Optional* 重写最早介绍的示例。

首先，重构类，使其 getter 方法返回 *Optional* 引用：

```java
public class User {
    private Address address;

    public Optional<Address> getAddress() {
        return Optional.ofNullable(address);
    }

    // ...
}
public class Address {
    private Country country;

    public Optional<Country> getCountry() {
        return Optional.ofNullable(country);
    }

    // ...
}
```

上面的嵌套结构可以用下面的图来表示：

[![optional nested](https://images2017.cnblogs.com/blog/782702/201709/782702-20170923094507806-345950041.png)](http://inprogress.baeldung.com/wp-content/uploads/2017/08/user.png)

现在可以删除 *null* 检查，替换为 *Optional* 的方法：

```
@Test
public void whenChaining_thenOk() {
    User user = new User("anna@gmail.com", "1234");

    String result = Optional.ofNullable(user)
      .flatMap(u -> u.getAddress())
      .flatMap(a -> a.getCountry())
      .map(c -> c.getIsocode())
      .orElse("default");

    assertEquals(result, "default");
}
```

上面的代码可以通过方法引用进一步缩减：

```
String result = Optional.ofNullable(user)
  .flatMap(User::getAddress)
  .flatMap(Address::getCountry)
  .map(Country::getIsocode)
  .orElse("default");
```

  结果现在的代码看起来比之前采用条件分支的冗长代码简洁多了