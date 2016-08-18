---
published: false
---

## TomEE and JNDI lookup

If you try to JNDI lookup a Bean, it will find the Bean by name.  If an instance of the Proxy for that Bean already exists, but it's for a different Bean, it will "undeploy" that other bean - giving you the error message: 

	java.lang.IllegalStateException: Bean 'MyEJB' has been undeployed.

The problem is when you have two Beans with the same name.  This might not be your fault, but could be due to the way openejb in TomEE doesn't use the "mappedName" attribute from EJBs.  Because it ignores the mappedName attribute, it only uses the name attribute, e.g.

	@Stateless( name = "MyEJB", mappedName = "ejb/com/amazon/nonfiction/MyEJB" )

In this situation, TomEE stores the EJB as "MyEJB".

If you had another class with a different EJB annotation:

	@Stateless( name = "MyEJB", mappedName = "ejb/com/amazon/fiction/MyEJB" )

TomEE also stores the EJB with the name "MyEJB".

Even though the two classes are different, with different mappedNames, they are stored under the same name.

When you try to lookup an instance of the EJB, e.g.

	initialContext.lookup("java:/global/my-fiction-war/MyEJB")

TomEE seems to look up the name, and finds the details of the bean.  It checks if there's an instance with the same "name" already.  If there's already an instance, but of a different type, it will "undeploy" the existing instance, and create a new instance of the correct type.  And this is when you see:

	java.lang.IllegalStateException: Bean 'MyEJB' has been undeployed.

## The solution?

...prevent the clash from happening.

In the file `apache installation>/conf/system.properties` you can customize how TomEE/openejb stores the names of the instances internally:

	openejb.deploymentId.format={host}/{appId}/{ejbJarId}/{ejbName}

When this is used, when OpenEJB looks up MyEJB it doesn't clash with the other MyEJB, and so they both work happily side by side.
