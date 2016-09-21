# TTNavigationBar-alpha
滑动tableview  导航栏渐变，导航栏文字上移
本文所引用的布局类cocopods进行安装
pod ‘Masonry’
#import <Masonry/Masonry.h>
按照顺序添加视图
#pragma mark-- 生命周期
- (void)viewDidLoad {
    [super viewDidLoad];

    self.title = @"FadeNavigationDemo";
       self.tableView.rowHeight = 100;
    
    
    
    self.navigationController.navigationBar.hidden = YES;
    [self.view addSubview:self.scaleImageView];    // 设置展示图片的约束
    [_scaleImageView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.top.mas_equalTo(0);
        make.left.equalTo(self.view.mas_left);
        make.right.equalTo(self.view.mas_right);
        make.height.mas_equalTo(kHeardH);
    }];
    
    //设置自定义导航栏
    self.navigationController.navigationBar.hidden = YES;
    self.lastOffsetY = - kHeardH+35;
    [self.view addSubview:self.tableView];
    self.tableView.backgroundColor = [UIColor clearColor];
    [self.view addSubview:self.navigationView];
    self.navigationController.navigationBar.barStyle = UIBarStyleBlack;
    
    
    // 直接添加到控制器的View上面,注意添加顺序,在添加导航栏之后,否则会被遮盖住
    [self configNavigationBar];
}

创建自定义导航视图
// 注意:毛玻璃效果API是iOS8的,适配iOS8以下的请用其他方法
-(void)setNavigationSubView{
    // 毛玻璃背景
    UIImageView *bgImgView = [[UIImageView alloc] initWithFrame:_navigationView.bounds];
    bgImgView.image = [UIImage imageNamed:@"DialBackground"];
    [_navigationView addSubview:bgImgView];
    /**  毛玻璃特效类型
     *   UIBlurEffectStyleExtraLight,
     *   UIBlurEffectStyleLight,
     *   UIBlurEffectStyleDark
     */
    UIBlurEffect * blurEffect = [UIBlurEffect effectWithStyle:UIBlurEffectStyleDark];    //  毛玻璃视图
    UIVisualEffectView * effectView = [[UIVisualEffectView alloc] initWithEffect:blurEffect];    //添加到要有毛玻璃特效的控件中
    effectView.frame = bgImgView.bounds;
    [bgImgView addSubview:effectView];    //设置模糊透明度
    effectView.alpha = 0.9f;    //中间文本框
    UIView *centerTextView = [[UIView alloc]init];
    self.centerTextView = centerTextView;
    CGFloat centerTextViewX = 0;
    CGFloat centerTextViewY = 20;
    CGFloat centerTextViewW = 0;
    CGFloat centerTextViewH = 0;    //文字大小
    NSString *title = @"FadeNavigationDemo";
    NSString *desc  = @"timtian_desc";
    CGSize titleSize = [title sizeWithAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:12]}];
    CGSize descSize = [desc sizeWithAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:11]}];
    centerTextViewW = titleSize.width > descSize.width ? titleSize.width : descSize.width;
    centerTextViewH = titleSize.height + descSize.height +10;
    centerTextViewX = (__kScreenWidth - centerTextViewW) / 2;
    centerTextView.frame = CGRectMake(centerTextViewX, centerTextViewY, centerTextViewW, centerTextViewH);    //文字label
    
    UILabel *titleLabel = [[UILabel alloc]init];
    titleLabel.text = title;
    titleLabel.font = [UIFont systemFontOfSize:12];
     titleLabel.textColor = [UIColor whiteColor];
    titleLabel.frame = CGRectMake(0,5, centerTextViewW, titleSize.height);    UILabel *descLabel = [[UILabel alloc]init];
    descLabel.textAlignment = NSTextAlignmentCenter;
    descLabel.text = desc;
    descLabel.font = [UIFont systemFontOfSize:11];
    descLabel.textColor = [UIColor whiteColor];
    descLabel.frame = CGRectMake(0, titleSize.height + 5, centerTextViewW, descSize.height);
    [centerTextView addSubview:titleLabel];
    [centerTextView addSubview:descLabel];
    [_navigationView addSubview:centerTextView];
}

滚动导航渐变代码
#pragma mark - UIScrollViewDelegate
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    // 计算当前偏移位置
    CGFloat offsetY = scrollView.contentOffset.y;
    CGFloat delta = offsetY - _lastOffsetY;
    NSLog(@"delta=%f",delta);
    NSLog(@"offsetY=%f",offsetY);
    CGFloat height = kHeardH - delta;
    if (height < kNavBarH) {
        height = kNavBarH;
    }
    
//    navigationTitile 位移相关代码
    CGFloat margin = 205;

    
    if (delta > margin && delta < margin+39) {
  
        self.centerTextView.y = 64 - (delta-margin);
        self.centerTextView.alpha = 1.0;
    }
    if (delta>margin+39) {

        self.centerTextView.y = 25;
        self.centerTextView.alpha = 1.0;
    }
    if (delta<=margin) {
        self.centerTextView.alpha = 0;
    }
    if (delta<= 0) {
        self.centerTextView.y = 64;
        self.centerTextView.alpha = 0.0;
    }
    [_scaleImageView mas_updateConstraints:^(MASConstraintMaker *make) {
        make.height.mas_equalTo(height);
    }];
    CGFloat alpha = delta / (kHeardH - kNavBarH);
    if (alpha >= 1.0) {
        alpha = 1.0;
    }
    self.navigationView.alpha = alpha;
}

