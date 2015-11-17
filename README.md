# Objective-C编程规范

## 介绍
这是一份编程规范，主要参考了raywenderlich、github和苹果官方的Objective-C编程规范。相关文档链接在文末给出。


## 背景知识

这里是一些苹果官方描述oc编码规范的文档，如果有些规范这里没有提到，请参照这些文档：

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## 目录

* [语言](#语言)
* [代码组织](#代码组织)
* [对齐](#对齐)
* [注释](#注释)
* [命名](#命名)
* [函数](#函数method)
* [变量](#变量)
* [属性的修饰](#属性的修饰property-attributes)
* [.语法](#语法)
* [subscript和literals](#subscript和literals)
* [常量](#常量)
* [枚举类型](#枚举类型)
* [switch-case](#switch-case)
* [私有属性](#私有属性)
* [布尔值](#布尔值)
* [条件](#条件)
* [三元运算符](#三元运算符)
* [初始化方法](#初始化方法)
* [类构造方法](#类构造方法)
* [CGRect相关方法](#cgrect相关方法)
* [黄金路径](#黄金路径golden-path)
* [Error处理](#error处理)
* [单例模式](#单例模式)
* [换行](#换行)
* [Xcode工程](#xcode工程)
* [其他代码风格](#其他objective-c代码风格)

## 语言

代码应该使用英国英语。除了注释和字符串常量，不应该使用中文或拼音来命名标识符。

## 代码组织

使用 `#pragma mark -` 来对一个oc文件中的代码进行分组，一般使用如下的框架把重要的分组放在前面位置：

```objc
#pragma mark - Lifecycle

- (instancetype)init {}
- (void)dealloc {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Public

- (void)publicMethod {}

#pragma mark - Private

- (void)privateMethod {}

#pragma mark - Draw
- (void)drawRect:(CGRect)rect {}

#pragma mark - Events

- (IBAction)submitData:(id)sender {}
- (void)onBtnClicked:(id)sender {}

#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - Properties

- (void)setCustomProperty:(id)value {}
- (id)customProperty {}

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```

## 对齐

* 使用4个空格来进行代码的缩进。不要使用tab来缩进，因为它在不同的编辑器下可能显示不同的效果，可以在Xcode中设置一个tab自动转换为4个空格。
* 函数的定义中，左 ``{`` 必须跟函数名在同一行，右 ``}`` 必须使用单独的行。其他一些代码块 (`if`/`else`/`switch`/`while` etc.) 也一样。

**应该:**

```objc
if (user.isHappy) {
    //Do something
} else {
    //Do something else
}
```

**不应该:**

```objc
if (user.isHappy)
{
  //Do something
}
else 
{
  //Do something else
}
```
* 函数之间要有且只有一个空行。函数内部按业务功能用空行进行分隔。
* 应该使用自动synthesis，如果必须使用 `@synthesize` 和 `@dynamic`，它们应该放在`@implementation` 后的前几行。
* 每完成一个函数的定义，尽量使用Xcode的Re-indent的功能进行代码自动缩进。方法为: 选中整个函数，右键->Restruct->Re-Indent。

* **注：我们的代码风格基本上与Mozilla的风格一样，只是把2个字符对齐改成4个字节对齐。** 大家可以使用[ClangFormat](https://github.com/travisjeffery/ClangFormat-Xcode)这款Xcode插件进行代码风格统一。我们基于Mozilla主要进行如下调整:

```
---
Language:        Cpp
BasedOnStyle:  Mozilla
AccessModifierOffset: -2
DerivePointerAlignment: false
PointerAlignment: Right
ConstructorInitializerIndentWidth: 4
ContinuationIndentWidth: 4
IndentCaseLabels: false
IndentWidth:     4
ObjCBlockIndentWidth: 4
TabWidth:        4
UseTab:          Never

```

## 注释

* 代码应该尽量做到清晰可读，做到自解释，而不用加很多的注释。
* 当需要注释时，注释应该更多地用来描述 **为什么这样做**。
* 注释保持更新，历史的注释应该删除。
* 除了用来产生文档的一些注释(函数声明等)，其他注释应该使用行注释，不应该使用块注释。
* 可能存在问题的代码应该加上 ``// FIXME: `` 的注释，并详细描述(注意用半角的冒号)。
* 需要添加逻辑的代码应该加上 ``// TODO: ``，并详细描述。
* 不是源码文件创建者修改了代码，应该在修改的代码上加上 ``// Modified: ``，并写明修改人、修改时间、修改原因等信息。 

## 命名

必须尽量遵守苹果官方的命名规范，特别是跟[memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).相关的。

应该使用长的、描述清楚的函数名、变量名。

**应该:**

```objc
UIButton *settingsButton;
```

**不应该:**

```objc
UIButton *setBut;
```

应该使用三个字母的前缀来给类、常量进行命名。

常量应该使用驼峰命名法，并用给它加上相关类或模块的前缀。

**应该:**

```objc
static NSTimeInterval const RWTTutorialViewControllerNavigationFadeAnimationDuration = 0.3;
```

**不应该:**

```objc
static NSTimeInterval const fadetime = 1.7;
```

属性名应该也用驼峰命名法，第一个单词要小写。属性不要手动添加 ``@synthesize``，而是让Xcode自动synthesize。

**应该:**

```objc
@property (strong, nonatomic) NSString *descriptiveVariableName;
```

**不应该:**

```objc
id varnm;
```

### 属性的下划线

使用属性时，成员变量应该使用通过`self.` 进行访问。有个例外是，在初始化函数中，应该使用`_variableName` 的方式进行访问。

临时变量不应该加下划线。

## 函数(Method)

在函数签名中(函数声明或定义)，在函数类型(-/+符号)后，必须有一个空格。参数之间也要有一个空格。参数的keyword不要为空。除了第一个参数，后面的参数的keyword尽量不要加with、and等连词的前缀，keyword更不应该直接用with、and。

**应该:**

```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**不应该:**

```objc
-(void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

## 变量

变量名应该描述尽量清楚。除了`for()` 中，不应使用单个字母的变量。

除了常量的定义，变量定义中的`*` 号，应该紧挨变量，`*` 号前面加空格，如，`NSString *text` 而不是 `NSString* text` 或 `NSString * text`。

尽量使用[私有属性](#私有属性)来代替成员变量，因为它更可读。

不应该直接访问变量，而应该用属性去访问，除了在初始化函数(`init`, `initWithCoder:`, etc…)，`dealloc` 或 setter和getter中。

**应该:**

```objc
@interface RWTTutorial : NSObject

@property (strong, nonatomic) NSString *tutorialName;

@end
```

**不应该:**

```objc
@interface RWTTutorial : NSObject {
    NSString *tutorialName;
}
```


## 属性的修饰(Property attributes)

属性的修饰，必须显式地给出。顺序应该是weak/strong/copy后，再nonatomic，因为这与Interface Builder生成的属性是顺序是一致的。

**应该:**

```objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (strong, nonatomic) NSString *tutorialName;
```

**不应该:**

```objc
@property (nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic) NSString *tutorialName;
```
拥有可变版本的类型(如 NSString有NSMutableString，NSArray有NSMutableArray) 应该使用copy而不是用strong修饰。这样可以避免用户实际传入mutable版的变量（**注意，mutalbe类型的变量千万不能加copy修饰，因为这样这个变量copy后就是不可改变类型，但是却声明为可变类型，修改该变量往往会产生异常**）。

**应该:**

```objc
@property (copy, nonatomic) NSString *tutorialName;
```

**不应该:**

```objc
@property (strong, nonatomic) NSString *tutorialName;
@property (copy, nonatomic) NSMutableString *name; // 千万不能这样写
```

## .语法

objc2.0开始引入.语法，即可以使用`self.propertyName` 的方式来引用属性。虽然你也可以通过setter和getter函数来访问属性对应的变量，但是还是应该使用.语法。

**应该:**

```objc
view.backgroundColor = [UIColor orangeColor];
```

**不应该:**

```objc
[view setBackgroundColor:[UIColor orangeColor]];
```

## subscript和literals

`NSString`, `NSDictionary`, `NSArray`, 和 `NSNumber` 等应该尽量使用subscript和literals。如：

**应该:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingStreetNumber = @10018;
NSString *name = names[1];
```

**不应该:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingStreetNumber = [NSNumber numberWithInteger:10018];
NSString *name = [names objectAtIndex:1];
```

## 常量

常量应该尽量用`static` 语法来写，不应该使用`#define` 。

**应该:**

```objc
static NSString * const RWTAboutViewControllerCompanyName = @"RayWenderlich.com";

static CGFloat const RWTImageThumbnailHeight = 50.0;
```

**不应该:**

```objc
#define CompanyName @"RayWenderlich.com"

#define thumbnailHeight 2
```

## 枚举类型

应该使用新的``NS_ENUM`` 语法，而不应该使用旧的``enum`` 语法，因为新的语法有更强的类型检查和代码补全。

**For Example:**

```objc
typedef NS_ENUM(NSInteger, RWTLeftMenuTopItemType) {
    RWTLeftMenuTopItemMain,
    RWTLeftMenuTopItemShows,
    RWTLeftMenuTopItemSchedule
};
```

你仍然可以显式地给类型赋值:

```objc
typedef NS_ENUM(NSInteger, RWTGlobalConstants) {
    RWTPinSizeMin = 1,
    RWTPinSizeMax = 5,
    RWTPinCountMin = 100,
    RWTPinCountMax = 500,
};
```

不应该再使用的``enum`` 定义，除非你在写c语言。

**不应该:**

```objc
enum GlobalConstants {
    kMaxPinSize = 5,
    kMaxPinCount = 500,
};
```

## switch-case

switch-case中，如果一个case包含多行，加``{}`` 否则不加。

```objc
switch (condition) {
case 1:
    // ...
    break;
case 2: {
    // ...
    // Multi-line example using braces
    break;
}
case 3:
    // ...
    break;
default: 
    // ...
    break;
}

```
如果分支没有break的话，要加一行fall-through的注释，表明下面的code是通用的。

```objc
switch (condition) {
case 1:
    // fall-through!
case 2:
    // code executed for values 1 and 2
    break;
default: 
    // ...
    break;
}
```

## 私有属性

私有属性应该放在.m文件中。私有属性放在匿名的categories中，如下:

**For Example:**

```objc
@interface RWTDetailViewController ()

@property (strong, nonatomic) GADBannerView *googleAdView;
@property (strong, nonatomic) ADBannerView *iAdView;
@property (strong, nonatomic) UIWebView *adXWebView;

@end
```

## 布尔值

Objective-C使用`YES` 和 `NO`来表示布尔值。所以`true` 和 `false` 除了CoreFoundation和C、C++代码中，不应该被使用。因为`nil` 被判断为`NO`，所以没有必要与`nil` 进行比较。因为BOOL值可以最大可以达到8位，而YES只是被设置为1，所以尽量不要跟`YES` 比较。

**应该:**

```objc
if (someObject) {}
if (![anotherObject boolValue]) {}
```

**不应该:**

```objc
if (someObject == nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
```

如果布尔类型的属性名是一个形容词，那么属性虽然可以忽略`is` 前缀，但最好仍提供带`is` 前缀的getter方法，方便调用:

```objc
@property (assign, getter=isEditable) BOOL editable;
```

## 条件

条件的body应该使用括号，即使body代码只有一行。

**应该:**

```objc
if (!error) {
    return success;
}
```

**不应该:**

```objc
if (!error)
    return success;
```

或

```objc
if (!error) return success;
```

### 三元运算符

三元运算符, `?:` , 应该只在它能提高代码整洁性而且逻辑清晰的情况下使用。判断条件应该只有一个，多于一个的判断条件应该用if或临时变量来写。

**应该:**

```objc
NSInteger value = 5;
result = (value != 0) ? x : y;

BOOL isHorizontal = YES;
result = isHorizontal ? x : y;
```

**不应该:**

```objc
result = a > b ? x = c > d ? c : d : y;
```

## 初始化方法

初始化方法应该跟苹果的样例一样。格式如下：

```objc
- (instancetype)init {
    self = [super init];
    if (self) {
        // ...
    }
    return self;
}
```

返回值应该使用`instancetype` 来代替之前的`id`。

## 类构造方法

类构造方法与上面初始化方法类似，返回值应该也使用`instancetype` 而不是`id`。

```objc
@interface Airplane
+ (instancetype)airplaneWithType:(RWTAirplaneType)type;
@end
```

## CGRect相关方法

当访问 `CGRect` 中的`x`, `y`, `width`, 和 `height`时，应该使用
[`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) 来代替直接的结构体访问。

**应该:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = CGRectMake(0.0, 0.0, width, height);
```

**不应该:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
```

## 黄金路径(Golden Path)

当if判断有多层嵌套，应该尽量使用`黄金路径`，即不符合条件的分支先返回，这样可以避免分支嵌套。

**应该:**

```objc
- (void)someMethod {
    if (!condition1) {
        return;
    }
    //Do something1
    if (!condition2) {
        return;
    }
    // Do something 2
}
```

**不应该:**

```objc
- (void)someMethod {
    if (condition1) {
        //Do something1
        if (condition2) {
            // Do something 2
        }
    }
}
```

## Error处理

当函数返回NSError的引用，调用时要根据返回值进行判断，而不是NSError的值，因为苹果的一些api会往NSError中写一些垃圾数据，如果根据NSError进行判断可能会有问题。

**应该:**

```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
  // Handle Error
}
```

**不应该:**

```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
  // Handle Error
}
```

## 单例模式

单例模式应该使用dispatch_once来保证创建时的线程安全。

```objc
+ (instancetype)sharedInstance {
  static id sharedInstance = nil;

  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
    sharedInstance = [[self alloc] init];
  });

  return sharedInstance;
}
```

## 换行

换行是比较重要的，它直接影响到程序的可读性和美观。

For example:

```objc
self.productsRequest = [[SKProductsRequest alloc] initWithProductIdentifiers:productIdentifiers];
```

这种很长的代码，要做分行。后面几行，用4个字符进行对齐。

```objc
self.productsRequest = [[SKProductsRequest alloc] 
    initWithProductIdentifiers:productIdentifiers];
```

## Xcode工程

物理文件应该跟Xcode工程中的文件同步。Xcode工程中的分组应该对应到文件系统的物理目录。
尽可能在target的编译设置里面把"Treat Warnings as Errors"打开，而且打开更多的[错误警告](http://boredzo.org/blog/archives/2009-11-07/warnings)。如果你确定没有问题，可以使用pragma的ignore来忽略一些警告，[Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas)。

# 其他Objective-C代码风格

如果这里的代码风格不适合你，你可以尝试其他的一些编码风格

* [Raywenderlich](https://github.com/raywenderlich/objective-c-style-guide)
* [Robots & Pencils](https://github.com/RobotsAndPencils/objective-c-style-guide)
* [New York Times](https://github.com/NYTimes/objective-c-style-guide)
* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
