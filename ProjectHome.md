# GWTPHP Introduction #

GWTPHP is a PHP port for GWT Server (RemoteServiceServlet?). With GWTPHP you can connect GWT client application with PHP server application using native GWT-RPC protocol to communicate.

GWTPHP decode/encode GWT request/response using GWT native data interchange format.

No need of JSON or XMLRPC.

### Why you should use GWTPHP for your PHP application? ###

You are here  so you probably know that GWT is the best choice for  your web application, so I won't say a word about why you should use GWT. If you are still not convinced ask google :)

### So why use GWTPHP? ###
1. GWTPHP use native, developed by GWT-Team protocol to communication with server side of application.

2. GWTPHP is 100% compatible with GWT Server Side – it means that if you develop some GWT application with PHP as server you will be able to switch server to Java without any changes in source code of your GWT client side application. You won't need to rewrite your GUI.

3. GWTPHP is faster then any other typical solution for GWT to PHP communication like JSON or XMLRPC. Why? Native GWTRPC communication use binary protocol which are very good designed and which have very good compression level. See advantages of native GWTRPC protocol.

4. Using GWTPHP you are able (which is obvious) to implement all or only some part of your server side application in PHP. We all know that PHP is sometimes better choice then Java, so if you are able to switch from Java to PHP you can radically reduce your costs of developing. :)

### Why native GWT RPC protocol is better then JSON / XMLRPC? ###
1. You don't need to use short parameters/variables names. If you are sending data using JSON/XMLRPC protocol, you can notice that serialized data contains names of variables. For better performance you will probably use short names which are not human readable. GWTRPC do not serialize parameter names, so you can freely use human readable names of class variables.

2. Designed natively for serialization of objects.

3. Support for enums. (in next version of GWTPHP)

4. Support for inheritance

5. Support for Manual / Custom serialization support. For better performance in some cases (large data structures) you can implement your own strategy for serialization/deserialization.

6. Support for serialization of object graph or self referenced objects, there are no redundancy in serialized data.

7. Support for exceptions. If your service method is able to throw exception and will do it, this exception will be nicely serialized and sent to GWT Client side application as an exception.



![http://gwtphp.googlecode.com/files/sflogo.png](http://gwtphp.googlecode.com/files/sflogo.png)