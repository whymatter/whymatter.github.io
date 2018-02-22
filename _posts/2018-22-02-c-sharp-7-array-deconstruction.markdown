---
layout: post
title:  "C# 7 array deconstruction"
date:   2018-02-22 11:54:00PM
categories: c#7 c# programming
---

Some of you might know that python has much semantic sugar implemented by default. One thing I want to focus on now it array deconstruction. I show you what I mean, just to make sure that we talk about the same thing.

{% highlight python %}
my_array = ['foo', 'bar']
el0, el1 = my_array
el0 # foo
el1 # bar
{% endhighlight %}

What happens if we try the same thing in C# 7?

{% highlight c# %}
var myArray = new [] {"foo", "bar"};
(var el0, var el1) = myArray; /* Error */
{% endhighlight %}

This throws an error when compiling, but the error was interesting for me, it said:

![c#7 error]({{ "/assets/images/c-sharp-7-array-deconstruction-c-sharp-error.png" | absolute_url }})

*No suitable Deconstruct instance or extension method was found for type 'string[]', with 2 out parameters and a void return type.*

It says that there is a `Deconstruct` method missing... so let´s try to implement one, maybe as extension method.

{% highlight c# %}
public static class ArrayExtensions
{
    public static void Deconstruct(
            this string[] array, out string el0, out string el1)
    {
        el0 = array[0];
        el1 = array[1];
    }
}
{% endhighlight %}

Now, with this extension method let´s try it again!

![c#7 success]({{ "/assets/images/c-sharp-7-array-deconstruction-c-sharp-success.png" | absolute_url }})

OMG, this acutally works!
Let's try to make this a bit more generic:

{% highlight c# %}
public static class ArrayExtensions
{
    public static void Deconstruct<T>(this T[] array, out T el0, out T el1)
    {
        el0 = array.ElementAtOrDefault(0);
        el1 = array.ElementAtOrDefault(1);
    }
}

// usage
var myArray = new [] {
        new Baz { Name = "John" },
        new Baz { Name = "Walter" }
    };

(var john, var walter) = myArray;
{% endhighlight %}

And it turns out this works! Amazing, we can do array deconstructing in C# 7 with the efford of some simple extension methods!

## Conclusion

As we have seen with a bit of extra efford you can do array deconstrution in C# 7. I am really wondering why those extensions methods do not exist by default? But we could easily generate them using a simple T4 template.

Thank you for reading!