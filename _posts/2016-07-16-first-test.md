---
layout: post
title: "First test"
description: ""
category: 
tags: []
---

## C++ Design: proxy pattern

The proxy design pattern mainly concerns with the situation when the creation of objects is expensive. Instead of making all objects ready at the initializing step, one can use a proxy interface to store the parameters first and call the expensive execution only when it is absolutely needed.

Let us look at this example.

### None proxy approach

```c++
#include <iostream>
using namespace std;
class Render {
    int effect_id;
    static int next_effect;
  public:
    Render()  {
        effect_id = next_effect++;
        someExpensiveCalculation();
    }
    void someExpensiveCalculation() {
        cout << "It takes a hack amount of time to prepare effect " << effect_id << "...done \n";
    }
    void applyEffect()  {
        cout << "Rendering with effect " << effect_id << '\n';
    }
};
int Render::next_effect = 1;

int main() {
    Render renders[4];
    int i = 0;
    std::string s = "Please select effect [1-4] to apply (0 to exit):";
    cout << s;
    while( cin >> i ) {
        if( 0 == i) break;
        if( i-1 < 4 ) {
            renders[i-1].applyEffect();
        }
        cout << "\n" << s;
    }
}
```

The result is it takes forever to prepare all the effects that we want to apply on demand later. Some of the effects may never get used.

```
It takes a hack amount of time to prepare effect 1...done 
It takes a hack amount of time to prepare effect 2...done 
It takes a hack amount of time to prepare effect 3...done 
It takes a hack amount of time to prepare effect 4...done 
Please select effect [1-4] to apply (0 to exit):2
Rendering with effect 2
```

### Proxy design

The idea is to introduce a proxy class to interface the actual class. Only parameters are taken at creation. No calculation will be done until it is absolutely necessary.

```c++
#include <iostream>
#include <memory>
using namespace std;
class ActualRender {
    int effect_id;
  public:
    ActualRender(int id) : effect_id(id) {
        someExpensiveCalculation();
    }
    void someExpensiveCalculation() {
        cout << "It takes a hack amount of time to prepare effect " << effect_id << "...done \n";
    }
    void applyEffect()  {
        cout << "Rendering with effect " << effect_id << '\n';
    }
};

// class Render is only a proxy
class Render {
    std::unique_ptr<ActualRender> render;
    static int next_effect;
    int effect_id;
  public:
    Render() : render(nullptr), effect_id(next_effect++) { }
    void applyEffect() {
        if( !render ) {
            render = std::make_unique<ActualRender> (effect_id);
        }
        render->applyEffect();
    }
};
int Render::next_effect = 1;
```

Now let us see how it turns out.

```
Please select effect [1-4] to apply (0 to exit):1
It takes a hack amount of time to prepare effect 1...done 
Rendering with effect 1
Please select effect [1-4] to apply (0 to exit):1
Rendering with effect 1
```

It is truely calculated on demand!
