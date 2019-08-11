### Algorito
```
/*
 * @lc app=leetcode id=11 lang=java
 *
 * [11] Container With Most Water
 */
class Solution {
    public int maxArea(int[] height) {
        //动态规划
        int length = height.length;
        if(length <= 1){
            return 0;
        }
        int maxArea = 0;
        int left = 0;å
        int right = length - 1;
        while(left < right){
            //计算宽高
            int width = right - left;
            int h = height[left] > height[right]?height[right]:height[left];
            int tmpArea = width * h;
            maxArea = maxArea > tmpArea ? maxArea : tmpArea;

            if (height[left] < height[right]){
                left++;
            } else {
                right--;
            }
        }
        
        return maxArea;
    }
}


```

### Review


reflect学习
[https://medium.com/capital-one-tech/learning-to-use-go-reflection-822a0aed74b7](https://medium.com/capital-one-tech/learning-to-use-go-reflection-822a0aed74b7)
1、三个概念
Types, Kinds, and Values
If you define a struct named Foo, the kind is struct and the type is Foo.
If your variable is a pointer, map, slice, channel, or array, you can find out the contained type by using varType.Elem().
StructField struct
If you want to modify a value, remember it has to be a pointer, and you have to dereference the pointer first. 
reflect.MakeSlice, reflect.MakeMap, and reflect.MakeChan functions. 
golang 
struct to map  map to struct
Making a New Instance
利用反射初始化实例
只读：refVal := reflect.ValueOf(var)
可修改：refPtrVal := reflect.ValueOf(&var);
refPtrVal.Elem().Set(newRefVal)
new by type
newPtrVal := reflect.New(varType)
不通过make创建实例
slice:intSliceReflect := reflect.MakeSlice(sliceType, 0, 0)
intSliceReflect = reflect.Append(intSliceReflect, rv)
mapReflect := reflect.MakeMap(mapType)
创建函数
```
func MakeTimedFunction(f interface{}) interface{} {
    rf := reflect.TypeOf(f)
    if rf.Kind() != reflect.Func {
        panic("expects a function")
    }
    vf := reflect.ValueOf(f)
    wrapperF := reflect.MakeFunc(rf, func(in []reflect.Value) []reflect.Value {
        start := time.Now()
        out := vf.Call(in)
        end := time.Now()
        fmt.Printf("calling %s took %v\n", runtime.FuncForPC(vf.Pointer()).Name(), end.Sub(start))
        return out
    })
    return wrapperF.Interface()
}
```
make struct

```
func MakeStruct(vals ...interface{}) interface{} {
    var sfs []reflect.StructField
    for k, v := range vals {
        t := reflect.TypeOf(v)
        sf := reflect.StructField{
            Name: fmt.Sprintf("F%d", (k + 1)),
            Type: t,
        }
        sfs = append(sfs, sf)
    }
    st := reflect.StructOf(sfs)
    so := reflect.New(st)
    return so.Interface()
}
```
不能再运行时创建方法
使用场景：
1、处理json

### Share

反射是一种检测结构特别是类型的能力，属于元编程的一种。
go是静态类型。每个变量都有个静态类型，类型在编译阶段就确定了。
一个重要的类型是interface（代表方法的集合）。一个interface的变量可以保存任务非接口的变量，只要变量实现了接口的方法。
var r io.Readerr = os.Stdinr = bufio.NewReader(r)r = new(bytes.Buffer)// and so on
所有的反射都和接口紧密关联。
接口的定义
一个接口变量存有两个值：具体的值和它的类型描述。
r.(io.writer)
因为记录了类型信息，所以可以通过这种方法断言。接口只判断什么方法可以被调用，但是变量可能包含更多的方法可以调用
接口不包含接口的值。
1、反射是把接口的值转换为反射对象
type和value
当获取反射的时候回先把存储在一个空的interface，然后获取type信息。这两种包含很多方法用于操作和判断
kind是底层类型，type是静态类型
2、反射是从反射类值到接口值
3、利用反射修改变量值，这个值必须是可写的
是否可以设置类型是否可以找到地址
