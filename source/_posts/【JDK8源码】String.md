title: 【JDK8源码】String
date: 2019-10-30 12:10:46
categories: Java
tags: []
---
# String

------

> Strings are constant; their values cannot be changed after they are created. 
>
> String buffers support mutable strings.

看注释我们知道String是一个字符常量，创建后不能被改变的

而StringBuffer是一个可变的字符串

> The Java language provides special support for the string concatenation operator (&nbsp;+&nbsp;), and for conversion of other objects to strings. 
>
> String concatenation is implemented through the {@code StringBuilder}(or {@code StringBuffer}) class and its {@code append} method.

由此可知Java对String提供了特殊的连接符(+)并且可以将别的对象转换为String

## 0. 实现

```java
public final class String    implements java.io.Serializable, Comparable<String>, CharSequence
```

- Serializable(序列化)接口,没有任何的方法和域，仅用于表示序列化的语义

  ```java
  public interface Serializable {
  }
  ```

- Comparable<String>接口:这个接口只有一个public int compareTo(T o);接口，用于两个实例化对象比较大小

  ```java
  public int compareTo(T o);
  ```

- CharSequence： 这个接口是一个只读的字符序列。包括length(), charAt(int index), subSequence(int start, int end)这几个API接口，值得一提的是，StringBuffer和StringBuild也是实现了改接口。 

## 1. 属性

```java
public static final Comparator<String> CASE_INSENSITIVE_ORDER
                                         = new CaseInsensitiveComparator();

 private final char value[];

private int hash; // Default to 0
```



- value：用来存储字符，是一个char类型的数组
- hash：字符串的hashcode
- CASE_INSENSITIVE_ORDER ：用于忽略大小写的字符串比较

## 2. 构造方法

```java
public String() {
        this.value = "".value;
    }
// 空构造方法将value字符数组赋进空值
//*************************************
public String(String original) {
        this.value = original.value;
        this.hash = original.hash;
    }

 public String(char value[]) {
        this.value = Arrays.copyOf(value, value.length);
    }
 public String(char value[], int offset, int count) {
        if (offset < 0) {
            throw new StringIndexOutOfBoundsException(offset);
        }
        if (count <= 0) {
            if (count < 0) {
                throw new StringIndexOutOfBoundsException(count);
            }
            if (offset <= value.length) {
                this.value = "".value;
                return;
            }
        }
        // Note: offset or count might be near -1>>>1.
        if (offset > value.length - count) {
            throw new StringIndexOutOfBoundsException(offset + count);
        }
        this.value = Arrays.copyOfRange(value, offset, offset+count);
    }
```

 　String支持多种初始化方法，包括接收String，char[],byte[],StringBuffer等多种参数类型的初始化方法。但本质上，其实就是将接收到的参数传递给全局变量value[]。 

## 3. 内部类

String只有一个内部类那就是

```java
private static class CaseInsensitiveComparator
            implements Comparator<String>, java.io.Serializable {
        // use serialVersionUID from JDK 1.2.2 for interoperability
        private static final long serialVersionUID = 8575799808933029326L;

        public int compare(String s1, String s2) {
            int n1 = s1.length();
            int n2 = s2.length();
            int min = Math.min(n1, n2);
            for (int i = 0; i < min; i++) {
                char c1 = s1.charAt(i);
                char c2 = s2.charAt(i);
                if (c1 != c2) {
                    c1 = Character.toUpperCase(c1);
                    c2 = Character.toUpperCase(c2);
                    if (c1 != c2) {
                        c1 = Character.toLowerCase(c1);
                        c2 = Character.toLowerCase(c2);
                        if (c1 != c2) {
                            // No overflow because of numeric promotion
                            return c1 - c2;
                        }
                    }
                }
            }
            return n1 - n2;
        }
```

这里我就有一个疑惑，为什么String有CompareTo方法，为啥还要一个CaseInsensitiveComparator内部静态类来实现呢？

