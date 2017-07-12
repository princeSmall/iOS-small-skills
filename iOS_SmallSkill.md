1. 设置UILable行间距

        NSMutableAttributedString* attrString = [[NSMutableAttributedString  alloc] initWithString:label.text];
        
        NSMutableParagraphStyle *style = [[NSMutableParagraphStyle alloc] init];
        
        [style setLineSpacing:20];
        
        [attrString addAttribute:NSParagraphStyleAttributeName value:style range:NSMakeRange(0, label.text.length)];
        
        label.attributedText = attrString;
        
2. UILabel显示不同颜色字体

        NSMutableAttributedString * string = [[NSMutableAttributedString alloc] initWithString:label.text];
        
        [string addAttribute:NSForegroundColorAttributeName value:[UIColor redColor] range:NSMakeRange(0,5)];

        [string addAttribute:NSForegroundColorAttributeName value:[UIColor greenColor] range:NSMakeRange(5,6)];
       
        [string addAttribute:NSForegroundColorAttributeName value:[UIColor blueColor] range:NSMakeRange(11,5)];
        
        label.attributedText = string;
        
3. 动画修改label上的文字
   
        方法一
        CATransition * animation = [CATransition animation];
        animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
        animation.type = kCATransitionFade;
        animation.duration = 0.75;
        [self.label.layer addAnimation:animation forKey:@"kCATransitionFade"];
        self.label.text = @"New";
 
        方法二
        [UIView transitionWithView:self.label
                      duration:0.25f
                       options:UIViewAnimationOptionTransitionCrossDissolve
                    animations:^{
                        self.label.text = @"Well done!";
                    } completion:nil];
 
         方法三
         [UIView animateWithDuration:1.0
                     animations:^{
                         self.label.alpha = 0.0f;
                         self.label.text = @"newText";
                         self.label.alpha = 1.0f;
                     }];

     
4. 播放一张张连续的图片

        加入现在有三张图片分别为animate_1、animate_2、animate_3
        方法一：
        imageView.animationImages = @[[UIImage imageNamed:@"animate_1"], [UIImage imageNamed:@"animate_2"], [UIImage imageNamed:@"animate_3"]];
        imageView.animationDuration = 1.0;
        方法二：
        imageView.image = [UIImage animatedImageNamed:@"animate_" duration:1.0];
        方法二解释下：
        这个方法会加载animate_为前缀的，后边0-1024，也就是animate_0、animate_1一直到animate_1024
        
5. 修改UISearBar内部背景颜色

         UITextField *textField = [_searchBar valueForKey:@"_searchField"];
         textField.backgroundColor = [UIColor redColor];
 
6. 监听UISlider拖动状态

        [slider addTarget:self action:@selector(sliderValurChanged:forEvent:) forControlEvents:UIControlEventValueChanged];
 
         - (void)sliderValurChanged:(UISlider*)slider forEvent:(UIEvent*)event {
           UITouch *touchEvent = [[event allTouches] anyObject];
           switch (touchEvent.phase) {
               case UITouchPhaseBegan:
                    NSLog(@"开始拖动");
                    break;
               case UITouchPhaseMoved:
                    NSLog(@"正在拖动");
                    break;
               case UITouchPhaseEnded:
                    NSLog(@"结束拖动");
                    break;
               default:
                    break;
              }
          }

7. MD5加密

        + (NSString *)md5:(NSString *)str
        {
           const char *concat_str = [str UTF8String];
           unsigned char result[CC_MD5_DIGEST_LENGTH];
           CC_MD5(concat_str, (unsigned int)strlen(concat_str), result);
           NSMutableString *hash = [NSMutableString string];
           for (int i =0; i<16; i++){
              [hash appendFormat:@"X", result[i]];
           }
           return [hash uppercaseString];
        }
