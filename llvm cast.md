```
官方文档
https://llvm.org/docs/ProgrammersManual.html#the-isa-cast-and-dyn-cast-templates
```


```
template<class To, class FromTy> struct cast_convert_val<To,FromTy,FromTy> {
  // This _is_ a simple type, just cast it.
  static typename cast_retty<To, FromTy>::ret_type doit(const FromTy &Val) {
    typename cast_retty<To, FromTy>::ret_type Res2
     = (typename cast_retty<To, FromTy>::ret_type)const_cast<FromTy&>(Val);
    return Res2;
  }
};


template <class X> struct is_simple_type {
  static const bool value =
      std::is_same<X, typename simplify_type<X>::SimpleType>::value;
};

// cast<X> - Return the argument parameter cast to the specified type.  This
// casting operator asserts that the type is correct, so it does not return null
// on failure.  It does not allow a null argument (use cast_or_null for that).
// It is typically used like this:
//
//  cast<Instruction>(myVal)->getParent()
//
template <class X, class Y>
inline std::enable_if_t<!is_simple_type<Y>::value,
                        typename cast_retty<X, const Y>::ret_type>
cast(const Y &Val) {
  assert(isa<X>(Val) && "cast<Ty>() argument of incompatible type!");
  return cast_convert_val<
      X, const Y, typename simplify_type<const Y>::SimpleType>::doit(Val);
}
```


isa<ClassType>(ClassPointer) 判断Pointer是否是指向ClassType,是的话返回1,否的返回0
  ```
  static bool isLoopInvariant(const Value *V, const Loop *L) {
  if (isa<Constant>(V) || isa<Argument>(V) || isa<GlobalValue>(V))//这三个类都是继承于Value的
    return true;

  // Otherwise, it must be an instruction...
  return !L->contains(cast<Instruction>(V)->getParent());//llvm的cast不需要rtti（通过classof的方法实现，具体代码我也没看过），作用应该也是将父类指针转化为子类
}
  ```


