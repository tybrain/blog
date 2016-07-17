---
layout: post
title: "The chrono library"
category: 
date: 2016-07-17 08:42:34 EDT
---

For the convenience of measuring code performance and handling date/time types. *chrono* was introduced in c++11. It mainly concerns with two kinds objects: time durations and time points

### Basic 

```c++
#include <chrono>
#include <iostream>
using namespace std::chrono;
int main() {
    // durations 
    nanoseconds ns(5);
    microseconds mcs(6);
    milliseconds mls(7);
    seconds sec(6);
    minutes minu(8);
    hours hr(9);
    
    auto total = hr + minu + sec + mls + mcs + ns;
    std::cout << nanoseconds(total).count() << "ns" <<"\n"; // 32886007006005ns
    auto mins = duration_cast<minutes> (hr);
    std::cout << mins.count() << " mins\n";   // 540 mins

    // time points
    auto tp = system_clock::now();
    tp = hours(24) + minutes(40) + seconds(20);
}
```

### High resolution clock

```c++
auto t1 = high_resolution_clock::now();
auto t_h = high_resolution_clock::now() - t1;
auto t_l = system_clock::now() - t1;
```

### Useful functions

Convert time point to string

```c++
inline
std::string toString( system_clock::time_point const& tp) {
	std::time_t t = system_clock::to_time_t(tp);
    auto time_str = ctime(&t);
    time_str.resize(time_str.size()-1);
    return time_str;
}

auto tp = system_clock::now();
std::cout << toString(tp) << "\n";  // Mon Mar 25 12:45:20 2015
```
Make a time point from parameters

```c++
inline
auto makeTimePoint( int year,
                    int month,
                    int day,
                    int hour,
                    int minute,
                    int second = 0,
                    int daylight_saving = -1 ) {
    struct std::tm t = {
        second,
        minute,
        hour,
        day,
        month-1,
        year-1900,
        daylight_saving   // whether daylight saving time
    };
    std::time_t tt = std::mktime(&t);
    if( -1 == tt ) {
        throw "not a valid time format";
    }
    return system_clock::from_time_t(tt);
}

auto tp1 = makeTimePoint( 2015, 03, 23, 13, 34);
auto tp2 = makeTimePoint( 2015, 03, 24, 13, 34);
auto dt = duration_cast<hours> (tp2 - tp1);
std::cout << dt.count() << " hr \n";  // 24 hr
std::cout << toString(tp3) << " \n";  // Tue Mar 24 14:34:00 2015

``` 