8. base64加密       
   
        @interface NSData (Base64)
        /**
         *  @brief  字符串base64后转data
         */
        + (NSData *)dataWithBase64EncodedString:(NSString *)string
        {
          if (![string length]) return nil;
          NSData *decoded = nil;
        #if __MAC_OS_X_VERSION_MIN_REQUIRED < __MAC_10_9 || __IPHONE_OS_VERSION_MIN_REQUIRED < __IPHONE_7_0
         if (![NSData instancesRespondToSelector:@selector(initWithBase64EncodedString:options:)])
         {
        #pragma clang diagnostic push
        #pragma clang diagnostic ignored "-Wdeprecated-declarations"
        decoded = [[self alloc] initWithBase64Encoding:[string stringByReplacingOccurrencesOfString:@"[^A-Za-z0-9+/=]" withString:@"" options:NSRegularExpressionSearch range:NSMakeRange(0, [string length])]];
        #pragma clang diagnostic pop
         }
        else
        #endif
        {
        decoded = [[self alloc] initWithBase64EncodedString:string options:NSDataBase64DecodingIgnoreUnknownCharacters];
        }
        return [decoded length]? decoded: nil;
        }
        /**
          *  @brief  NSData转string
          *  @param wrapWidth 换行长度  76  64
          */
        - (NSString *)base64EncodedStringWithWrapWidth:(NSUInteger)wrapWidth
        {
           if (![self length]) return nil;
           NSString *encoded = nil;
          #if __MAC_OS_X_VERSION_MIN_REQUIRED < __MAC_10_9 || __IPHONE_OS_VERSION_MIN_REQUIRED < __IPHONE_7_0
           if (![NSData instancesRespondToSelector:@selector(base64EncodedStringWithOptions:)])
           {
          #pragma clang diagnostic push
          #pragma clang diagnostic ignored "-Wdeprecated-declarations"
        encoded = [self base64Encoding];
        #pragma clang diagnostic pop
 
         }
        else
        #endif
        {
        switch (wrapWidth)
        {
            case 64:
            {
                return [self base64EncodedStringWithOptions:NSDataBase64Encoding64CharacterLineLength];
            }
            case 76:
            {
                return [self base64EncodedStringWithOptions:NSDataBase64Encoding76CharacterLineLength];
            }
            default:
            {
                encoded = [self base64EncodedStringWithOptions:(NSDataBase64EncodingOptions)0];
            }
        }
         }
        if (!wrapWidth || wrapWidth >= [encoded length])
        {
           return encoded;
         }
         wrapWidth = (wrapWidth / 4) * 4;
         NSMutableString *result = [NSMutableString string];
           for (NSUInteger i = 0; i < [encoded length]; i+= wrapWidth)
         {
        if (i + wrapWidth >= [encoded length])
        {
            [result appendString:[encoded substringFromIndex:i]];
            break;
        }
        [result appendString:[encoded substringWithRange:NSMakeRange(i, wrapWidth)]];
        [result appendString:@"\r\n"];
          }
          return result;
         }
        /**
          *  @brief  NSData转string 换行长度默认64
          */
         - (NSString *)base64EncodedString
         {
              return [self base64EncodedStringWithWrapWidth:0];
         }
 
9. 3DES加密

        #import
        @interface NSData (3DES)
        /**
          *  利用3DES加密数据
          */
        - (NSData*)encryptedWith3DESUsingKey:(NSString*)key andIV:(NSData*)iv {
 
              NSData *keyData = [key dataUsingEncoding:NSUTF8StringEncoding];
 
              size_t dataMoved;
              NSMutableData *encryptedData = [NSMutableData dataWithLength:self.length + kCCBlockSize3DES];
 
              CCCryptorStatus result = CCCrypt(kCCEncrypt,kCCAlgorithm3DES,kCCOptionPKCS7Padding,keyData.bytes,keyData.length,iv.bytes,self.bytes,self.length,encryptedData.mutableBytes,encryptedData.length,&dataMoved);
 
             if (result == kCCSuccess) {
        encryptedData.length = dataMoved;
        return encryptedData;
                }
 
            return nil;
 
         }
        /**
          *  @brief   利用3DES解密数据
          */
        - (NSData*)decryptedWith3DESUsingKey:(NSString*)key andIV:(NSData*)iv {
 
           NSData *keyData = [key dataUsingEncoding:NSUTF8StringEncoding];
 
          size_t dataMoved;
          NSMutableData *decryptedData = [NSMutableData dataWithLength:self.length + kCCBlockSize3DES];
 
          CCCryptorStatus result = CCCrypt(kCCDecrypt,kCCAlgorithm3DES,kCCOptionPKCS7Padding,keyData.bytes,keyData.length,iv.bytes,self.bytes,self.length,decryptedData.mutableBytes,decryptedData.length,&dataMoved);
 
         if (result == kCCSuccess) {
              decryptedData.length = dataMoved;
              return decryptedData;
            }
 
           return nil;
 
        }
 
