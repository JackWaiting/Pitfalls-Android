# Exception
在日常开发过程中，我们经常会遇到各种 Exception，导致这些异常的背后原因各种各样，在这里我们主要是整理、记录和归纳，在实际的项目开发过程中所遇到的常见异常的具体情况和分析。


## 5.RuntimeException

### 5.1: java.lang.RuntimeException: Parcelable encountered IOException writing serializable

#### 问题描述：
Android中的 Activity 传递数据时，为了方便往往将很多数据封装成对象，然后将整个对象传递过去。传对象的时候有两种情况，一种是实现Parcelable接口，一种是实现 Serializable 接口。
可以用 bundle.putSerializable（Key，Object） 传递数据或者直接用 intent putExtrr（Key，Object） 传递数据。但当我们调用 bundle.putSerializable（Key，Object）时如抛出此异常可用以下方法解决。

#### 解决方法：

1、抛出Java.io.NotSerializableException异常

抛出这个异常是因为你的对象没有实现Serializable接口，只要实现该接口就好了。

2、抛出java.lang.RuntimeException异常

抛出这个异常是因为传递的对象里面的对象也要实现Serializable接口

## 4. IllegalAccessException

## 3. IllegalStateException

## 2. Transformexception

## 1. RemoteServiceException

