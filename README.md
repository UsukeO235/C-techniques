# C-Techniques
C Programming Techniques I Usually Use
よく使うC言語テクニック

## Overview
このリポジトリは私がC言語プログラムを作成するときによく使うテクニックをまとめたものです。
ここに書かれていることはあくまで個人の原則であり、組織のルールがあればそちらを優先します。

## 特殊なプログラムではソースコードを書く前に仕様書・説明書を書く
成果物の作成優先度は以下とする。プログラムを作らずに目的を達成できないか十分に検討する。
1. 仕様書・説明書
1. ソースコード
    1. コメント
    1. 処理

## コメントは「何をしているか」よりも「何をしたいか」を優先して書く
執筆中。

## gotoでエラー処理
原則はearly-returnだが、malloc/freeを使わなければならない場合はgotoを使って1ヶ所でreturnする。
処理の流れが複雑になることを防ぐため、gotoでジャンプしてよいのは下方向にのみとし、上方向にはジャンプしないこととする。

```c
int main()
{
    int error_code = 0;
    
    unsigned int size1 = 16;
    unsigned int size2 = 32;
    
    char* ptr1 = (char*)malloc(sizeof(char) * size1);
    if(ptr1 == NULL)
    {
        error_code = 1;
        goto exit_ptr1_malloc_failed;
    }
    
    char* ptr2 = (char*)malloc(sizeof(char) * size2);
    if(ptr2 == NULL)
    {
        error_code = 2;
        goto exit_ptr2_malloc_failed;
    }
    
    /* ***************** do something ***************** */
    
    /****************************************************/
    
    free(ptr2);
    
exit_ptr2_malloc_failed:
    free(ptr1);
    
exit_ptr1_malloc_failed:
    
    return error_code;
}
```

上記の例ではエラーコードとそれに対応する終了処理タグの両方を設計、管理する必要がある。
switch文のfall throughというテクニックを使うことで、この問題を回避できる。
なお、ここではfall through利用の是非については考えない。

```c
#define ERROR_SUCCESS 0
#define ERROR_PTR1_MALLOC_FAILED 1
#define ERROR_PTR2_MALLOC_FAILED 2

int main()
{
    int error_code = ERROR_SUCCESS;

    unsigned int size1 = 16;
    unsigned int size2 = 32;
    
    char* ptr1 = (char*)malloc(sizeof(char) * size1);
    if(ptr1 == NULL)
    {
        error_code = ERROR_PTR1_MALLOC_FAILED;
        goto exit;
    }
    
    char* ptr2 = (char*)malloc(sizeof(char) * size2);
    if(ptr2 == NULL)
    {
        error_code = ERROR_PTR2_MALLOC_FAILED;
        goto exit;
    }
    
    /* ***************** do something ***************** */
    
    /****************************************************/

exit:
    switch(error_code)
    {
        case ERROR_SUCCESS:
            free(ptr2);
        case ERROR_PTR2_MALLOC_FAILED:
            free(ptr1);
        case ERROR_PTR1_MALLOC_FAILED:
            // None
        default:
            break;
    }

    return error_code;
}
```
