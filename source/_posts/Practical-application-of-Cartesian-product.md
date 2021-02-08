---
title: Practical application of Cartesian product
date: 2020-08-17 14:31:06
tags:
---

利用笛卡尔乘积实现中文姓名拼音校验

由于中文存在多音字的情况，对于每个字可能的拼音求笛卡尔积。

## 接口定义

```java
public interface IChineseNamePinyin {
    boolean verifyChineseNamePinyin(String name, String pinyin);
}
```

## 具体实现

利用 `Java 8 Stream flatMap()` 方法对姓名中每个字可能的拼音求笛卡尔积。

```java
public ChineseNamePinyinImpl implements IChineseNamePinyin {
    /**
     * 中文姓名拼音校验
     * @param name   姓名
     * @param pinyin 拼音
     * @return 中文姓名拼音校验结果
     */
    @Override
    public boolean verifyChineseNamePinyin(String name, String pinyin) {
        if (StringUtils.isNullOrEmpty(name) || StringUtils.isNullOrEmpty(pinyin)) return false;

        // 初始化 pinyin4j format
        HanyuPinyinOutputFormat format = new HanyuPinyinOutputFormat();
        format.setCaseType(HanyuPinyinCaseType.LOWERCASE);
        format.setToneType(HanyuPinyinToneType.WITHOUT_TONE);
        format.setVCharType(HanyuPinyinVCharType.WITH_V);

        // 将姓名转为 char array
        char[] nameChars = name.trim().toCharArray();

        List<List<String>> nameCharPinyinList = Lists.newLinkedList();
        List<String> namePinyinList = Lists.newLinkedList();
        namePinyinList.add("");

        try {
            for (char nameChar : nameChars) {
                // 过滤非中文字符
                if (!Character.toString(nameChar).matches("[\\u4E00-\\u9FA5]+")) continue;

                // 统计多音字所有的拼音
                nameCharPinyinList.add(this.nameCharToHanyuPinyinStringList(nameChar, format));
            }
        } catch (Exception e) {
            return false;
        }

        // 将所有拼音的组合收集成一个 list
        for (List<String> strings : nameCharPinyinList) {
            namePinyinList = namePinyinList.stream()
                    .flatMap(r -> strings.stream()
                            .map(c -> r + c))
                    .collect(Collectors.toList());
        }

        // 判断拼音是否匹配
        return namePinyinList.contains(pinyin);
    }

    /**
     * 调用 PinyinHelper 获取拼音
     */
    private List<String> nameCharToHanyuPinyinStringList(char nameChar, HanyuPinyinOutputFormat format) throws Exception {
        return Arrays.asList(PinyinHelper.toHanyuPinyinStringArray(nameChar, format));
    }
}
```
