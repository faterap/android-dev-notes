# Objective-C load vs initialize

## +initialize

1. Called once per a class
2. Won’t be not called if you don’t invoke a class.
3. +initialize method could be invoked more than once.
   Example it will be called twice if a subclass don’t implement initialize
4. Is called when you first time call a class
5. Called in thread safe manner
6. Superclasses receive initialize message before their subclasses

## +load

1. Invoked whenever a class or category is added to the Objective-C runtime. When your app starts
2. Called even if you don’t invoke any methods of a class.
3. Invoked once per class and once per a class category, if +load is implemented in a category
4. Framework classes are loaded at this time
5. C++ static initializers are not loaded
6. Invoked before main method.
7. It’s safe to use autorelease object here because ARC create autorelease pool automatically.