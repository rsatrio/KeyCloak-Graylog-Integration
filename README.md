## Keycloak Logging to Graylog (or other Syslog server)

Below are steps to sending keycloak's event logging to graylog:

1. Setup syslog as one of graylog's input. Note the IP Address port, and protocol (TCP/UDP) for the syslog input

   https://www.graylog.org/post/how-to-use-graylog-as-a-syslog-server

2. Activate Event Logging in Keycloak's realm from its admin console:

   https://www.keycloak.org/docs/latest/server_admin/index.html#auditing-and-events

3. Open $KEYCLOAK_HOME/standalone/configuration/standalone.xml

4.  Add below line under <subsystem xmlns="urn:jboss:domain:logging>. Adjust the value for serverHostname, port, and protocol. In example below, we are using TCP as the syslog protocol. Usually the default for syslog is using UDP.

   ```xml
   <custom-handler name="graylog" class="org.jboss.logmanager.handlers.SyslogHandler" module="org.jboss.logmanager">
                   <level name="DEBUG"/>
                   <formatter>
                       <pattern-formatter pattern="%-5p [%c] (%t) %s%E%n"/>
                   </formatter>
                   <properties>
                       <property name="serverHostname" value="localhost"/>
                       <property name="appName" value="keycloak"/>
                       <property name="syslogType" value="RFC5424"/>
                       <property name="port" value="514"/>
                       <property name="protocol" value="TCP"/>
                   </properties>
               </custom-handler>

    <logger category="org.keycloak.events">
                   <level name="DEBUG"/>
                   <handlers>
                       <handler name="graylog"/>
                   </handlers>
               </logger>
   ```

5. Start keycloak and check logging.properties under standalone's configuration folder($KEYCLOAK_HOME/standalone/configuration/logging.properties), the configuration should now contain handler with name graylog

6. Try to login to one of the keycloak's client (i.e account) using false username/password. There should be log(s) sent to graylog by keycloak

   
