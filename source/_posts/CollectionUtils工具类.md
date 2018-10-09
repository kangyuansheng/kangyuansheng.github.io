---
title: CollectionUtils工具类
date: 2018-10-08 10:24:24
tags: [Java工具类]
categories: [Java工具类]
---

### CollectionUtils工具类介绍:
CollectionUtils工具类是org.apache.commons.collections4包下的工具类.提供了我们常用的集合操作,可以大大方便我们的日常编程.
<!--more-->

### CollectionUtils工具类常用方法介绍:
```java
/**
 * public static <O>  Collection <O>  union（Iterable <？extends O> a，Iterable <？extends O> b）
 * 返回Collection包含给定Iterables 的并集的 a 。
 * 返回的每个元素的基数Collection将等于两个给定Iterables 中该元素的基数的最大值。
 * 类型参数：O - 能够表示两个输入集合中包含的类型的泛型类型。
 *  参数：
 * a - 第一个集合，不能为空
 * b - 第二个集合，不得为null
 * 返回：两个集合的联合
 */
@Test
public void testUnion(){
    String[] arrayA = new String[] { "A", "B", "C", "D", "E", "F" };  
    String[] arrayB = new String[] { "B", "D", "F", "G", "H", "K" };
    List<String> listA = Arrays.asList(arrayA);
    List<String> listB = Arrays.asList(arrayB);
    //2个数组取并集 
    System.out.println(ArrayUtils.toString(CollectionUtils.union(listA, listB)));
    //[A, B, C, D, E, F, G, H, K]
}

/**
 * public static <O>  Collection <O>  intersection（Iterable <？extends O> a，Iterable <？extends O> b）
 * 返回Collection包含给定Iterables 的交集的 a 。
 * 返回的每个元素的基数Collection将等于两个给定Iterables 中该元素的基数的最小值。
 * 类型参数：O - 能够表示两个输入集合中包含的类型的泛型类型。
 *  参数：
 * a - 第一个集合，不能为空
 * b - 第二个集合，不得为null
 * 返回：两个集合的交集
 */
@Test
public void testIntersection(){
    String[] arrayA = new String[] { "A", "B", "C", "D", "E", "F" };  
    String[] arrayB = new String[] { "B", "D", "F", "G", "H", "K" };
    List<String> listA = Arrays.asList(arrayA);
    List<String> listB = Arrays.asList(arrayB);
    //2个数组取交集 
    System.out.println(ArrayUtils.toString(CollectionUtils.intersection(listA, listB)));
    //[B, D, F]

}

/**
 * public static <O>  Collection <O>  disjunction（Iterable <？extends O> a，Iterable <？extends O> b）
 * 返回Collection包含给定Iterables 的唯一析取（对称差异）的a。
 * 类型参数：O - 能够表示两个输入集合中包含的类型的泛型类型。
 *  参数：
 * a - 第一个集合，不能为空
 * b - 第二个集合，不得为null
 * 返回：两个集合的对称差异
 */
@Test
public void testDisjunction(){
    String[] arrayA = new String[] { "A", "B", "C", "D", "E", "F" };  
    String[] arrayB = new String[] { "B", "D", "F", "G", "H", "K" };
    List<String> listA = Arrays.asList(arrayA);
    List<String> listB = Arrays.asList(arrayB);
    //2个数组取交集 的补集
    System.out.println(ArrayUtils.toString(CollectionUtils.disjunction(listA, listB)));
    //[A, C, E, G, H, K]
}

/**
 * public static <O>  Collection <O>  disjunction（Iterable <？extends O> a，Iterable <？extends O> b）
 * 返回一个新的Collection包含<i>a</i> - <i>b</i>。每个元件的基数Ë在返回Collection 将是基数Ë在一个负的基数ë在b，或零，取较大值。
 * 类型参数：O - 能够表示两个输入集合中包含的类型的泛型类型。
 *  参数：
 * a - 要从中减去的集合，不得为null
 * b - 要减去的集合，不能为空
 * 返回：一个带有结果的新集合
 */
@Test
public void testSubtract(){
    String[] arrayA = new String[] { "A", "B", "C", "D", "E", "F" };  
    String[] arrayB = new String[] { "B", "D", "F", "G", "H", "K" };
    List<String> listA = Arrays.asList(arrayA);
    List<String> listB = Arrays.asList(arrayB);
    //arrayA扣除arrayB
    System.out.println(ArrayUtils.toString(CollectionUtils.subtract(listA, listB)));
    //[A, C, E]

}

/**
 * 集合是否为空
 */
@Test
public void testIsEmpty(){

    class Person{}
    class Girl extends Person{}

    List<Integer> first = new ArrayList<>();
    List<Integer> second = null;
    List<Person> boy = new ArrayList<>();
    //每个男孩心里都装着一个女孩
    boy.add(new Girl());
    //判断集合是否为空
    System.out.println(CollectionUtils.isEmpty(first));   //true
    System.out.println(CollectionUtils.isEmpty(second));   //true
    System.out.println(CollectionUtils.isEmpty(boy));   //false

    //判断集合是否不为空
    System.out.println(CollectionUtils.isNotEmpty(first));   //false
    System.out.println(CollectionUtils.isNotEmpty(second));   //false
    System.out.println(CollectionUtils.isNotEmpty(boy));   //true
}

/**
 * 集合是否相等
 */
@Test
public void testIsEqual(){

    class Person{}
    class Girl extends Person{
    }

    List<Integer> first = new ArrayList<>();
    List<Integer> second = new ArrayList<>();
    first.add(1);
    first.add(2);
    second.add(2);
    second.add(1);
    Girl goldGirl = new Girl();
    List<Person> boy1 = new ArrayList<>();
    //每个男孩心里都装着一个女孩
    boy1.add(new Girl());
    List<Person> boy2 = new ArrayList<>();
    //每个男孩心里都装着一个女孩
    boy2.add(new Girl());
    //比较两集合值
    System.out.println(CollectionUtils.isEqualCollection(first,second));   //true
    System.out.println(CollectionUtils.isEqualCollection(first,boy1));   //false
    System.out.println(CollectionUtils.isEqualCollection(boy1,boy2));   //false

    List<Person> boy3 = new ArrayList<>();
    //每个男孩心里都装着一个女孩
    boy3.add(goldGirl);
    List<Person> boy4 = new ArrayList<>();
    boy4.add(goldGirl);
    System.out.println(CollectionUtils.isEqualCollection(boy3,boy4));   //true
}

/**
 * 不可修改的集合
 * Collections.unmodifiableCollection可以得到一个集合的镜像，它的返回结果是不可直接被改变，否则会提示错误
 */
 @Test
public void testUnmodifiableCollection(){
    Collection<String> c = new ArrayList<>();
    Collection<String> s = CollectionUtils.unmodifiableCollection(c);
    c.add("boy");
    c.add("love");
    c.add("girl");
    //! s.add("have a error");
    System.out.println(s);
}

/**
 * CollectionUtils.collect
 * 从一个对象的集合中，获取某一属性的集合
 */
 List<String> nameList = (List<String>) CollectionUtils.collect(
    stuList, new Transformer() {
        public Object transform(Object arg0) {
            Student u = (Student) arg0;
            return u.getName();
        }
    });
    
/**
 * CollectionUtils.filter
 * 从一个对象集合中过滤出只符合要求的对象
 * 注意：这个Predicate类必须实现evaluate(Object obj) 方法，这个方法的返回值只要是true，说明这个对象是要保留下来的。如这个里面的是取 姓名叫 li的人，如果是其它特殊的、变动的名称。可以另外新创建一个 实现 Predicate的类。
 */
 CollectionUtils.filter(stuList, new Predicate(){
    public boolean evaluate(Object arg0) {
        Student v = (Student)arg0;
        return StringUtils.isNotBlank(v.getName()) && "li".equals(v.getName());
    }
});

/**
 * CollectionUtils.select
 * 集合筛选
 */
 @SuppressWarnings("unchecked")
List<Student> wang = (List<Student>) CollectionUtils.select(stuList, new Predicate() {
    @Override
    public boolean evaluate(Object object) {
        if (Student.class.isAssignableFrom(object.getClass())) {
            Student param = (Student) object;
            if (StringUtils.isNotBlank(param.getName()) && "wang".equalsIgnoreCase(param.getName())) {
                return true;
            }
        }
        return false;
    }
});

/**
 * public static <T> T find(Iterable<T> collection, Predicate<? super T> predicate)
 * 重要方法：借助Predicate达到神一般的效果，从此减少一半for循环。返回第一个找到的元素
 * 已过时。 从4.1开始，IterableUtils.find(Iterable, Predicate)改为使用
 * 如果输入集合或predicate为null，或者集合中没有元素与predicate匹配，则返回null。
 * 类型参数：T- Iterable包含的对象类型
 *  参数：
 * collection - 要搜索的集合，可能为null
 * predicate - 要使用的predicate，可以为null
 * 返回：集合的第一个元素与predicate匹配，如果找不到则返回null
 */

/**
 * public static <T> boolean filter(Iterable<T> collection,Predicate<? super T> predicate)
 * 重要方法：借助Predicate达到神一般的效果，从此减少一半for循环。直接改变容器c
 * 通过对每个元素使用predicate来过滤集合。如果predicate过滤返回false，则删除该元素。
 * 如果输入集合或predicate为null，则不会进行任何更改。
 * 类型参数：T- Iterable包含的对象类型
 *  参数：
 * collection - 从中​​获取输入的集合可能为null
 * predicate - 用作过滤器的predicate可以为null
 * 返回：如果此调用修改了集合，则返回true，否则返回false。
 */
 
 /**
 * public static <C> void  transform（Collection <C> collection，Transformer <？super C，？extends C> transformer）
 * 重要方法：还是神器，但是在jdk8中等同于foreach方法效果。如果jdk<8，可以用这个方法代替
 * 通过将Transformer应用于每个元素来转换集合。
 * 如果输入集合或predicate为null，则不会进行任何更改。
 * 此例程最适用于列表，其中set（）用于“适当的”进行转换。对于其他集合，clear（）和addAll（）用于替换元素。
 * 如果输入集合控制其输入（例如Set），并且Transformer创建重复项（或者无效），则由于调用此方法，集合的大小可能会减小。
 * 类型参数：T- Iterable包含的对象类型
 *  参数：
 * collection - 从中​​获取输入的集合可能为null
 * transformer  - 要执行的变换器可能为空
 * 返回：如果此调用修改了集合，则返回true，否则返回false。
 */
 
 /**
 * CollectionUtils.countMatches(c,predicate)
 * 根据predicate返回有多少元素满足预言，返回值int。
 *  
 * CollectionUtils.select(c,predicate)
 * 根据predicate找出满足的元素，组成新的collection并返回
 * 
 * CollectionUtils.select(c,predicate,outputCollection)
 * 根据predicate找出满足的元素，加入到outputCollection中。
 */
```