10. 页面跳转实现翻转动画
 
        modal方式
        TestViewController *vc = [[TestViewController alloc] init];
        vc.view.backgroundColor = [UIColor redColor];
        vc.modalTransitionStyle = UIModalTransitionStyleCoverVertical;
        [self presentViewController:vc animated:YES completion:nil];
 
        push方式:
        TestViewController *vc = [[TestViewController alloc] init];
        vc.view.backgroundColor = [UIColor redColor];
        [UIView beginAnimations:@"View Flip" context:nil];
        [UIView setAnimationDuration:0.80];
        [UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
        [UIView setAnimationTransition:UIViewAnimationTransitionFlipFromRight forView:self.navigationController.view cache:NO];
        [self.navigationController pushViewController:vc animated:YES];
        [UIView commitAnimations];
        
11. 多网络请求：

        dispatch_group_t group = dispatch_group_create();
        dispatch_queue_t serialQueue = dispatch_queue_create("com.wzb.test.www", DISPATCH_QUEUE_SERIAL);
        dispatch_group_enter(group);
        dispatch_group_async(group, serialQueue, ^{
        // 网络请求一
        [WebClick getDataSuccess:^(ResponseModel *model) {
            dispatch_group_leave(group);
        } failure:^(NSString *err) {
            dispatch_group_leave(group);
        }];
        });
        dispatch_group_enter(group);
          dispatch_group_async(group, serialQueue, ^{
        // 网络请求二
        [WebClick getDataSuccess:getBigTypeRM onSuccess:^(ResponseModel *model) {
            dispatch_group_leave(group);
        }                                  failure:^(NSString *errorString) {
            dispatch_group_leave(group);
        }];
        });
        dispatch_group_enter(group);
        dispatch_group_async(group, serialQueue, ^{
        // 网络请求三
        [WebClick getDataSuccess:^{
            dispatch_group_leave(group);
        } failure:^(NSString *errorString) {
            dispatch_group_leave(group);
        }];
        });
 
        // 所有网络请求结束后会来到这个方法
        dispatch_group_notify(group, serialQueue, ^{
        dispatch_async(dispatch_get_global_queue(0, 0), ^{
            dispatch_async(dispatch_get_main_queue(), ^{
                // 刷新UI
            });
        });
        });

12.  监听scrollView是否滚动到了顶部／底部
 
        -(void)scrollViewDidScroll: (UIScrollView*)scrollView
         {
           float scrollViewHeight = scrollView.frame.size.height;
           float scrollContentSizeHeight = scrollView.contentSize.height;
           float scrollOffset = scrollView.contentOffset.y;
 
           if (scrollOffset == 0)
            {
        // 滚动到了顶部
            }
           else if (scrollOffset + scrollViewHeight ==  scrollContentSizeHeight)
            {
        // 滚动到了底部
            }
          }
 
13. 过滤空格
 
 
        -(NSString *)userName:(NSString *)name{
    
        name = [name stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];//前后空格
        
        name = [name stringByReplacingOccurrencesOfString:@" " withString:@""];//中间空格
        return name;
        }
        
14. 获取邮箱用户部分和域名部分

        - (BOOL)validateEmail:(NSString *)email {
            if ((0 != [email rangeOfString:@"@"].length) &&
                (0 != [email rangeOfString:@"."].length)) {
                 NSCharacterSet *tmpInvalidCharSet =
        [[NSCharacterSet alphanumericCharacterSet] invertedSet];
                NSMutableCharacterSet *tmpInvalidMutableCharSet =
        [tmpInvalidCharSet mutableCopy];
                [tmpInvalidMutableCharSet removeCharactersInString:@"_-"];
        
                 NSRange range1 = [email rangeOfString:@"@" options:NSCaseInsensitiveSearch];
        
        //取得用户名部分
        NSString *userNameString = [email substringToIndex:range1.location];
        NSLog(@"length---%ld",userNameString.length);
        if (userNameString.length < 1 || userNameString.length > 20) {
            return NO;
        }else
        //取得域名部分
        NSString *domainString = [email substringFromIndex:range1.location + 1];
        NSArray *domainArray = [domainString componentsSeparatedByString:@"."];
        
        for (NSString *string in domainArray) {
            NSRange rangeOfInavlidChars =
            [string rangeOfCharacterFromSet:tmpInvalidMutableCharSet];
            if (rangeOfInavlidChars.length != 0 || [string isEqualToString:@""])
                return NO;
        }
        
        return YES;
        } else {
        return NO;
               }
        }

        
        
        
        