# MCPE1.16.50_AddressList
I will be updating the addresses that have been parsed in version 1.16.50 of MCPE on an irregular basis

## How to hook a function

### Part 1:

```
static void (*_Bruh$doSomething)(uintptr_t*);
static void Bruh$doSomething(uintptr_t* self) {

  return _Bruh$doSomething(self);
}

%ctor {
  //0x000000000 is the address of the function you want to hook
  MSHookFunction((void*)(0x000000000 + _dyld_get_image_vmaddr_slide(0)), (void*)&Bruh$doSomething, (void**)&_Bruh$doSomething);
}
```

### Part 2:
```
static uintptr_t** VTBruh;

static void (*_Bruh$doSomething)(uintptr_t*);
static void Bruh$doSomething(uintptr_t* self) {

  return _Bruh$doSomething(self);
}

%ctor {
  //Put the first address of the virtual table in 0x000000000
  VTBruh = (uintptr_t**)(0x000000000 + _dyld_get_image_vmaddr_slide(0));
  //The number of array elements should be the number of rows counted by the line counter, plus one
  _Bruh$doSomething = (void(*)(uintptr_t*)) VTBruh[1];
  
  VTBruh[1] = (uintptr_t*)&Bruh$doSomething;
}
```

### Part3(This is not a hook. It's just a call to a function):
```
static void(*Bruh$doSomething)(uintptr_t*);

%ctor {
  //0x000000000 is the address of the function you want to call
  Bruh$doSomething = (void(*)(uintptr_t*))(0x000000000 + _dyld_get_image_vmaddr_slide(0));
}
```
