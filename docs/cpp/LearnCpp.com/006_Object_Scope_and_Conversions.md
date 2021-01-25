# Object Scope and Conversions

Code blocks work just like in vex. A **compound statement** is when you have multiple statements seperated by ; after an if statement. This is also called a **block**. You should keep the nesting level of your functions to 3 or less. If your function has a need for more, consider abstracting that to anther function. 

In cpp you manually defined **user-defined namespaces** - these allow you to have duplicate named functions that are still organized properly. It's very simple to set them up:
```cpp
namespace foo \\defnie a namecpace named foo
{
    int doSomething(int x, int y){
        return x + y;
    }
}
```

Now you call doSomething like this

```cpp
int dosomething(intx, int y); //forward declaration

int main(){
    std::cout << foo::doSomething(4,3) << '\n';
    return 0
}
```


