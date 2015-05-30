**Important note: This concept are applicable to new version of GWTPHP 1.0 which are still under development and which are not accessible for now.**



![http://gwtphp.googlecode.com/files/gwtphp-basic-flow-and-components-diagram.png](http://gwtphp.googlecode.com/files/gwtphp-basic-flow-and-components-diagram.png)


Image 1. describes basic concept of GWTPHP work flow . All of steps described below are fully automatic. So don't worry about complexity, you need to know only basic of concept to better understand what is going on under the hood.

## Two phases of writing application ##

Writing application with GWTPHP have two phases, development (steps from 1 to 3) and processing (steps from 4 to 8)

### Phase of development ###
**Step 1.** You need to write your code for GWT client side application. Here it is very important to write Java interfaces for your services. This is standard part of making remote procedure call in GWT. Service interface have to extends com.google.gwt.user.client.rpc.RemoteService (see GWT tutorial “ Making Remote Procedure Calls”)

**Step 2.** Use GWTPHP MapGenerator to generate MetaData which are needed to properly desarialize GWTRPC calls and to serialize server response. You can use GWTPHP MapGenerator as CLI (Command Line Interpreter) or ANT Task(recommended).


**Step 3.** GWTPHP MapGenerator not only generate MetaData but also translate interfaces written in Step 1 to PHP. This interfaces you need to import and implement in your PHP server side application. As you can see you don't need to manually rewrite Java services interfaces to PHP. You can even ask GWTPHP Map Generator to generate PHP implementation of your Java services interfaces. This will be of course dummy null returned implementations but useful in migration big projects.

End of development phase.
You need to repeat Step 2 and 3 after any changes of Java services interfaces source code.

### Phase of processing ###
Let's just sit down and watch  :):

When GWT is running and GWTRPC call was triggered, any serialized data should go to GWTRPC-Core module. This module are responsible for deserialize and serialize any RPC calls / responses. When GWTPHP receive a GWT call, then will (step 5) deserialize it using MetaData generated in Step 2. Using MetaData it will also call (Step 6.) suitable PHP service implementation with deserialized data. It is up to you what this implementation will return (Step 7). Regardless of what you return it must be acceptable by definition of interface (you cannot return Author object if Book object is expected, you can return Child object if Parent object is expected when Child extends Parent). After you return, response are serialized (Step 8.) and returned to GWT client side application. This step ends process of GWT remote procedure call.

Now you should understand most important thing in process of use described before:
Use GWTPHP Map Generator after any change of your service interface code (GWT Side)

And some tips for newbie PHP-Developer:
  * Return only what you defined in your interface classes.
  * Use inheritance freely
  * Don't afraid of to return objects. Many developers constantly returns array of primitive types and parse it on client side. Don't do this, it is error prone. Actually it is better to return even dummy single-parameter objects, then array's or primitive types, because PHP can validate this in “compile time”.
  * Don't afraid to returns complicated object graph's or self referenced objects. It will work :)
  * Use exceptions. It worth.