# Objective-C 属性

### 1. copy

- use it for creating a shallow copy of the object
- good practice to always set immutable properties to copy - because mutable versions can be passed into immutable properties, copy will ensure that you'll always be dealing with an immutable object
- if an immutable object is passed in, it will retain it - if a mutable object is passed in, it will copy it

### 2. assign

- use *assign* for primatives - exactly like weak except it doesn't nil out the object when released (set by default)

`assign` is the keyword used for primitives. It is pretty easy to understand: If you do not have an object, you cannot use `strong`, because strong tells the compiler how to work with pointers. But if you have a primitive (i.e. int, float, bool, or something without the little asterisk after the type), then you use `assign`, and that makes it work with primitives.

### 3. strong(same as retain)

- use *strong* to retain objects - although the keyword retain is synonymous, it's best to use strong instead

The default is called `strong`. Strong just means you have a reference to an object and you will keep that object alive. As long as you hold that reference to the object in that property, that object will not be deallocated and released back into memory.

### 4. weak(almost like `assign`, but automatically set to nil after the object, it is pointing to, is deallocated.)

- use *weak* if you only want a pointer to the object without retaining it - useful for avoid retain cycles (ie. delegates) - it will automatically nil out the pointer when the object is released

Strong’s analog is `weak`. Weak gives you a reference so you can still “talk” to that object, but you are not keeping it alive. If everyone else’s strong references go away, then that object is released. The nice thing about weak references is that they will automatically nil references that go away. If you have a reference to an object and all of the `strong` references to it go away and it gets deallocated, your weak reference does not point to some junk memory, which can give you crashes. It will just automatically nil itself out.

### 5. retain(same as strong)

*increases the retain count of an object by 1.* Takes ownership of an object.