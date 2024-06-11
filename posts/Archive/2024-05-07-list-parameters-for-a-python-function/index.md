---
title: "List Parameters for a Python Function"
date: "2024-05-07"
categories: 
  - "python"
  - "syndication"
---

As someone who is newer to the world of Python I have a lot of questions. I mean a LOT. I know my way around SQL really well, but when I come to write some PySpark code I often need to dissect things to better understand what is going on and how to use different functions.

Which leads me to today's question...how do I get a list of all the parameters for a Python function? In today's case: mssparkutils.credentials.getSecret.

You could go to the documentation, but that's often not complete (I'm looking at you, my fellow Microsoft employees on the documentation team!). Usually, I just want to know what my possibilities are so I can at least throw some options in there to see what happens. Maybe I'll get lucky. That's where inspect.signature comes into play.

## help

The first thing I could try is using Python's built in "help" method. By simply passing a module, method, function, keyword, or other object the help information from PyDoc.

Python

```
help(mssparkutils.credentials.getSecret)
```

![](https://images.bradleyschacht.com/wp-content/uploads/2024/05/image.png)

Easy enough. What if I want a little more detail?

## inspect

The inspect module can help get some useful information for us in this case as well. Using my new friend help, I can see this will give me a look at useful information from Python objects. Here we will look at two functions: inspect.signature() and inspect.getfullargspec().

![](https://images.bradleyschacht.com/wp-content/uploads/2024/05/image-1.png)

To begin, we will import the inspect module.

### inspect.signature()

As you can see, running inspect.signature(msparkutils.credentials.getSecret) returns what I need in a simple output which is very similar to what we say with help(mssparkutils.credentials.getSecret) earlier. It gives me the list of inputs which are akvName, secret, and linkedService which is optional as it has a default value assigned to it.

![](https://images.bradleyschacht.com/wp-content/uploads/2024/05/image-2.png)

But I am a simple person. I need things spelled out a bit more explicitly sometimes.

### inspect.getfullargspec()

When I run inspect.getfullargspec(mssparkutils.credentials.getSecret) I am given a much more detailed breakdown of the output. While help and signature() will usually get the job done, it is nice knowing that I can get a full breakdown of the inputs and outputs.

![](https://images.bradleyschacht.com/wp-content/uploads/2024/05/image-3.png)

I am sure there are reasons to use signature over getfullargspec and reasons to use getfullargspec over signature. I saw something about a decorated function being handled properly by signature but not getfullargspec. Since the only possible time I can think of to use a decorator function is when I'm putting up Christmas decor and it is currently May, I think that is a deep dive topic for future Brad.

![](https://images.bradleyschacht.com/wp-content/uploads/2024/05/image-4.png)
