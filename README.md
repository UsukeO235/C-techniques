# C-Techniques
C Programming Techniques I Usually Use
よく使うC言語テクニック

## Overview
このリポジトリは私がC言語プログラムを作成するときによく使うテクニックをまとめたものです。
ここに書かれていることはあくまで個人の原則であり、組織のルールがあればそちらを優先します。

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
