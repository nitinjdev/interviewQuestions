# js question 2: what is closures

A closures is when a function remember & can access variable from its outer scope , even after the outer  function has finished executing

benefits: 
1\. memory efficient :  As its does not create variable everytime, its hold it refernces

2\. encapsulation : we can create private varibles


# call vs apply vs bind

call > allow you to invoke a function while explicitly setting the value of this and passing args individuals

functionName.call(this,arg1, arg2,args3)

apply > same as call but only pass array

functionName.call(this,[agrs1, agrs2, ]

bind > method that create and return a new function with a specified this 

functionName.bind(this, arg1, arg2, arg3)