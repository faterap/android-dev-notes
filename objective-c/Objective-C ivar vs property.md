# Objective-C ivar vs property

### 1. 可见性

如果想要定义私有(private)变量, 可以考虑使用iVar; 定义公开(public)变量,则使用@property;

iVar虽然可以用 `@private`, `@protected` 和 `@public` 修饰, 但只会对影响到子类的可见性.也就是说,即使你用 `@public`修饰iVar, 其它类也是无法访问到该变量的.

### 2. 属性(attributes)

@property 可以使用strong, weak, nonatomic, readonly 等属性进行修饰.

iVar默认都是strong.

### 3. 书写习惯

通常, iVar名称使用下划线开头, 如 _view, _height, _width.
 但这并非强制要求.

### 4. getter/setter

编译器自动为@property生成访问器(getter/setter).

### 5. 效率

iVar 运行效率更高.

### 6. 结论

如果只是在类的内部访问, 既不需要weak、retain等修饰词,也不需要编译器自动生成getter/setter方法, 则使用 variable就可以.否则就使用 @property.