---
layout: post
title:  "SDWebImage之UIView+WebCacheOperation"
date:   2017-04-01 10:59:21 +0700
categories: jekyll update
---
# SDWebImage之UIView+WebCacheOperation

------

## 这个类中有四个方法，分别是：
```Objective-C
//简单翻译，个人的理解
/**通过一个key建立一个imageoperation字典，后边可以根据key取出imageoperation
 *  Set the image load operation (storage in a UIView based dictionary)
 *
 *  @param operation the operation
 *  @param key       key for storing the operation
 */
- (void)sd_setImageLoadOperation:(nullable id)operation forKey:(nullable NSString *)key;

/**把当前的View的相关的operations全部取消
 *  Cancel all operations for the current UIView and key
 *
 *  @param key key for identifying the operations
 */
- (void)sd_cancelImageLoadOperationWithKey:(nullable NSString *)key;

/**根据key移除相应的operations
 *  Just remove the operations corresponding to the current UIView and key without cancelling them
 *
 *  @param key key for identifying the operations
 */
- (void)sd_removeImageLoadOperationWithKey:(nullable NSString *)key;

//声明一个字典
- (SDOperationsDictionary *)operationDictionary {
//使用runtime将self与这个字典关联起来，并通过loadOperationKey来获字典
    SDOperationsDictionary *operations = objc_getAssociatedObject(self, &loadOperationKey);
    if (operations) {
        return operations;
    }
    operations = [NSMutableDictionary dictionary];
    objc_setAssociatedObject(self, &loadOperationKey, operations, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    return operations;
}

```
没有啦，完啦，欢迎勘误。


