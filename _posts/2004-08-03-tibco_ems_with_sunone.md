---
layout: post
title: Using Tibco EMS with SunONE AS 7
---

If for some reason you are unfortunate enough to have to use SunONE
Advanced Server 7 (variously known as as7se, SunONE AS7, Sun Java
System Advanced Server, etc.) and have to integrate it with Tibco's
JMS server (Tibco EMS), here is what you need to do.

### App server configuration

If for some reason you are unfortunate enough to have to use SunONE
Advanced Server 7 (variously known as as7se, SunONE AS7, Sun Java
System Advanced Server, etc.) and have to integrate it with Tibco's
JMS server (Tibco EMS), here is what you need to do.

### App server configuration

Put the Tibco client JARs (jndi.jar, jms.jar and tibjms.jar) in
`$SUNONE/share/lib`. Add them to the server's classpath. In the server
admin console, navigate to:

 * Server/JVM Settings/Path Settings/Class path suffix

Or directly in server.xml:

    <server ...>
      ...
      <java-config ... classpath-suffix="C:\Sun\AppServer7\share\lib\jndi.jar;C:\Sun\AppServer7\share\lib\jms.jar;C:\Sun\AppServer7\share\lib\tibjms.jar" ...

Configure an "external JNDI" resource for the Queue Connection Factory:

 * Server/JNDI/External Resources

and set:

 * Resource Type: javax.naming.InitialContext
 * JNDI Lookup: 	 QueueConnectionFactory
 * Factoryclass:  com.tibco.tibjms.naming.TibjmsInitialContextFactory

and add a property:

 * java.naming.provider.url = tibjmsnaming://localhost:7222

Configure an "external JNDI" resource for the Queue:

 * Resource Type: javax.jms.Queue
 * JNDI Lookup:   queue.sample
 * Factoryclass:  com.tibco.tibjms.naming.TibjmsInitialContextFactory

and add a property:

    java.naming.provider.url = tibjmsnaming://localhost:7222

If the initial JNDI lookup requires a username/password, this can be
passed in by setting two additional properties:

 * java.naming.security.principal = username
 * java.naming.security.credentials = password

These can also be defined directly in server.xml:

    <external-jndi-resource 
        enabled="true" 
    	jndi-name="jms/tibco/QueueConnectionFactory" 
    	res-type="javax.naming.InitialContext" 
    	factory-class="com.tibco.tibjms.naming.TibjmsInitialContextFactory" 
    	jndi-lookup-name="QueueConnectionFactory">
    
      <property 
    	name="java.naming.provider.url"	
    	value="tibjmsnaming://localhost:7222">
    
    </external-jndi-resource>
    
    <external-jndi-resource 
        enabled="true" 
    	jndi-name="jms/tibco/queue.sample" 
    	res-type="javax.jms.Queue" 
    	factory-class="com.tibco.tibjms.naming.TibjmsInitialContextFactory" 
    	jndi-lookup-name="queue.sample">
    
      <property 
    	name="java.naming.provider.url"	
    	value="tibjmsnaming://localhost:7222">
    
    </external-jndi-resource>

Additional queue (and topic) resources can be added as required.

### Bean Deployment

No changes are required to MDB code to support the Tibco provider. The
deployment descriptor must be configured to use the external JNDI
resources defined above.

In `sun-ejb-jar.xml`, the `mdb-connection-factory` should refer to the
Queue connection factory defined above. Also, a resource reference
should be defined refering to the factory. For example:

    <pre>
    <sun-ejb-jar>
       <enterprise-beans>
          <ejb>
             <ejb-name>TestBean</ejb-name>
             <jndi-name>jms/tibco/queue.sample</jndi-name>
    		 <resource-ref>
                <res-ref-name>jms/QueueConnectionFactory</res-ref-name>
                <jndi-name>jms/tibco/QueueConnectionFactory</jndi-name>
             </resource-ref>
             <mdb-connection-factory>
                <jndi-name>jms/tibco/QueueConnectionFactory</jndi-name>
                <default-resource-principal>
                   <name>sunone</name>
                   <password>guest</password>
                </default-resource-principal>
             </mdb-connection-factory>
          </ejb>
       </enterprise-beans>
    </sun-ejb-jar> 
    </pre>

