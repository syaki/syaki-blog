---
title: 利用 AbstractProcessor 增加摸鱼时间
date: 2020-02-01 19:44:52
tags: Java
---

绝不手写能通过代码生成的代码！！！

# 背景

内部 Web 应用框架要求所有应用对外暴露的接口要定义一份 interface ，并且实现它。

如下，每新增一个 HTTP 接口都需要在 TestSOAService 中添加函数定义，并在 TestSOAServiceImpl 实现。

```java
public interface TestSOAService {
    HelloWorldResponseType helloWorld(HelloWorldRequestType request) throws Exception;
}

@Service
public class TestSOAServiceImpl implements TestSOAService {
    @Autowired
    private ServiceInvoker serviceInvoker;

    public HelloWorldResponseType helloWorld(HelloWorldRequestType request) throws Exception {
        return serviceInvoker.invoke(HelloWorldService.class, request);
    }
}
```

# 解决办法

```java
@Service
public class DefaultServiceInvokerImpl implements ServiceInvoker {
    private ApplicationContext context;

    @Autowired
    public DefaultServiceInvokerImpl(ApplicationContext context) { this.context = context; }

    @Override
    public <Q, S, T> S invoke(Class<T> cls, Q req) throws Exception {
        return context.getBean(cls).service(req);
    }
}
```

由于每个接口都是统一调用自定义实现的 ServiceInvoker.invoke 方法，所以每个接口的定义和实现只有名称不同。

因此考虑通过定义一个 AbstractProcessor 的子类来生成接口定义和实现文件代码。

## 自定义 Annotation

自定义一个 Annotation ，添加到每个接口 Service 类上。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Service
public @interface WebService {
    String name() default "TestSOAService";
}

@WebService
public class HelloWorldService {
    public HelloWorldResponseType service(HelloWorldRequestType request) throws Exception {
        HelloWorldResponseType response = new HelloWorldResponseType();
        return response;
    }
}
```

## 定义一个 AbstractProcessor 的子类

```java
public class WebServiceGenerateProcessor extends AbstractProcessor {
    private Map<String, Pair<TypeSpec.Builder, TypeSpec.Builder>> files = Maps.newLinkedHashMap();
    private JavacElements elementUtils;
    private JavacTypes typeUtils;
    private Messager messager;

    @Override
    public Set<String> getSupportedAnnotationTypes() { return Collections.singleton(WebService.class.getName()); }

    @Override
    public SourceVersion getSupportedSourceVersion() { return SourceVersion.RELEASE_8; }

    @Override
    public synchronized void init(ProcessingEnvironment processingEnv) {
        super.init(processingEnv);
        this.elementUtils = (JavacElements) processingEnv.getElementUtils();
        this.typeUtils = (JavacTypes) processingEnv.getTypeUtils();
        this.messager = processingEnv.getMessager();
        this.messager.printMessage(Diagnostic.Kind.NOTE, "Generate WebServices...");
    }

    @Override
    public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
        if (roundEnv.processingOver()) {
            /**
             * 写文件
             * JavaFile.builder
             */
        } else {
            /**
             * 生成文件内容
             * 通过 roundEnv.getElementsAnnotatedWith 获取 service class
             * 通过 TypeSpec.interfaceBuilder TypeSpec.classBuilder
             * MethodSpec.methodBuilder CodeBlock.builder AnnotationSpec.builder
             * 等方法生成文件内容
             */
        }
        return false;
    }
}
```

# 效果

编译器会在编译期找到所有添加了 WebService 注解的类，并根据我们定义的 WebServiceGenerateProcessor.process 逻辑生成 TestSOAService 和 TestSOAServiceImpl 文件。

这样每天又可以多出 5 分钟的摸鱼时间 🐶 。
