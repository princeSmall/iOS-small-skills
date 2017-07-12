# iOS-small-skills
这是我使用html写的oc操作的一些小技巧，网页能更好的展示并无关联的小功能

例如：

    /***
     1、UserName[1-20] + @ + English or Number . + English[2-4]
     2、English and Number[6-20]
    **/

       - (BOOL)isValidateEmail:(NSString *)email {
          NSString *emailRegex = @"[A-Z0-9a-z._%+-]{1,20}+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,4}";
          NSPredicate *emailTest =
          [NSPredicate predicateWithFormat:@"SELF MATCHES %@", emailRegex];
          return [emailTest evaluateWithObject:email];
    }
     - (BOOL) validatePassword:(NSString *)passWord
    {
        NSString * passWordRegex = @"^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{6,20}$";
    
        NSPredicate *passWordPredicate = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",passWordRegex];
    
       return [passWordPredicate evaluateWithObject:passWord];
    }

