Suddenly your web application has become very slow on clicking particular URL. How would you debug it and solve the problem?


1 If the page requires database queries I would look at the performance of those queries individually.
  数据库的相应时间应该是ms级别
  
2 If the page requests data from a particular service I would look at the performance of those service requests.

3 I would try to determine whether the delay is on the server side or on the client side (if the queries and services respond quickly
  but the page loads slowly it might be a slowness in the javascript or other page level elements).
