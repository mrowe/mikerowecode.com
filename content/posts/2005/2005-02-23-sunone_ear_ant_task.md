---
layout: post
title: An Ant task for sun app server ear verification
date: "2005-02-23"
url: "/2005/02/sunone_ear_ant_task.html"
---

Here is an ant target that worked at least once to verify an EAR for
deployment on SunONE...

    <!--
        properties for SunONE app server deployment (can be overridden
        in local properties, otherwise use dev box by default)
    -->
    <property name="deploy.sunone.home" value="/usr/local/sunas8"/>
    <property name="deploy.sunone.asadmin" value="${deploy.sunone.home}/bin/asadmin"/>

    <property name="deploy.admin.user" value="admin"/>
    <property name="deploy.admin.password" value="welcome1"/>
    <property name="deploy.admin.host" value="c3app2002d"/>
    <property name="deploy.admin.port" value="4884"/>
    <property name="deploy.sunone.instance" value="ucm-dev"/>

    <path id="deploy.sunone.path">
        <pathelement location="${deploy.sunone.home}/lib/dom.jar"/>
        <pathelement location="${deploy.sunone.home}/lib/xalan.jar"/>
        <pathelement location="${deploy.sunone.home}/lib/xercesImpl.jar"/>
        <pathelement location="${deploy.sunone.home}/lib/appserv-ideplugin.jar"/>
        <pathelement location="${deploy.sunone.home}/lib/appserv-rt.jar"/>
        <pathelement location="${java.home}/lib/tools.jar"/>
        <pathelement location="${deploy.sunone.home}/lib/j2ee.jar"/>
        <pathelement location="${deploy.sunone.home}/lib/appserv-ext.jar"/>
        <pathelement location="${deploy.sunone.home}/lib/appserv-cmp.jar"/>
        <pathelement location="${deploy.sunone.home}/lib/appserv-admin.jar"/>
    </path>

    <target name="verify-ear" depends="ear">
        <mkdir dir="${build}/doc"/>
        <java classname="com.sun.enterprise.tools.verifier.Verifier"
              fork="yes" failonerror="true">
            <env key="LD_LIBRARY_PATH" path="${deploy.sunone.home}/lib"/>
            <sysproperty key="java.ext.dirs" 
                         path="${java.home}/jre/lib/ext"/>
            <sysproperty key="com.sun.enterprise.home"
                         value="${deploy.sunone.home}"/>
            <sysproperty key="com.sun.aas.verifier.xsl"
                         value="${deploy.sunone.home}/lib/verifier" />
            <sysproperty key="com.sun.aas.installRoot" 
                         value="${deploy.sunone.home}" />

            <arg line="--destdir ${build}/doc"/>
            <arg line="--reportlevel warnings"/>
            <arg line="-C ${deploy.sunone.home}/lib/verifier"/>
            <arg value="${build}/ucmnetworkserver.ear" />
            <classpath refid="deps"/>
            <classpath refid="deploy.sunone.path"/>
        </java>
        <!--
        <exec executable="${deploy.sunone.home}/bin/verifier"
              timeout="120000">
            <arg value="${build}/ucmnetworkserver.ear" />
        </exec>
        -->
    </target>