其实一切都是为了代码复用

如果我们每次都想要忽略大小写来比较大小的话(这样场景可能比较多)我们就需要每次都重写这个方法了

## 4. 其他方法

```java
 public int length() {
        return value.length;
    }


public boolean isEmpty() {
        return value.length == 0;
    }

 public char charAt(int index) {
        if ((index < 0) || (index >= value.length)) {
            throw new StringIndexOutOfBoundsException(index);
        }
        return value[index];
    }
```

知道String的内部是由数组实现的我们就可以很清晰的指导这几个方法的实现了

```java
void getChars(char dst[], int dstBegin) {
        System.arraycopy(value, 0, dst, dstBegin, value.length);
    }

 public void getChars(int srcBegin, int srcEnd, char dst[], int dstBegin) {
        if (srcBegin < 0) {
            throw new StringIndexOutOfBoundsException(srcBegin);
        }
        if (srcEnd > value.length) {
            throw new StringIndexOutOfBoundsException(srcEnd);
        }
        if (srcBegin > srcEnd) {
            throw new StringIndexOutOfBoundsException(srcEnd - srcBegin);
        }
        System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin);
    }
```

而getChars时调用arraycopy来实现的，我们在很多类中都看到这种手法,比如ArrayList里面

```java
@Deprecated
    public void getBytes(int srcBegin, int srcEnd, byte dst[], int dstBegin) {
        if (srcBegin < 0) {
            throw new StringIndexOutOfBoundsException(srcBegin);
        }
        if (srcEnd > value.length) {
            throw new StringIndexOutOfBoundsException(srcEnd);
        }
        if (srcBegin > srcEnd) {
            throw new StringIndexOutOfBoundsException(srcEnd - srcBegin);
        }
        Objects.requireNonNull(dst);

        int j = dstBegin;
        int n = srcEnd;
        int i = srcBegin;
        char[] val = value;   /* avoid getfield opcode */

        while (i < n) {
            dst[j++] = (byte)val[i++];
        }
    }

//----------------------------------------
public byte[] getBytes(String charsetName)
            throws UnsupportedEncodingException {
        if (charsetName == null) throw new NullPointerException();
        return StringCoding.encode(charsetName, value, 0, value.length);
    }
//----------------------------------------
public byte[] getBytes(Charset charset) {
        if (charset == null) throw new NullPointerException();
        return StringCoding.encode(charset, value, 0, value.length);
    }
```

获取当前字符串的二进制，其本质都是调用StringCoding.encode实现的，无论返回的byte还是byte数组

```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }//先判断两个对象是否相同
        if (anObject instanceof String) {//判断传入的是否为String 类型
            String anotherString = (String)anObject;//转换类型
            int n = value.length;//得到长度
            if (n == anotherString.value.length) {//判断长度是否相同
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])//逐个字符比较
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

equals本质就是通过一个字符一个字符比较得出是否相同，另外一个是contentEquals

```java
public boolean contentEquals(StringBuffer sb) {
        return contentEquals((CharSequence)sb);
    }

    private boolean nonSyncContentEquals(AbstractStringBuilder sb) {
        char v1[] = value;
        char v2[] = sb.getValue();
        int n = v1.length;
        if (n != sb.length()) {
            return false;
        }
        for (int i = 0; i < n; i++) {
            if (v1[i] != v2[i]) {
                return false;
            }
        }
        return true;
    }

public boolean contentEquals(CharSequence cs) {
        // Argument is a StringBuffer, StringBuilder
        if (cs instanceof AbstractStringBuilder) {
            if (cs instanceof StringBuffer) {
                synchronized(cs) {
                   return nonSyncContentEquals((AbstractStringBuilder)cs);
                }
            } else {
                return nonSyncContentEquals((AbstractStringBuilder)cs);
            }
        }
        // Argument is a String
        if (cs instanceof String) {
            return equals(cs);
        }
        // Argument is a generic CharSequence
        char v1[] = value;
        int n = v1.length;
        if (n != cs.length()) {
            return false;
        }
        for (int i = 0; i < n; i++) {
            if (v1[i] != cs.charAt(i)) {
                return false;
            }
        }
        return true;
    }
