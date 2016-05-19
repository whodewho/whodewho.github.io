---
layout: post
title:  "人生若只如二分"
category: programming
tags: [algorithm, interview]
---

越界吧少年人

求一个浮点数的立方根，面试写过最有意思的二分，考虑正负、精度、与1的关系。Test it [here][1]。

{% highlight c++ %}
#include <iostream>
#include <stdlib.h>
#include <cmath>
#include <assert.h>

using namespace std;

double epsilon=0.00001;
   
double getCubeRoot(double num, double epsilon)
{
    if(num==0)return num;
    
    int sign=1;
    if(num<0)
    {
      sign=-1;
      num=-num;
    }
    
    double l,h;
    if(num<=1)
    {
      l=num;
      h=1;
    }
    else
    {
      l=0;
      h=num/2+1;
    }

    while(abs(h-l)>=epsilon)
    {
      double m=l+(h-l)/2;
      double tmp=m*m*m;
      //cout<<l<<" "<<h<<" "<<m<<" "<<tmp<<" "<<num<<endl;
      if(abs(tmp-num)<epsilon)
      {
        return sign*m;
      }
      else if(tmp>num)
      {
        h=m;
      }
      else
      {
        l=m;
      }
    }
    return sign*min(l,h);
}

void testgetCubeRoot_SpecialNumber()
{
    assert(abs(getCubeRoot(0,epsilon)-cbrt(0))<epsilon);
    assert(abs(getCubeRoot(1,epsilon)-cbrt(1))<epsilon);
    assert(abs(getCubeRoot(-1,epsilon)-cbrt(-1))<epsilon);
}

void testgetCubeRoot_PositiveGreaterThanOne()
{
    assert(abs(getCubeRoot(1.4567,epsilon)-cbrt(1.4567))<epsilon);
    assert(abs(getCubeRoot(2.6443,epsilon)-cbrt(2.6443))<epsilon);
    assert(abs(getCubeRoot(8,epsilon)-cbrt(8))<epsilon);
    assert(abs(getCubeRoot(12367.1234,epsilon)-cbrt(12367.1234))<epsilon);
}

void testgetCubeRoot_NegativeSmallerThanNegativeOne()
{
    assert(getCubeRoot(-1.4567,epsilon)-cbrt(-1.4567)<epsilon);
    assert(getCubeRoot(-2.6443,epsilon)-cbrt(-2.6443)<epsilon);
    assert(getCubeRoot(-8,epsilon)-cbrt(-8)<epsilon);
    assert(abs(getCubeRoot(-12367.1234,epsilon)-cbrt(-12367.1234))<epsilon);
}

void testgetCubeRoot_PositiveSmallerThanOne()
{
    assert(abs(getCubeRoot(0.4567,epsilon)-cbrt(0.4567))<epsilon);
    assert(abs(getCubeRoot(0.6443,epsilon)-cbrt(0.6443))<epsilon);
    assert(abs(getCubeRoot(0.1,epsilon)-cbrt(0.1))<epsilon);
}

void testgetCubeRoot_NegativeGreaterThanNegativeOne()
{
    assert(abs(getCubeRoot(-0.4567,epsilon)-cbrt(-0.4567))<epsilon);
    assert(abs(getCubeRoot(-0.6443,epsilon)-cbrt(-0.6443))<epsilon);
    assert(abs(getCubeRoot(-0.1,epsilon)-cbrt(-0.1))<epsilon);
}

int main()
{
    testgetCubeRoot_SpecialNumber();
    testgetCubeRoot_PositiveGreaterThanOne();
    testgetCubeRoot_NegativeSmallerThanNegativeOne();
    testgetCubeRoot_PositiveSmallerThanOne();
    testgetCubeRoot_NegativeGreaterThanNegativeOne();
    return 0;
}
{% endhighlight %}


[1]: http://www.compileonline.com/compile_cpp_online.php

