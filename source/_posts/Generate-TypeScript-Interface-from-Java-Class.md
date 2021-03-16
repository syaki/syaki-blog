---
title: Generate TypeScript Interface from Java Class
date: 2021-03-09 22:16:55
tags: [TypeScript, Java]
---

最近在对 Web 项目做架构升级，计划使用 TypeScript 。

**为了 NO AnyScript !!!**

因此需要对已有的 Java Web 应用接口的 Model Class 生成对应的 TypeScript Interface 。

为了不减少摸鱼时间，当然是写个脚本生成 🐶

# 具体思路

1. 维护一个 Java 类型映射到 TypeScript 类型的 Map

   ```java
   Map<String, String> javaToTypeScriptTypeMap = new HashMap<String, String>() {{
       put("int", "number");
       put("java.lang.Integer", "number");
       put("java.util.List", "Array");
       // ...
   }}
   ```

2. 根据包名扫描获取所有的 Class

   ```java
   public static List<Class> getClassListFromPackage(String packageName) {
       // 获取路径
       String packageDirName = packageName.replace('.', '/');
       Thread.currentThread().getContextClassLoader().getResources(packageDirName);

       // 根据路径扫描文件并获取 Class
       String className = file.getName();
       Thread.currentThread().getContextClassLoader().loadClass(packageName + "." + className);
   }
   ```

3. 遍历获取到的每个 Class 里的所有 Field 的类型和名称，并收集成一个 Map

   ```java
   /**
    * 第一层 Map key 为 className
    * 第二层 Map key 为 TypeScript type , value 为对应类型的字段名 list
    */
   Map<String, Map<String, List<String>>> res = new HashMap<>();

   // foreach classList
   Field[] fields = clazz.getDeclaredFields();

   // foreach fields
   String fieldName = field.getName();
   String fieldType = field.getType().getName();
   String tsFieldType = javaToTypeScriptTypeMap.getOrDefault(fieldType, fieldType);

   // 对于 array 和 map 的范型参数需要特殊处理
   // foreach ((ParameterizedType) field.getGenericType()).getActualTypeArguments()
   t = t.replaceAll("class ", "");
   t = t.replaceAll(packageName + ".", "");
   // foreach javaToTypeScriptTypeMap.entrySet()
   String k = entry.getKey();
   String v = entry.getValue();
   t = t.replace(k, v);
   ```

4. 遍历收集的 Map 生成 ts 文件

   ```java
   StringBuilder typeFile = new StringBuilder();
   res.forEach((ck, cv) -> {
       typeFile.append("export class ");
       typeFile.append(ck);
       typeFile.append(" {\n");

       cv.forEach((fk, fv) -> fv.forEach(field -> {
           typeFile.append("  ");
           typeFile.append(field);
           typeFile.append("!: ");
           typeFile.append(fk);
           typeFile.append(";\n");
       }));

       typeFile.append("}\n\n");
   });
   try {
       BufferedWriter out = new BufferedWriter(new FileWriter(packageName + ".ts"));
       out.write(typeFile.toString());
       out.close();
   } catch (Exception e) {
       e.printStackTrace();
   }
   ```

5. 最后得到一个以 packageName 为名称的 TypeScript 文件。

   ```typescript
   export class Person {
     name!: string;
     age!: number;
     friendList!: Array<Person>;
   }
   ```

# 缺陷

目前的实现方案有个缺陷，无法处理 class 的范型参数，类似于

```java
class Page<T> {
    private int index;
    private int size;
    private List<T> data;
}
```

只会生成

```typescript
export interface Page {
    index!: number;
    size!: number;
    data!: Array<T>;
}
```

# Update: 2021/03/10

可以通过 toGenericString 方法获取包含范型参数的类名

```java
clazz.toGenericString()
// sample: public class com.demo.model.page<T> {}
```

# Update: 2021/03/15

使用过程中发现遗漏了对 class 的继承关系处理

```java
Map<String, String> superClassMap = new HashMap<>();
Class superClass = clazz.getSuperclass();
if (superClass != null) {
    if (!"java.lang.Object".equals(superClass.getName())) {
        superClassMap.put(
            className,
            superClass.getName().replaceAll(packageName + ".", "")
        );
    }
}
```

# todo: 父子类有范型参数的情况

```java
class Sup<T> {
    private List<T> list;
}
class Sub<T> extends Sup<T> {
    private String name;
}
/**
 * Java 的范型只是类型擦除，此处 Sub.list 的类型为 Object
 */
class Sub extends Sup {
    private String name;
}
```

```typescript
class Sup<T> {
  list: Array<T> | null | undefined;
}
/**
 * 此处会报错
 */
class Sub extends Sup {
  name: string | null | undefined;
}
```
