```java
package com.example.boot.domain;

import java.io.Serializable;

public class R <T> implements Serializable {

    Integer status;

    String message;

    T data;

    public R(Integer status, String message, T data) {
        this.status = status;
        this.message = message;
        this.data = data;
    }

    public R(Integer status, String message) {
        this.status = status;
        this.message = message;
    }

    public static <T> R<T> ok(String message, T data){
        return new R(StatusCode.SUCCESS.getCode(),message,data);
    }

    public static <T> R<T> ok(String message){
        return new R(StatusCode.SUCCESS.getCode(),message);
    }

    public static <T> R<T> error(String message){
        return new R(StatusCode.ERROR.getCode(),message);
    }

    public Integer getStatus() {
        return status;
    }

    public void setStatus(Integer status) {
        this.status = status;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
```