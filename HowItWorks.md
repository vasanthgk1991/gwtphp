# How it works #

Sources for this example are here [GWTPHP-Helloworld](http://sourceforge.net/project/showfiles.php?group_id=207256).


## Serialization of remote calls in GWT way ##

Before we start I will shortly write about GWT remote procedures call (RPC) serialization. When you try to call remote procedure from GWT application, GWT serialize information about it and send it to server.

GWTRPC serialized data contains:
  * service interface name e.g.: pl.rmalinowski.gwtphp.helloworld.client.MainService
  * called method name with parameters list e.g.: login(java.lang.String,java.lang.String)
  * parameters values e.g.: "nick", "password"
  * class serialization signatures e.g.:for java.util.Date class are 1659716317

More about class serialization signatures:

> Each class used in serialized call, has to have serialization signature, which ensure that both of client and server works on instances of the same class. (for example ). When we change class definition the serialization signature will change too. Signature value are computed by GWT compiler in compilation time of your application for each of class that implements IsSerializable (and now Serializable) interface. If we have compiled GWT application and after that change class definition on server side and we compile only server application, we will have two different signatures for one class. In that case if we call remote procedure from GWT application it will throw SerializationException. This topic is important because GWTPHP cannot compute serialization signature, and you must yourself set appropriate serialization signatures for your classes. (How to do this I will describe later)


## PHP to Java mapping ##

PHP are not strongly typed programming language, and when we are using GWTPHP on server side of your application, you must prepare some additional information for GWTPHP. This information will tell GWTPHP how to convert Java classes to PHP classes, Java primitive values to PHP primitive values and so on. We will create gwtphpmap file for each of your classes that you wont to share with GWT application. Thanks to this map GWTPHP will be able to find which services and which method should call on RPC message receive and we will able to sets serialization signatures for our custom classes.


## Typical GWT RPC scenario ##

Now i will show what is going on when GWT call GWTPHP.
In typical GWT remote call scenario we have following steps:

**Step 1.** GWT send serialized data to server. (see previous section: _GWTRPC serialized data contains_)

**Step 2.** GWTPHP deserialize data :) let say that GWT is trying to call _login_ method of _pl.rmalinowski.gwtphp.helloworld.client.MainService_.

**Step 3.** GWTPHP look for _MainService.gwtphpmap.inc.php_ file in _gwt-maps/pl/rmalinowski/gwtphp/helloworld/client_ folder. If not found _ClassMapNotFoundException_ will thrown. If found will look into file and try to find description for type: _pl.rmalinowski.gwtphp.helloworld.client.MainService_.

_MainService.gwtphpmap.inc.php_ file could look like this:
```
<?php
$gwtphpmap = array (   
   array( 
      'className' => 'pl.rmalinowski.gwtphp.helloworld.client.MainService' , //Java 
      'mappedBy' => 'pl.rmalinowski.gwtphp.helloworld.client.MainService' , // PHP   
      'methods' => array (
         array(
            'name' => 'login',
            'mappedName'=>'login',
            'returnType' =>  'pl.rmalinowski.gwtphp.helloworld.client.dto.User',
            'params' => array(
               array(
                  'type'=>'java.lang.String'
               ) ,
               array(
                  'type'=>'java.lang.String'
               ) 
            ) ,
            'throws' => array(   
               array(
                  'type'=>'pl.rmalinowski.gwtphp.helloworld.client.dto.AuthorizationException'
               ) 
            )

      //(...) cut for clarity 

         )
   );

?>
```

**Step 4.** GWTPHP found  className and mappedBy values in array and now will look for  _gwt-maps/pl/rmalinowski/gwtphp/helloworld/client/MainService.class.php_ file where should be an implementation of _MainService_ class. If not found _ClassNotFoundException_ will thrown.

_MainService.class.php_ file could look like this:
```
<?php
require_once(dirname(__FILE__).'/dto/User.class.php');
require_once(dirname(__FILE__).'/dto/AuthorizationException.class.php');
class MainService {
			
	public function login($login, $passwd) {
		if ($login == $passwd && $login == 'gwtphp') {
			$user = new User();
			$user->setId(123);
			$user->setLogin("gwtphp");
			$user->setName("Rafal Malinowski");
			//$user->setBirthdate((microtime ( true ))*1000);
			$user->setBirthdate((mktime (11,23,34,4,27,1996,0))*1000);
			return $user;
		}
		else 
		throw new AuthorizationException("Login or password not correct! Try gwtphp :)");
		
	}

//(...) cut for clarity 
		
}

?>
```
**Step 5.** GWTPHP using  gwtphpmap will try to find appropriate method to invoke. If not found _NoSuchMethodException_ will thrown. If found will invoke and serialize result. Serialization is possible because of returnType information occurred in gwtphpmap array (_'returnType' =>  'pl.rmalinowski.gwtphp.helloworld.client.dto.User'_)

**Step 6.** While process of serialization _User_ class, GWTPHP will look for  _User.gwtphpmap.inc.php_ file in folder  _gwt-maps/pl/rmalinowski/gwtphp/helloworld/client/dto_.

_User.gwtphpmap.inc.php_ file could look like this:
```
<?php

$gwtphpmap = array( 
            'className' => 'pl.rmalinowski.gwtphp.helloworld.client.dto.User' ,
            'mappedBy' => 'pl.rmalinowski.gwtphp.helloworld.client.dto.User' ,
            'typeCRC' => '1390835995',
            'fields' => array (
                        array (
                           'name' => 'id',
                           'type'=>'I'
                        ),
                        array (
                           'name' => 'name',
                           'type'=>'java.lang.String',
                        ),
                        array (
                           'name' => 'login',
                           'type'=>'java.lang.String',
                        ),
                        array (
                           'name' => 'birthdate',
                           'type'=>'java.util.Date'
                        )

                  )

         );

?>
```
**Step 7.** Serialization of instance of User class is possible because of _typeCRC_ information occurred in gwtphpmap ('typeCRC' => '1390835995'), and because gwtphpmap specify completely list of serializable fields in User class. Note that User serializable fields  must have exactly the same names in PHP class and Java class, and number of fields must equals. If not you will have SerializationException after remote procedure call.

**Step 8.** GWTPHP return to GWT serialized response.

Next steps are like in typical GWT async service call scenario.