In `ejb-jar.xml`, make sure the transaction type is "Bean" (see
Transactions below). The resource reference should refer to the
resource defined in `sun-ejb-jar.xml` above. Note that the
"res-ref-name" in these two files is independant of the JNDI name of
the actual resource (that is, they must only match each other).

    <pre>
    <ejb-jar>
       <enterprise-beans>
          <message-driven>
             <ejb-name>TestBean</ejb-name>
             <ejb-class>au.com.sensis.c3.cca.connectors.ejb.TestBean</ejb-class>
             <transaction-type>Bean</transaction-type>
             <message-driven-destination>
                <destination-type>javax.jms.Queue</destination-type>
             </message-driven-destination>
             <resource-ref>
                <res-ref-name>jms/QueueConnectionFactory</res-ref-name>
                <res-type>javax.jms.QueueConnectionFactory</res-type>
                <res-auth>Container</res-auth>
             </resource-ref>
          </message-driven>
       </enterprise-beans>
    </ejb-jar>
    </pre>

### Transactions

From: [swforum.sun.com](http://swforum.sun.com/jive/thread.jspa?threadID=15101&messageID=29541)

> Sun ONE Application Server 7 does not support CMT MDBs with 3rd party
> JMS providers. (As you note, BMT MDBs work with MQSeries and Sun ONE
> App Server).

As stated, bean-managed transactions appear to work fine (see sample code below).

### Sample Bean

A sample implementation of a minimal message-driven bean that was
tested with SunONE and Tibco EMS:

    <pre>
    package au.com.sensis.c3.cca.connectors.ejb;
    
    import javax.ejb.EJBException;
    import javax.ejb.MessageDrivenBean;
    import javax.ejb.MessageDrivenContext;
    import javax.jms.Message;
    import javax.jms.MessageListener;
    import javax.jms.TextMessage;
    import javax.transaction.SystemException;
    import javax.transaction.UserTransaction;
    
    
    /**
     * A simple MDB that listens for messages and mentions the fact if it
     * receives one.
     *
     * @author <a href="mailto:mrowe@mojain.com">Michael Rowe </a>
     */
    public class TestBean
        implements MessageDrivenBean, MessageListener
    {
    
        protected static org.apache.commons.logging.Log _log =
            org.apache.commons.logging.LogFactory.getLog(TestBean.class);
    
    	protected MessageDrivenContext _context;
    
        public TestBean()
        {
        }
    
        public void onMessage(Message message)
        {
    
            UserTransaction ut = _context.getUserTransaction();
    
            if (message instanceof TextMessage)
            {
                String doc = null;
                try
                {
    				ut.begin();
                    TextMessage text = (TextMessage) message;
    				doc = text.getText();
    				ut.commit();
                 }
                 catch (Exception e)
                 {
    				try
    				{
    					_log.warn("rolling back transaction");
    					ut.rollback();
    				}
    				catch (SystemException se)
    				{
    					throw new EJBException("Rollback failed: " + se.getMessage());
    				}
                    _log.error("Can't get text", e);
                 }
                _log.info("Got message: " + doc);
            }
            else
            {
                _log.warn("Message received, but it wasn't a TextMessage");
            }
    
        } // onMessage()
    
        public void ejbCreate()
        {
        }
    
        public void ejbRemove()
        {
        }
    
        public void setMessageDrivenContext(MessageDrivenContext context)
                throws EJBException
        {
    		_context = context;
        }
    
    }

### SunONE mdb concurrency notes

Default pool settings allow mutiple beans to be created. They appear
to be created (constructor/ejbCreate()) on demand, but then stay in
the pool.

The `onMessage()` calls run on multiple threads, but there appears to be
reuse (i.e. threads that have finished executing an `onMessage()` will
be used again).

Setting the pool to have a max of one bean (`bean-pool/max-pool-size`)
causes messages to be consumed serially by one instance of the bean,
in one thread (as expected).

And interestingly (although not all that relevant), all the beans seem
to get constructed by the same thread, and that thread doesn't do any
message servicing. But there does seem to be a 1:1 relationship
between bean instances and message servicing threads (although this is
with only six beans in the pool, it could be different with lots
more).

If there is only one bean in the pool, it is still constructed on a
different thread to that on which is processes messages.
