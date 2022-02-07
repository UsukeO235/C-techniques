# C-techniques
C Programming Techniques I Usually Use
よく使うC言語テクニック

## gotoでエラー処理
原則はearly-returnだが、malloc/freeを使わなければならない場合はgotoを使って1ヶ所でreturnする。
```c
int main()
{
    unsigned int size1 = 16;
    unsigned int size2 = 32;
    
    char* ptr1 = (char*)malloc(sizeof(char) * size1);
    if(ptr1 == NULL)
    {
        goto exit_ptr1_malloc_failed;
    }
    
    char* ptr2 = (char*)malloc(sizeof(char) * size2);
    if(ptr2 == NULL)
    {
        goto exit_ptr2_malloc_failed;
    }
    
    /* ***************** do something ***************** */
    
    /****************************************************/
    
    free(ptr2);
    
exit_ptr2_malloc_failed:
    free(ptr1);
    
exit_ptr1_malloc_failed:
    
    return 0;
}
```
