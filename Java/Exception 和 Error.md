# Exception 和 Error

## 關於 Exception 與 Error 的基本概念

Exception 與 Error 都是繼承自 Throwable, 在 Java 的世界裡, 只有 Throwable 類型的 instance 才可以被 throw 或著 catch, 其為 exception handling 的基本組成類型. 至於 Exception 與 Error 的差異, 這部分就體現出了 Java 設計者對不同異常情境的分類:

- Exception: 通常指程式運行時所出現的可預料之意外狀況, 基本上都要進行 catch 的動作, 然後進行相應處理, 如 IOException.
- Error: 指在正常情況下, 不太可能出現的問題, 絕大部分的 Error 都會導致程式 (e.g. JVM 本身) 處於一種不正常且不可恢復的狀態. 所以對於這種情況, 你也不太需要去 catch 了, 因為也沒什麼意義. 常見的如 OutOfMemoryError / StackOverflowError 這些, 都是繼承自 Error.

**再來, Exception 又可以分成兩類:**

- **Checked Exception**: 又稱受檢例外, 通常在原始碼中必須顯式地 catch 並且處理, 這部分算是 compile time 會檢查的部分.

  Java.lang.ClassNotFoundException

  Java.lang.CloneNotSupportedException

  Java.lang.IllegalAccessException

  Java.lang.InterruptedException

  Java.lang.NoSuchFieldException

  Java.lang.NoSuchMetodException

- **Unchecked Exception**: 又稱非受檢例外, 就是所謂的 **RuntimeException**, 常見的像是 NullPointerException, ArrayIndexOutOfBoundsException. 這種類型的例外通常是可以透過撰寫相應程式以避免的邏輯錯誤, 可以根據當下的情境來判斷是不是要 catch, 且在 compile time 並不會強制要求要 catch.



[]: https://medium.com/@clu1022/java筆記-exception-與-error-dbdf9a9b0909	"Java筆記 — Exception 與 Error"