```

这个是比较StringBuffer、StringBuild和String的，这里也看出来StringBuffer和StringBuild都是实现CharSequence接口的， 源码中先判断参数是从哪一个类实例化来的，再根据不同的情况采用不同的方案，不过其实大体都是采用上面那个for循环的方式来进行判断两字符串是否内容相同。 

```java
 public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        int lim = Math.min(len1, len2);//取最小值
        char v1[] = value;
        char v2[] = anotherString.value;//存储使用,防止修改里面的数据

        int k = 0;
        while (k < lim) {
            char c1 = v1[k];
            char c2 = v2[k];
            if (c1 != c2) {
                return c1 - c2;//当遇到第一个较小的字符时，判定该字符串小，返回
            }
            k++;
        }
        return len1 - len2;//字符串长度较大的较大
    }
```

 其核心就是那个while循环，通过从第一个开始比较每一个字符，当遇到第一个较小的字符时，判定该字符串小。 

 一种是在较小长度的字符粗每个字符都和另一个字符串的每个字符相等，那么字符串长度较大的较大。 

```java
public boolean startsWith(String prefix, int toffset) {
        char ta[] = value;//获取原字符串，防止被修改
        int to = toffset;//字符串开始寻找的位置
        char pa[] = prefix.value;//获取要寻找的字符串的值
        int po = 0;
        int pc = prefix.value.length;
        // Note: toffset might be near -1>>>1.
        if ((toffset < 0) || (toffset > value.length - pc)) {
            return false;
        }
        while (--pc >= 0) {
            if (ta[to++] != pa[po++]) {//出现不相同返回false
                return false;
            }
        }
        return true;
    }
```

```java
//s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]
//hash值的计算公式
public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {//判断hash值==0是为了缓存hash值，hash值默认为0
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
```

为什么用31这个神奇的数字呢？

- 31是奇素数。

- **哈希分布较为均匀。偶数的冲突率基本都很高，只有少数例外。较小的乘数，冲突率也比较高，如1至20**。

- **哈希计算速度快。可用移位和减法来代替乘法。现代的VM可以自动完成这种优化，如31 \* i = (i << 5) – i**。

- 31和33的计算速度和哈希分布基本一致，整体表现好，选择它们就很自然了。

  大家可以参考,[为什么Java String哈希乘数为31？](http://ddrv.cn/a/16343)

```java
public int indexOf(int ch, int fromIndex) {
        final int max = value.length;//获取字符串长度
    	//判断越界
    	if (fromIndex < 0) {
            fromIndex = 0;
        } else if (fromIndex >= max) {
            // Note: fromIndex might be near -1>>>1.
            return -1;
        }
    	
		//这个字符小于最小提供字符编码
        if (ch < Character.MIN_SUPPLEMENTARY_CODE_POINT) {
            // handle most cases here (ch is a BMP code point or a
            // negative value (invalid code point))
            final char[] value = this.value;
            for (int i = fromIndex; i < max; i++) {
                if (value[i] == ch) {
                    return i;
                }
            }
            return -1;
        } else {
            return indexOfSupplementary(ch, fromIndex);
        }
    }
```

而在Character中看到

```java
public static final int MIN_SUPPLEMENTARY_CODE_POINT = 0x010000;
```

这表明在java中char存储的值通常都是比ox010000小的，就是BMP类型的字符。
而当比这个值大的时候，就是增补字符了，那么会调用Character先判断是否是有效的字符，再进一步处理。

## 5.总结

String类的底层其实使用char [] values 来实现的，抓住这个要点我们就很容易可以理解了